# KHAN-MD Setup Guide

This guide will help you set up and deploy the KHAN-MD WhatsApp bot safely and securely.

---

## 📋 Prerequisites

Before you begin, make sure you have:
- A WhatsApp account (separate from your personal account is recommended)
- A phone number that can receive SMS or calls
- Basic knowledge of environment variables
- Access to one of the supported deployment platforms

---

## ⚠️ Important Security Notice

**READ THIS FIRST:**

1. **Never share your SESSION_ID** with anyone
2. **Never commit `settings.js` or `.env`** files to GitHub
3. **Use a separate WhatsApp number** for the bot (not your personal number)
4. **Review the security analysis** in `SECURITY_ANALYSIS.md` before deploying
5. **The actual bot code is in a different repository** - This is a configuration template

---

## 🚀 Quick Start

### Step 1: Get Your Session ID

1. Visit one of the official pairing sites:
   - Short Session: https://khanmd-pair.onrender.com/
   - Long Session: https://khanxmd-pair.onrender.com/

2. **Security Warning:** These are external sites. Your session ID will be visible on your screen. Anyone with your session ID can control your WhatsApp account.

3. Follow the pairing process:
   - **QR Code Method:** Scan the QR code with your WhatsApp
   - **Pairing Code Method:** Enter the code in WhatsApp settings

4. Copy your SESSION_ID (it will be a long string)

### Step 2: Configure the Bot

**Option A: Using settings.js (Local deployment)**

1. Copy the example settings file:
   ```bash
   cp settings.example.js settings.js
   ```

2. Edit `settings.js` with your details:
   ```javascript
   module.exports = {
     SESSION_ID: "your_session_id_here",
     OWNER_NUMBER: "923001234567",  // Your number with country code
     PREFIX: ".",
     TIMEZONE: "Asia/Karachi"
   };
   ```

3. **IMPORTANT:** Never commit this file to git (it's in .gitignore)

**Option B: Using Environment Variables (Recommended for cloud deployment)**

Create a `.env` file:
```env
SESSION_ID=your_session_id_here
OWNER_NUMBER=923001234567
PREFIX=.
TIMEZONE=Asia/Karachi

# Optional features (see app.json for all options)
MODE=public
AUTO_STATUS_SEEN=true
AUTO_STATUS_REACT=true
ANTI_DELETE=true
```

### Step 3: Choose Deployment Method

---

## 🐳 Docker Deployment

### Prerequisites
- Docker installed on your system
- Docker Compose (optional)

### Steps

1. Clone the repository:
   ```bash
   git clone https://github.com/JawadYT36/KHAN-MD.git
   cd KHAN-MD
   ```

2. Create your `.env` file (see Step 2, Option B)

3. Build the Docker image:
   ```bash
   docker build -t khan-md .
   ```

4. Run the container:
   ```bash
   docker run -d \
     --name khan-md-bot \
     --env-file .env \
     -p 9090:9090 \
     --restart unless-stopped \
     khan-md
   ```

5. View logs:
   ```bash
   docker logs -f khan-md-bot
   ```

6. Stop the bot:
   ```bash
   docker stop khan-md-bot
   ```

---

## ☁️ Cloud Deployment (Heroku)

### Prerequisites
- Heroku account
- Heroku CLI installed (optional)

### Steps

1. Click the Deploy to Heroku button in README.md

2. Fill in the required environment variables:
   - `SESSION_ID`: Your session ID from pairing site
   - `OWNER_NUMBER`: Your phone number
   - Other optional variables (see Configuration section)

3. Click "Deploy App"

4. Wait for deployment to complete

5. Open your app and check logs

---

## 🚂 Railway Deployment

### Steps

1. Click the Deploy to Railway button in README.md

2. Connect your GitHub account

3. Select the repository

4. Add environment variables in Railway dashboard:
   - SESSION_ID
   - OWNER_NUMBER
   - Other optional configs

5. Deploy and monitor

---

## 🎨 Render Deployment

### Steps

1. Click Deploy to Render button in README.md

2. Create a new Web Service

3. Connect your GitHub repository

4. Add environment variables:
   - SESSION_ID
   - OWNER_NUMBER
   - All other configurations

5. Deploy

---

## 💻 Local/VPS Deployment

### Prerequisites
- Node.js v16 or higher
- npm or yarn
- PM2 (will be installed)

### Steps

1. Clone the repository:
   ```bash
   git clone https://github.com/JawadYT36/KHAN-MD.git
   cd KHAN-MD
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Configure settings (see Step 2 above)

4. Start the bot:
   ```bash
   npm start
   ```

5. Check PM2 status:
   ```bash
   pm2 status
   ```

6. View logs:
   ```bash
   pm2 logs KHAN-MD
   ```

7. Stop the bot:
   ```bash
   npm stop
   ```

8. Restart the bot:
   ```bash
   npm restart
   ```

---

## ⚙️ Configuration Options

All configuration options are defined in `app.json`. Here are the most important ones:

### Essential Settings

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| SESSION_ID | Your WhatsApp session | - | ✅ Yes |
| OWNER_NUMBER | Bot owner's number | - | ✅ Yes |
| PREFIX | Command prefix | . | No |
| BOT_NAME | Name of your bot | KHAN-MD | No |
| MODE | Bot operation mode | public | No |

### Mode Options
- `public`: Responds to everyone
- `private`: Only owner can use
- `inbox`: Only private chats
- `group`: Only group chats

### Auto Features

| Variable | Description | Values |
|----------|-------------|--------|
| ALWAYS_ONLINE | Keep online status | true/false |
| AUTO_TYPING | Show typing indicator | true/false/inbox/group |
| AUTO_RECORDING | Show recording indicator | true/false/inbox/group |
| AUTO_REACT | React to all messages | true/false |
| AUTO_REPLY | Automatic replies | true/false |
| AUTO_STICKER | Auto-convert to sticker | true/false |

### Status Features

| Variable | Description | Values |
|----------|-------------|--------|
| AUTO_STATUS_SEEN | Auto-view statuses | true/false |
| AUTO_STATUS_REACT | Auto-react to statuses | true/false |
| AUTO_STATUS_REPLY | Auto-reply to statuses | true/false |
| AUTO_STATUS_MSG | Custom status reply | text |

### Anti Features

| Variable | Description | Values |
|----------|-------------|--------|
| ANTI_DELETE | Show deleted messages | true/false |
| ANTI_DELETE_PATH | Where to send | same/inbox |
| ANTI_LINK | Remove links in groups | true/false/delete |
| ANTI_BAD | Filter bad words | true/false |
| ANTI_MENTION | Control mentions | true/false/warn/delete |

### Customization

| Variable | Description |
|----------|-------------|
| STICKER_NAME | Sticker pack name |
| MENU_IMAGE_URL | Menu image URL |
| DESCRIPTION | Bot description |
| CUSTOM_REACT_EMOJIS | Custom reaction emojis |
| STATUS_REACT_EMOJIS | Status reaction emojis |

---

## 🔧 Post-Deployment

### Verify the Bot is Running

1. **Check WhatsApp:** Your bot number should appear online

2. **Send a test command:** 
   ```
   .menu
   ```

3. **Check response:** Bot should reply with command menu

### Initial Configuration

1. Send `.restart` to refresh the bot (if available)

2. Configure group settings (if using in groups):
   - Add bot to group
   - Make bot admin (for full features)
   - Configure group-specific settings

### Monitoring

1. **Check logs regularly:**
   - Docker: `docker logs -f khan-md-bot`
   - PM2: `pm2 logs KHAN-MD`
   - Cloud: Use platform's log viewer

2. **Monitor resource usage:**
   - CPU and memory
   - Bandwidth (for media features)
   - Storage (for downloaded files)

3. **Set up alerts:**
   - Bot offline notifications
   - Error alerts
   - Resource limit warnings

---

## 🛡️ Security Best Practices

### 1. Protect Your Session
- ✅ Never share SESSION_ID
- ✅ Use environment variables
- ✅ Don't commit credentials to git
- ✅ Rotate session if compromised

### 2. Secure Your Deployment
- ✅ Use HTTPS for web interface
- ✅ Set up firewall rules
- ✅ Keep dependencies updated
- ✅ Regular security audits

### 3. Monitor Access
- ✅ Review bot activity logs
- ✅ Monitor unauthorized access attempts
- ✅ Track API usage
- ✅ Set up rate limiting

### 4. Data Protection
- ✅ Enable ANTI_DELETE carefully (privacy concerns)
- ✅ Don't log sensitive messages
- ✅ Encrypt database if storing data
- ✅ Regular backups

### 5. Updates
- ✅ Check for security updates regularly
- ✅ Update dependencies: `npm audit fix`
- ✅ Follow security advisories
- ✅ Test updates before deploying

---

## 🔍 Troubleshooting

### Bot Not Connecting

**Problem:** Bot shows "Connecting..." but never connects

**Solutions:**
1. Check SESSION_ID is correct
2. Verify internet connection
3. Check WhatsApp is not logged in elsewhere
4. Generate new session ID
5. Check for WhatsApp API changes

### Commands Not Working

**Problem:** Bot receives messages but doesn't respond

**Solutions:**
1. Check PREFIX configuration
2. Verify MODE setting
3. Check owner number is correct
4. Review logs for errors
5. Ensure bot has necessary permissions

### Bot Crashes Frequently

**Problem:** Bot keeps restarting or crashing

**Solutions:**
1. Check error logs
2. Increase memory allocation
3. Update dependencies
4. Check for infinite loops
5. Monitor resource usage

### Session Expired

**Problem:** "Session expired" or "Logged out" error

**Solutions:**
1. Generate new SESSION_ID
2. Update configuration
3. Restart bot
4. Check for multi-device conflicts

### High Resource Usage

**Problem:** Bot using too much CPU/memory

**Solutions:**
1. Disable auto-features temporarily
2. Clear cache and temp files
3. Optimize database queries
4. Limit concurrent operations
5. Scale up resources

---

## 📚 Additional Resources

- **Repository Structure:** See `REPOSITORY_STRUCTURE.md`
- **Security Analysis:** See `SECURITY_ANALYSIS.md`
- **Data Flow:** See `DATA_FLOW_DIAGRAM.md`
- **API Documentation:** Check main repository
- **Community Support:** Join WhatsApp channel (see README)

---

## 🆘 Getting Help

### Before Asking for Help

1. ✅ Read this guide completely
2. ✅ Check troubleshooting section
3. ✅ Review error logs
4. ✅ Search existing issues on GitHub
5. ✅ Try restarting the bot

### Where to Get Help

1. **GitHub Issues:** Report bugs or feature requests
2. **WhatsApp Channel:** Community support
3. **Documentation:** Check all .md files in repo

### When Reporting Issues

Include:
- Bot version
- Deployment method
- Error messages (remove sensitive data)
- Steps to reproduce
- Expected vs actual behavior

---

## 📝 Maintenance

### Regular Tasks

**Daily:**
- Monitor bot status
- Check error logs

**Weekly:**
- Review resource usage
- Clear temporary files
- Check for updates

**Monthly:**
- Update dependencies
- Security audit
- Backup configuration
- Review access logs

---

## ⚖️ Legal & Ethical Use

### Do's ✅
- Use for personal automation
- Respect privacy
- Follow WhatsApp terms of service
- Comply with local laws
- Give credit to creators

### Don'ts ❌
- Send spam messages
- Violate privacy
- Distribute malware
- Commercial use without permission
- Harass users
- Scrape data without consent

---

## 📜 License

This project is licensed under Apache License 2.0. See LICENSE file for details.

---

## 👏 Credits

- **Creator:** JawadTechXD
- **Co-Developer:** JawadYT36
- **Based on:** Baileys Library
- **Community:** All contributors

---

**Need more help?** Check the other documentation files or join the community!

*Last Updated: 2025-12-29*
