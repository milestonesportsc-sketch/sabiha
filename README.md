# SABIHA-WATCH-SECURITY

Mobile security panel for **your own** phone, devices and server. Part of the Sabiha assistant project.

## Modules
Dashboard · AI Security Assistant · Bluetooth Scanner (animated radar) · Wi-Fi Monitor · Network Security · Vulnerability Scanner · Device Monitoring · Security Reports · Settings

Tap the mic and speak (English or বাংলা, set in Settings): "open Bluetooth", "scan", "dashboard", "report", or ask a security question.
Voice never locks the server by itself; it opens the Dashboard and you tap Lock down to confirm.

## Sabiha avatar
Sabiha's portrait (`sabiha.jpg`) shows on Home and in the Assistant. Tap her: she greets you, then listens. While she speaks, the portrait glows and pulses. Replies use the phone's built-in voice.

## Watch face
The Watch Face module draws Sabiha's dial (portrait, security status, time, date, curved banner). Download face image saves a PNG without time and stats; use it as a custom dial or photo background if your watch app offers that.

## Make Sabiha's voice work
Tap the mic once: it turns on and stays on (listen, answer, listen again). Say "stop" or tap the mic to turn it off.
Chrome only allows the microphone on **https** pages or **localhost**, not on a file opened from storage. Options:
- Upload the folder to GitHub Pages or Netlify (free, https).
- Or on a computer: `python3 -m http.server` in this folder, then open `http://localhost:8000`.
- Or use the Android app build (see below).
The first time, tap Allow on the microphone prompt. A website can't switch the mic on by itself, so that first tap is always needed.

## One-file version
`SABIHA-WATCH.html` (next to this folder) is the whole app in one file with both Sabiha pictures inside. Open it in Chrome. It starts on Sabiha's watch face. Settings > Sabiha look switches between Blue guardian and Cyber warrior.

## Run
Open `index.html` in Chrome on Android. Starts in Demo mode.

## Real Android Bluetooth scanner
Browsers can't passively scan all nearby Bluetooth devices. Wrap this folder as an Android app with Capacitor:

```
npm init -y && npm i @capacitor/core @capacitor/cli @capacitor/android @capacitor-community/bluetooth-le
npx cap init SabihaSecurity com.sabiha.security --web-dir .
npx cap add android && npx cap sync
```
Add to `index.html` before `script.js` (bundled with Vite/webpack so `BleClient` imports work):

```js
import { BleClient } from '@capacitor-community/bluetooth-le';
window.SabihaNativeBLE = async (add) => {
  await BleClient.initialize();
  await BleClient.requestLEScan({}, r => add(r.device.name, r.device.deviceId, r.rssi));
  await new Promise(res => setTimeout(res, 8000));
  await BleClient.stopLEScan();
};
```
Android needs BLUETOOTH_SCAN permission (Android 12+) or location permission (older). Android also limits Wi-Fi scans (about 4 per 2 minutes), so a native Wi-Fi plugin is needed for network names and signal.

## Server backend contract
`POST /action`, header `Authorization: Bearer <token>`, JSON `{ "action": "status|block_ip|restart_service|lockdown", "ip"?, "service"? }`
Reply: `{ "cpu", "ram", "failedLogins", "locked", "message" }`.
HTTPS only, allow-list restartable services, validate IPs, rate-limit, log every action.

## Scope
Scanning is for devices and networks you own or are authorized to test.
