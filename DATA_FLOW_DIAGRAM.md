# KHAN-MD Data Flow Diagram

This document provides visual representations of data flow within the KHAN-MD WhatsApp bot system.

---

## 1. High-Level System Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                         USER INTERACTIONS                        │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   WhatsApp User  ◄───► WhatsApp Groups  ◄───► Status Updates   │
│                                                                  │
└────────────────────────┬─────────────────────────────────────────┘
                         │
                         │ Messages, Media, Commands
                         ▼
┌──────────────────────────────────────────────────────────────────┐
│                      WHATSAPP SERVERS                            │
│                    (End-to-End Encrypted)                        │
└────────────────────────┬─────────────────────────────────────────┘
                         │
                         │ WhatsApp Web Protocol
                         ▼
┌──────────────────────────────────────────────────────────────────┐
│                    BAILEYS LIBRARY LAYER                         │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  - Protocol Handling                                      │  │
│  │  - Encryption/Decryption                                  │  │
│  │  - Session Management                                     │  │
│  │  - Multi-device Support                                   │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────┬─────────────────────────────────────────┘
                         │
                         │ Decoded Messages & Events
                         ▼
┌──────────────────────────────────────────────────────────────────┐
│                       BOT CORE LOGIC                             │
│                      (index.js + modules)                        │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Command Parser  →  Feature Router  →  Response Builder  │  │
│  └──────────────────────────────────────────────────────────┘  │
└─┬─────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┬────────┘
  │     │      │      │      │      │      │      │      │
  ▼     ▼      ▼      ▼      ▼      ▼      ▼      ▼      ▼
┌────┐┌────┐┌─────┐┌─────┐┌────┐┌─────┐┌────┐┌─────┐┌──────┐
│DB  ││File││Media││Trans││TTS ││Stick││Auto││Group││Web   │
│SQL ││Sys ││Down ││late ││API ││er   ││Feat││Mgmt ││Scrape│
└────┘└────┘└─────┘└─────┘└────┘└─────┘└────┘└─────┘└──────┘
  │     │      │      │      │      │      │      │      │
  └─────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┴────────┐
                                                                    │
                         External APIs & Services                  │
                                                                    ▼
┌──────────────────────────────────────────────────────────────────┐
│  YouTube API  │  Google Translate  │  Google TTS  │  Web URLs   │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. Authentication & Session Flow

```
STEP 1: Initial Pairing
┌─────────────┐
│    User     │
│   Device    │
└──────┬──────┘
       │
       │ Visit pairing website
       ▼
┌────────────────────────┐
│  Pairing Site (External)│
│  - Generates QR Code   │
│  - or Pairing Code     │
└──────┬─────────────────┘
       │
       │ Scan QR / Enter Code
       ▼
┌──────────────────┐
│  WhatsApp App    │
│  (User's Phone)  │
└──────┬───────────┘
       │
       │ Links device
       ▼
┌─────────────────────┐
│  WhatsApp Servers   │
│  (Generates Auth)   │
└──────┬──────────────┘
       │
       │ SESSION_ID returned
       ▼
┌─────────────────────┐
│  Pairing Site       │
│  (Displays Session) │
└──────┬──────────────┘
       │
       │ User copies
       ▼

STEP 2: Configuration
┌─────────────────────┐
│   User manually     │
│   adds to:          │
│   - settings.js or  │
│   - .env file or    │
│   - app.json        │
└──────┬──────────────┘
       │
       ▼

STEP 3: Bot Startup
┌─────────────────────┐
│  Bot reads config   │
│  SESSION_ID loaded  │
└──────┬──────────────┘
       │
       │ Connect to WhatsApp
       ▼
┌─────────────────────┐
│  Baileys Library    │
│  - Validates session│
│  - Connects to WA   │
└──────┬──────────────┘
       │
       │ Authenticated
       ▼
┌─────────────────────┐
│  Bot is ONLINE      │
│  Ready to receive   │
└─────────────────────┘
```

---

## 3. Message Processing Flow

```
INCOMING MESSAGE
       │
       ▼
┌─────────────────────────────┐
│  WhatsApp Server            │
│  (E2E Encrypted)            │
└──────────┬──────────────────┘
           │
           │ Encrypted payload
           ▼
┌─────────────────────────────┐
│  Baileys Library            │
│  - Decrypt message          │
│  - Parse metadata           │
│  - Extract content          │
└──────────┬──────────────────┘
           │
           │ Decrypted message object
           ▼
┌─────────────────────────────┐
│  Message Handler            │
│  - Check sender             │
│  - Check chat type          │
│  - Check bot mode           │
└──────────┬──────────────────┘
           │
           │ Validate permissions
           ▼
     ┌────┴────┐
     │ Is Bot  │
     │ Allowed?│
     └─┬─────┬─┘
       │     │
    NO │     │ YES
       │     │
       ▼     ▼
    IGNORE  ┌─────────────────────┐
            │  Command Detector   │
            │  - Check prefix     │
            │  - Parse command    │
            └──────┬──────────────┘
                   │
                   ▼
             ┌────┴────┐
             │Is Valid │
             │Command? │
             └─┬─────┬─┘
               │     │
            NO │     │ YES
               │     │
               ▼     ▼
       ┌────────┐  ┌──────────────────┐
       │Auto    │  │Command Processor │
       │Features│  │- Execute handler │
       │Active? │  │- Get response    │
       └────────┘  └────────┬─────────┘
               │            │
               └────┬───────┘
                    │
                    ▼
       ┌────────────────────────────┐
       │  Response Builder          │
       │  - Format message          │
       │  - Attach media            │
       │  - Add reactions           │
       └──────────┬─────────────────┘
                  │
                  │ Response ready
                  ▼
       ┌────────────────────────────┐
       │  Baileys Send              │
       │  - Encrypt message         │
       │  - Add metadata            │
       └──────────┬─────────────────┘
                  │
                  │ Encrypted payload
                  ▼
       ┌────────────────────────────┐
       │  WhatsApp Server           │
       │  (Deliver to recipient)    │
       └────────────────────────────┘
```

---

## 4. Feature Module Data Flow

### A. Media Download Flow
```
User sends: ".play song name"
       │
       ▼
Command Parser extracts: "play" + "song name"
       │
       ▼
┌──────────────────┐
│ YouTube Search   │
│ (yt-search pkg)  │
└────────┬─────────┘
         │ Video URL found
         ▼
┌──────────────────┐
│ YouTube Download │
│ (@dark-yasiya)   │
└────────┬─────────┘
         │ Audio file
         ▼
┌──────────────────┐
│ FFmpeg Process   │
│ (Convert format) │
└────────┬─────────┘
         │ Processed audio
         ▼
┌──────────────────┐
│ Upload to WA     │
│ (via Baileys)    │
└──────────────────┘
```

### B. Sticker Creation Flow
```
User sends image
       │
       ▼
┌──────────────────┐
│ Download Image   │
│ (from WhatsApp)  │
└────────┬─────────┘
         │ Image buffer
         ▼
┌──────────────────┐
│ Image Processing │
│ (Jimp/Sharp)     │
└────────┬─────────┘
         │ Resized/cropped
         ▼
┌──────────────────┐
│ Sticker Formatter│
│ (wa-sticker-fmt) │
└────────┬─────────┘
         │ WebP format
         ▼
┌──────────────────┐
│ Add Metadata     │
│ (pack name/auth) │
└────────┬─────────┘
         │ Sticker ready
         ▼
┌──────────────────┐
│ Send Sticker     │
│ (via Baileys)    │
└──────────────────┘
```

### C. Translation Flow
```
User: ".translate Hello to Urdu"
       │
       ▼
Parse: target="Urdu", text="Hello"
       │
       ▼
┌──────────────────────────┐
│ Google Translate API     │
│ (@vitalets/google-trans) │
└────────┬─────────────────┘
         │ Translated text
         ▼
Format: "Hello → ہیلو"
         │
         ▼
Send response to user
```

### D. Auto-Status-View Flow
```
Status Update Posted
       │
       ▼
┌─────────────────────┐
│ Baileys detects     │
│ status update event │
└──────┬──────────────┘
       │
       ▼
Check: AUTO_STATUS_SEEN = true?
       │
       │ YES
       ▼
┌─────────────────────┐
│ Mark status as seen │
│ (via Baileys)       │
└──────┬──────────────┘
       │
       ▼
Check: AUTO_STATUS_REACT = true?
       │
       │ YES
       ▼
┌─────────────────────┐
│ Send reaction emoji │
│ (random from list)  │
└──────┬──────────────┘
       │
       ▼
Check: AUTO_STATUS_REPLY = true?
       │
       │ YES
       ▼
┌─────────────────────┐
│ Send reply message  │
│ (AUTO_STATUS_MSG)   │
└─────────────────────┘
```

---

## 5. Database Interaction Flow

```
Bot Operation
       │
       ▼
┌─────────────────────────┐
│  Need Data?             │
│  - User settings        │
│  - Group config         │
│  - Anti-delete cache    │
└──────┬──────────────────┘
       │
       ▼
┌─────────────────────────┐
│  Sequelize ORM          │
│  (Query builder)        │
└──────┬──────────────────┘
       │
       │ SQL Query
       ▼
┌─────────────────────────┐
│  SQLite3 Database       │
│  (Local file)           │
└──────┬──────────────────┘
       │
       │ Result set
       ▼
┌─────────────────────────┐
│  Process data           │
│  Return to bot logic    │
└─────────────────────────┘

TABLES (Likely):
┌──────────────┐
│   users      │
├──────────────┤
│ - phone      │
│ - name       │
│ - banned     │
└──────────────┘

┌──────────────┐
│   groups     │
├──────────────┤
│ - jid        │
│ - settings   │
│ - welcome    │
└──────────────┘

┌──────────────┐
│   messages   │
├──────────────┤
│ - id         │
│ - content    │
│ - deleted    │
└──────────────┘
```

---

## 6. Anti-Delete Feature Flow

```
Message Sent in Group
       │
       ▼
┌─────────────────────────┐
│ Bot receives & stores   │
│ - Message content       │
│ - Media (if any)        │
│ - Metadata              │
└──────┬──────────────────┘
       │
       │ Store in cache/DB
       ▼
┌─────────────────────────┐
│ SQLite3 / Memory Cache  │
└─────────────────────────┘
       │
       │ Someone deletes message
       ▼
┌─────────────────────────┐
│ Baileys detects         │
│ "message deleted" event │
└──────┬──────────────────┘
       │
       ▼
Check: ANTI_DELETE = true?
       │
       │ YES
       ▼
┌─────────────────────────┐
│ Retrieve from cache     │
│ - Original message      │
│ - Original media        │
│ - Sender info           │
└──────┬──────────────────┘
       │
       ▼
Check: ANTI_DELETE_PATH
       │
       ├─── "same" → Send to same chat
       │
       └─── "inbox" → Send to bot owner
```

---

## 7. Security Data Flow (Concerns)

```
POTENTIAL DATA LEAKAGE POINTS:

1. Session Storage
   settings.js (Plain Text) 🔴
   └─ Contains: SESSION_ID
   
2. Environment Variables
   .env file
   └─ If logged or exposed 🟡
   
3. PM2 Logs
   logs/app.log
   └─ May contain: Messages, Numbers 🟡
   
4. Database File
   database.sqlite
   └─ Unencrypted at rest 🟡
   
5. Temp Files
   /tmp/downloads
   └─ Downloaded media 🟢
   
6. External Services
   ├─ Pairing Sites 🔴
   ├─ Google APIs 🟢
   ├─ YouTube APIs 🟢
   └─ Web Scraping 🟡

ENCRYPTION STATUS:
✅ WhatsApp Messages (E2E encrypted by WA)
❌ Database (No encryption)
❌ Session file (No encryption)
❌ Logs (Plain text)
❌ Config files (Plain text)
```

---

## 8. Express Server Routes (Theoretical)

```
Client Request
       │
       ▼
┌─────────────────────────┐
│  Express Server :9090   │
└──────┬──────────────────┘
       │
       ▼
┌──────────────────────────────────────┐
│  Routing (Likely routes)             │
├──────────────────────────────────────┤
│                                      │
│  GET  /          → Dashboard         │
│  GET  /health    → Health check      │
│  GET  /qr        → QR Code display   │
│  POST /webhook   → External webhooks │
│  GET  /status    → Bot status        │
│                                      │
└──────┬───────────────────────────────┘
       │
       ▼
Response to Client
```

---

## 9. Deployment Data Flow

```
DEVELOPER                    DEPLOYMENT PLATFORM           RUNTIME
    │                                │                        │
    │ 1. Push code                   │                        │
    │ ─────────────────────────────> │                        │
    │                                │                        │
    │                                │ 2. Pull from GitHub    │
    │                                │ <──────────────────────┤
    │                                │                        │
    │                                │ 3. npm install         │
    │                                │ ───────────────────────│
    │                                │                        │
    │                                │ 4. Build Docker image  │
    │                                │ ───────────────────────│
    │                                │                        │
    │ 5. Set env variables           │                        │
    │ ─────────────────────────────> │                        │
    │    (SESSION_ID, etc.)          │                        │
    │                                │                        │
    │                                │ 6. Start container     │
    │                                │ ───────────────────────│
    │                                │                        │
    │                                │                        │ 7. npm start (PM2)
    │                                │                        │ ─────────────────>
    │                                │                        │
    │                                │                        │ 8. Connect WhatsApp
    │                                │                        │ ═══════════════════>
    │                                │                        │     (Baileys)
    │                                │                        │
    │                                │                        │ 9. Bot ONLINE
    │                                │                        │
```

---

## 10. Complete System Context

```
                     ┌──────────────────────────────────────┐
                     │         EXTERNAL WORLD               │
                     ├──────────────────────────────────────┤
                     │                                      │
                     │  • WhatsApp Users (Billions)         │
                     │  • WhatsApp Groups                   │
                     │  • Status Updates                    │
                     │  • Media Content                     │
                     │                                      │
                     └────────────┬─────────────────────────┘
                                  │
                                  │ Mobile App / Web
                                  ▼
                     ┌──────────────────────────────────────┐
                     │      WHATSAPP INFRASTRUCTURE         │
                     ├──────────────────────────────────────┤
                     │  • WhatsApp Servers (Global)         │
                     │  • End-to-End Encryption             │
                     │  • Multi-Device Protocol             │
                     │  • Media Servers                     │
                     └────────────┬─────────────────────────┘
                                  │
                                  │ WhatsApp Web Protocol
                                  ▼
┌────────────────────────────────────────────────────────────────────┐
│                          KHAN-MD BOT                               │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │                      CORE COMPONENTS                         ││
│  ├──────────────────────────────────────────────────────────────┤│
│  │                                                              ││
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     ││
│  │  │   Baileys    │  │   Express    │  │     PM2      │     ││
│  │  │   Library    │  │   Server     │  │   Manager    │     ││
│  │  └──────────────┘  └──────────────┘  └──────────────┘     ││
│  │                                                              ││
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     ││
│  │  │   Command    │  │   Feature    │  │   Database   │     ││
│  │  │   Parser     │  │   Modules    │  │   (SQLite)   │     ││
│  │  └──────────────┘  └──────────────┘  └──────────────┘     ││
│  │                                                              ││
│  └──────────────────────────────────────────────────────────────┘│
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │                    EXTERNAL INTEGRATIONS                     ││
│  ├──────────────────────────────────────────────────────────────┤│
│  │                                                              ││
│  │  • YouTube Download APIs                                    ││
│  │  • Google Translate Service                                 ││
│  │  • Google Text-to-Speech                                    ││
│  │  • Web Scraping (axios/cheerio)                            ││
│  │  • Media Processing (FFmpeg/Jimp)                          ││
│  │  • File Storage (MegaJS optional)                          ││
│  │                                                              ││
│  └──────────────────────────────────────────────────────────────┘│
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │                      CONFIGURATION                           ││
│  ├──────────────────────────────────────────────────────────────┤│
│  │                                                              ││
│  │  • settings.js (Session, Owner, Prefix)                     ││
│  │  • .env (Environment Variables)                             ││
│  │  • app.json (Feature Flags)                                 ││
│  │                                                              ││
│  └──────────────────────────────────────────────────────────────┘│
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ HTTP/HTTPS
                                  ▼
                     ┌──────────────────────────────────────┐
                     │       MONITORING & ADMIN             │
                     ├──────────────────────────────────────┤
                     │  • PM2 Dashboard                     │
                     │  • Express Web Interface             │
                     │  • Log Files                         │
                     │  • Health Checks                     │
                     └──────────────────────────────────────┘
```

---

## Summary of Data Touchpoints

| Data Type | Where It Goes | Security Level |
|-----------|--------------|----------------|
| Messages | WhatsApp ➔ Baileys ➔ Bot ➔ DB | 🟢 Encrypted in transit |
| Session ID | Config ➔ Baileys ➔ WhatsApp | 🔴 Plain text at rest |
| Media | WhatsApp ➔ Bot ➔ Temp Files | 🟡 Temporary storage |
| User Numbers | WhatsApp ➔ Bot ➔ DB | 🟡 No encryption |
| Commands | User ➔ Bot ➔ Logger | 🟡 May be logged |
| External Data | APIs ➔ Bot ➔ User | 🟡 Third-party trust |

---

*Data Flow Documentation - Last Updated: 2025-12-29*
