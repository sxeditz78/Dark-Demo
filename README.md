# 🎬 VVIP VDO TELE BOT

A private Telegram bot that serves videos/images from a private channel to approved users, with auto-delete, admin controls, and broadcast support.

---

## ⚙️ Features

- 📹 Sends latest **5 videos/images** per session from a private channel
- ⏱️ **Auto-deletes** media after **1 minute**
- 📢 **Broadcast** to all users (auto-deletes after **6 hours**)
- 🔄 **7-day cycle** — same content shown for 7 days, then reset
- 🗄️ **PostgreSQL** backend (Neon)
- ⚡ Concurrent sends for fast delivery
- 📷 Supports **video**, **photo**, and **document** type media

---

## 🛠️ Setup

### 1. Clone & Install

```bash
git clone https://github.com/sxeditz78/VVIP-VDO-TELE-BOT
cd VVIP-VDO-TELE-BOT
pip install -r requirements.txt
```

### 2. Environment Variables

Create a `.env` file:

```env
BOT_TOKEN=your_bot_token
ADMIN_ID=your_telegram_user_id
DATABASE_URL=postgresql://user:pass@host/dbname
CHANNEL_ID=-1001234567890
CONTACT_ADMIN=https://t.me/yourusername
```

| Variable | Description |
|---|---|
| `BOT_TOKEN` | From @BotFather |
| `ADMIN_ID` | Your Telegram user ID |
| `DATABASE_URL` | Neon / any PostgreSQL URL |
| `CHANNEL_ID` | Private channel ID (negative number) |
| `CONTACT_ADMIN` | Admin contact link shown to users |

### 3. Run

```bash
python bot.py
```

---

## 🚀 Deployment (AWS EC2)

```bash
# Pull latest & restart
git pull && sudo systemctl restart bot

# Check logs
sudo journalctl -u bot -f
```

**systemd service** (`/etc/systemd/system/bot.service`):
```ini
[Unit]
Description=VVIP VDO Telegram Bot
After=network.target

[Service]
WorkingDirectory=/home/ubuntu/VVIP-VDO-TELE-BOT
ExecStart=/usr/bin/python3 bot.py
Restart=always
EnvironmentFile=/home/ubuntu/VVIP-VDO-TELE-BOT/.env

[Install]
WantedBy=multi-user.target
```

---

## 📋 Admin Commands

| Command | Description |
|---|---|
| `/start` | User ko latest videos/images bhejo |
| `/reset` | Sabka cache clear karo (naye videos milenge) |
| `/setcaption <text>` | Bottom caption change karo |
| `/broadcast <text>` | Sabko message bhejo (reply to media bhi works) |

### Broadcast with Image/Video:
1. Channel mein image/video bhejo
2. Us message ko reply karo
3. `/broadcast Optional caption yahan` likho

---

## ⏱️ Timing Settings (`config.py`)

| Setting | Value | Description |
|---|---|---|
| `VIDEOS_PER_SESSION` | 5 | Ek baar mein kitne media send ho |
| `VIDEO_DELETE_SECONDS` | 60 (1 min) | Media auto-delete time |
| `BROADCAST_DELETE_SECONDS` | 21600 (6 hrs) | Broadcast auto-delete time |
| `CYCLE_DAYS` | 7 | Kitne din baad naya content |

---

## 🗄️ Database Tables

| Table | Purpose |
|---|---|
| `users` | All bot users |
| `channel_videos` | Indexed media from channel |
| `fetched_content` | User-wise sent message IDs |
| `broadcast_jobs` | Pending broadcast deletes |
| `settings` | Bot settings (caption etc.) |

---

## 📁 Project Structure

```
├── bot.py          # Main bot file
├── config.py       # Settings & env loader
├── requirements.txt
└── .env            # Your secrets (not in git)
```

---

## 🔧 How It Works

1. **Channel post** → `channel_post_handler` auto-indexes media into DB
2. User sends `/start` → bot fetches latest 5 media from DB → sends concurrently
3. After **1 minute** → all sent media auto-deleted
4. Same user gets **same content for 7 days** (cached), then resets
5. Admin can force reset anytime with `/reset`
