# Hackathon Broadcast — Admin & Display Screens

A tiny real-time notification system for hackathons and events.

- **`/admin`** — a control panel where an organizer types a message and hits send.
- **`/display`** — a big screen (projector/TV) that instantly shows the message with an animation and a sound.

Both pages talk over WebSockets (Socket.IO), so the display updates the moment admin clicks send — no refresh needed, and you can have multiple display screens open at once (e.g. one per room) and they'll all update together.

---

## 1. Requirements

- [Node.js](https://nodejs.org) v16 or newer (v18+ recommended)
- npm (comes with Node)

Check you have them:
```bash
node -v
npm -v
```

## 2. Setup

```bash
# 1. Go into the project folder
cd hackathon-display

# 2. Install dependencies
npm install

# 3. Start the server
npm start
```

You should see:
```
Server running: http://localhost:3000
Admin panel:    http://localhost:3000/admin
Display screen: http://localhost:3000/display
```

## 3. Usage

1. Open **`http://localhost:3000/display`** on the projector/TV screen (put the browser in fullscreen — press `F11`).
2. Open **`http://localhost:3000/admin`** on your laptop/phone — this is the control panel.
3. On `/admin`:
   - Type a **Headline** (required) and optional **Details**.
   - Pick a priority: Info / Success / Warning / Urgent (each has its own color and a distinct sound).
   - Click **Send to display screen**.
4. The `/display` screen instantly shows the notification and plays a sound.

Click **Clear display history** on `/admin` to reset the display screen back to its idle state.

> **Note on sound:** most browsers block audio until the page has been interacted with at least once. Click anywhere on the `/display` page one time after loading it (e.g. click once to enter fullscreen) so the first real notification sound isn't blocked.

## 4. Using it across multiple computers (not just one laptop)

The server now listens on your whole network automatically — no manual setup needed. When you run `npm start`, it detects your machine's LAN IP and prints everything you need, including a scannable QR code for the admin panel:

```
=== Hackathon Broadcast — running ===

On this machine:
  Admin panel:    http://localhost:3000/admin
  Display screen: http://localhost:3000/display

From other devices on the same network:
  Admin panel:    http://192.168.1.42:3000/admin
  Display screen: http://192.168.1.42:3000/display

Scan to open the admin panel on a phone:
[QR code]
```

To use it across machines:

1. Run `npm start` on the computer that will act as the server (your laptop is fine).
2. On the **display computer/projector**, open the `http://<ip>:3000/display` URL shown in the terminal.
3. On **your own device**, open (or scan the QR code for) the `http://<ip>:3000/admin` URL — works great from a phone too.
4. Make sure every device is on the **same WiFi/network**, and that your firewall allows incoming connections on port `3000` (macOS/Windows may prompt you the first time — allow it).

If no IP is detected, the terminal will tell you — usually means the server machine isn't connected to WiFi/LAN.

## 5. Project structure

```
hackathon-display/
├── server.js           # Express + Socket.IO server, routes for /admin and /display
├── package.json
└── public/
    ├── admin.html       # Control panel UI
    └── display.html     # Big-screen notification UI
```

## 6. Customizing

- **Port:** set the `PORT` environment variable, e.g. `PORT=4000 npm start`.
- **Colors / priorities:** edit the CSS variables and `type` values (`info`, `success`, `warning`, `urgent`) at the top of `admin.html` and `display.html`.
- **Sounds:** sounds are generated in-browser with the Web Audio API (no sound files needed). Adjust the `freqMap` object in `display.html` to change the tones per priority.
- **History length:** the server keeps the last 20 notifications in memory (`server.js`, `history.slice(0, 20)`). This resets whenever the server restarts — swap in a database if you need it to persist.

## 7. Deploying beyond your laptop (optional)

If you want the display reachable without keeping your own laptop running as the server, deploy `server.js` to any Node host (Render, Railway, Fly.io, a small VPS, etc.) and just open `https://your-deployed-url/admin` and `https://your-deployed-url/display` from any device — no local network setup needed.

## 8. Troubleshooting

| Problem | Fix |
|---|---|
| `/display` doesn't update | Make sure both `/admin` and `/display` are pointed at the same server URL (same host/port). |
| No sound plays | Click anywhere on the `/display` page once — browsers block audio until a user interacts with the page. |
| `EADDRINUSE` error on start | Port 3000 is already in use — run with a different port: `PORT=4000 npm start`. |
| Can't reach from another device | Check both devices are on the same network and your firewall isn't blocking the port. |