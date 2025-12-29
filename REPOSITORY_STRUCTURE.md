# KHAN-MD Repository Structure Documentation

## Overview
KHAN-MD is a WhatsApp bot built on the Baileys library (WhatsApp Web API). This document provides a comprehensive overview of the repository structure, architecture, and data flow.

---

## Repository Structure

```
ModKHAN-MD/
├── .git/                    # Git version control
├── .gitignore              # Git ignore patterns
├── Dockerfile              # Docker container configuration
├── LICENSE                 # Apache 2.0 License
├── README.md               # Project documentation
├── SECURITY.md             # Security policy
├── app.json                # Heroku/deployment configuration
├── index.js                # Main entry point (currently minimal)
├── package.json            # Node.js dependencies
└── settings.js             # Bot configuration settings
```

---

## Core Files Analysis

### 1. **index.js** (Main Entry Point)
- **Current Status**: Contains only a comment: "Deploy From JawadTechXD repo not this *"
- **Purpose**: This should be the main application entry point
- **Note**: The actual bot logic appears to be in a different repository (JawadTechXD/KHAN-XD)

### 2. **settings.js** (Configuration)
```javascript
{
  SESSION_ID: "",           // WhatsApp session authentication
  OWNER_NUMBER: "",         // Bot owner's phone number
  PREFIX: ".",              // Command prefix
  TIMEZONE: "Asia/Karachi"  // Bot timezone
}
```

### 3. **package.json** (Dependencies)
- **Runtime**: Node.js
- **Process Manager**: PM2
- **Main Dependencies**:
  - `@whiskeysockets/baileys@7.0.0-rc.9` - WhatsApp Web API
  - `express@5.1.0` - Web server
  - `axios@1.7.9` - HTTP client
  - Various media processing libraries (ffmpeg, jimp, etc.)

### 4. **app.json** (Deployment Configuration)
- Contains environment variables for various bot features
- Configures Heroku deployment settings
- Defines buildpacks and stack version

### 5. **Dockerfile**
- Based on `node:lts-buster`
- Installs dependencies and PM2
- Exposes port 9090
- Runs `npm start` command

---

## Architecture Overview

### Component Breakdown

1. **WhatsApp Connection Layer**
   - Uses Baileys library for WhatsApp Web protocol
   - Handles session management via SESSION_ID
   - Manages multi-device connections

2. **Command Processing**
   - Prefix-based command system (default: ".")
   - Bot responds to specific command patterns
   - Supports multiple command categories

3. **Media Processing**
   - FFmpeg for video/audio conversion
   - Jimp for image manipulation
   - Sticker creation capabilities

4. **External Integrations**
   - YouTube downloading (@dark-yasiya/yt-dl.js)
   - Google Translate API
   - Google TTS (Text-to-Speech)
   - Web scraping (axios, cheerio, jsdom)

5. **Storage & Persistence**
   - SQLite3 for local database
   - Sequelize ORM for database management
   - File system operations (fs-extra)

6. **Web Interface**
   - Express server on port 9090
   - QR code generation for pairing
   - Web-based session management

---

## Deployment Architecture

### Supported Platforms
1. **Heroku** - Cloud platform deployment
2. **Railway** - Modern cloud deployment
3. **Render** - Web service deployment
4. **Replit** - Development environment
5. **Koyeb** - Serverless deployment
6. **Netlify** - Web hosting
7. **VPS** - Custom server deployment
8. **Docker** - Containerized deployment

### Deployment Flow
```
User → Pairing Site → SESSION_ID → Deploy Platform → Bot Instance → WhatsApp
```

---

## Feature Modules (from app.json)

1. **Anti-Features**
   - `ANTI_DELETE`: Restore deleted messages
   - `ANTI_LINK`: Remove links in groups
   - `ANTI_BAD`: Filter bad words
   - `ANTI_MENTION`: Control mentions
   - `ANTI_STATUS_MENTION`: Control status mentions

2. **Auto-Features**
   - `AUTO_REPLY`: Automatic message responses
   - `AUTO_STICKER`: Auto-convert images to stickers
   - `AUTO_STATUS_SEEN`: Auto-view statuses
   - `AUTO_STATUS_REACT`: Auto-react to statuses
   - `AUTO_TYPING`: Show typing indicator
   - `AUTO_RECORDING`: Show recording indicator
   - `AUTO_DOWNLOADER`: Auto-download media from links
   - `AUTO_REACT`: React to all messages
   - `ALWAYS_ONLINE`: Keep bot online status

3. **Group Management**
   - `ADMIN_ACTION`: Track admin actions
   - `WELCOME`: Welcome new members
   - Group settings control

4. **Customization**
   - Custom reactions with emojis
   - Custom sticker pack name
   - Custom menu images
   - Bot mode (public/private/inbox/group)

---

## Bot Modes

- **Public**: Responds to all users
- **Private**: Responds only to owner
- **Inbox**: Responds only in private chats
- **Group**: Responds only in groups

---

## Process Management

### PM2 Configuration
```bash
npm start    # Start bot with PM2
npm stop     # Stop bot
npm restart  # Restart bot
```

PM2 provides:
- Automatic restarts on crashes
- Process monitoring
- Log management
- Load balancing

---

## Important Notes

⚠️ **Repository Status**: This repository appears to be a configuration/deployment template. The actual bot implementation is referenced in another repository (JawadTechXD/KHAN-XD).

📌 **Key Observation**: The `index.js` file is essentially empty, containing only a redirect comment to the actual source repository.

🔗 **External Dependencies**: 
- Pairing sites: `khanmd-pair.onrender.com` and `khanxmd-pair.onrender.com`
- VPS deployment: `jawadtech-vps.onrender.com`
- Main code repository: `github.com/JawadTechXD/KHAN-XD`

---

## Version Information

- **Current Version**: 5.0.0 (from package.json)
- **Deployment Version**: 9.0.5 (from README)
- **Baileys Version**: 7.0.0-rc.9
- **Node.js**: LTS version (from Dockerfile)

---

*Last Updated: 2025-12-29*
