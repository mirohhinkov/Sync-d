SYNC’D — Full MVP Specification Document. Comprehensive Functional, Technical, UX, and Business Specification


## 1. Product Overview
Sync’d is a mobile-first, real-time content mirroring platform designed to allow users to share visual content from their device **without uploading, transferring, or duplicating files**. Unlike traditional cloud storage, file transfer apps, or messaging platforms, Sync’d streams a secure, real-time visual representation of content stored on the sender’s device.

This document provides a detailed 10–20 page MVP specification covering:
- Functional requirements
- UX flows and interface behavior
- Technical architecture
- Non-functional requirements
- Security model
- Edge-case handling
- Business considerations
- Future roadmap

The MVP's purpose is to validate real-time controlled content sharing while ensuring privacy, low latency, and sender control.
### 1.1 Core Principles
Sync’d is built on five fundamental principles:

1. **Privacy by Default** — The sender maintains 100% control at all times. Content never leaves the sender’s device.
2. **No File Duplication** — Recipients view content but never receive a copy.
3. **Real-Time Access** — Recipients see the content immediately through a secure streamed session.
4. **Granular Control** — The sender chooses what to share, for how long, and to whom.
5. **Mobile First** — Designed primarily for smartphones, but accessible on tablets and desktops.

These principles shape all MVP features, UX flows, and system architecture.
### 1.2 MVP Goals
The primary objectives of the MVP include:

- Validate that real-time, selective content sharing solves key user problems.
- Demonstrate seamless device-to-device content mirroring at low latency.
- Build a secure system based on end-to-end encryption and zero-storage architecture.
- Test user demand for privacy-first content sharing.
- Define the foundation for advanced premium features such as offline P2P, annotation, and enterprise tools.

Success will be measured through usage, session duration, sharing frequency, subscriber conversion intention, and user feedback.
## 2. MVP Feature Set (Detailed Functional Specs)
The following features make up the MVP’s core functional scope. Each is broken down into behaviors, constraints, and edge-case logic.

---

### 2.1 Selective Content Sharing

This feature enables users to select **specific files** from their device to share in real time. It allows them to share visually based content that can be mirrored efficiently.

Supported content types:
- Photos (JPEG, PNG)
- Videos (MP4, MOV)
- PDFs
- Documents (doc, docx)
- Screenshots
- Camera roll items

Functional requirements:
- Built using the native OS content picker (iOS and Android).
- Ability to select up to **5 items** per session.
- Thumbnail previews displayed after selection.
- Users can deselect or reorder items.
- Unsupported file types must show a “Not Supported” indicator.

Edge-case handling:
- If a file is deleted during a session, notify both sender and viewers.
- Large videos (>500 MB) must trigger a warning about possible performance issues.

---

### 2.2 Real-Time Mirroring Engine

The mirroring engine is the core of Sync’d. It sends a **live visual stream** of the selected content.

System behavior:
- Uses WebRTC for peer-to-peer streaming.
- Zero storage: Server acts only as a signaling relay.
- Latency target: **< 300ms**.
- Resolution auto-adjusts based on bandwidth.
- Viewer cannot download, screenshot (detected), or save the file.

Quality Adaptation:
- Tier 1: 1080p (default)
- Tier 2: 720p
- Tier 3: 480p
- Tier 4: 360p failover

If quality drops below Tier 4, session auto-pauses with a “Network weak...” message.

---

### 2.3 Viewer Interface

Recipients access the shared content through:
- Mobile app
- Web viewer (browser)
- Tablet

Viewer capabilities:
- Zoom in/out
- Rotate device for orientation changes
- Swipe between items (if permitted)
- Enforced read-only mode

Viewer restrictions:
- No screenshots (detected + sender notified)
- No downloads
- No saving cached images

Browser requirements:
- Chrome, Safari, Firefox
- Minimum 2019+ device hardware

---

### 2.4 Permissions & Access Control

The sender defines session-level controls:
- View-only (default)
- Time-limited access (1 minute – 24 hours)
- Session revocation (instant)
- Device limit (max 3 viewers)

Sender dashboard shows:
- Connected viewers
- Connection strength
- Screenshot alerts
- Time remaining

---

### 2.5 Authentication

Sender:
- Email login
- Google login
- Apple login

Viewer:
- Anonymous access allowed (no account)
- Optional quick login for history tracking

Authentication is JWT-based (server-issued, short-lived).

# 3. User Experience & Journey Maps
This section outlines every user flow included in the MVP, detailing expected behavior, errors, and micro-interactions.

---

### 3.1 Sender Flow

1. **Open the App**
   - Splash screen displays brand identity.
   - Quick explanation for new users.

2. **Login / Signup**
   - Choose email or social login.
   - If returning user, auto-login.

3. **Home Dashboard**
   - “Start Sharing”
   - “Recent Sessions”
   - “Settings”

4. **Select Content**
   - Opens OS-native picker.
   - Shows selected item thumbnails.
   - Confirm or modify selection.

5. **Create Session**
   - Backend generates encrypted session.
   - Display:
     - QR code
     - 6-digit access code
     - Magic link

6. **Viewers Join**
   - Real-time viewer list updates.
   - Sender can approve or deny.

7. **Sharing Session Begins**
   - Sender swipes to display items.
   - Viewer display syncs instantly.
   - A floating toolbar provides:
     - End session
     - Pause sharing
     - Change permissions
     - Add/remove content items

8. **Session End**
   - Auto-expire or manual end.
   - Summary displayed:
     - Duration
     - Viewer count
     - Screenshot events

---

### 3.2 Viewer Flow

1. **Open link or scan QR**
2. **Join screen appears**
3. **(Optional) Enter name**
4. **Remote content viewer loads**
5. **Receives content in real-time**
6. **Session ends automatically**

## 4. Technical Architecture (Extended)
Sync’d MVP uses a modern, scalable, secure architecture designed for real-time performance and zero-storage privacy.

---

### 4.1 System Diagram (Described)

**Client (Sender)**
↓ (Signaling)
**Backend Server**
↓ (WebRTC)
**Client (Viewers)**

The server only handles:
- Session creation
- Viewer authentication
- WebRTC signaling
- Logging & analytics

All content stays exclusively on the sender’s device.

---

### 4.2 Components

Frontend:
- React Native (iOS + Android)
- Web viewer (React or lightweight vanilla JS)

Backend:
- Firebase Auth
- Firestore (session metadata)
- Node.js Cloud Functions (signaling + token management)

Real-Time Layer:
- WebRTC (peer-to-peer)
- STUN/TURN servers (fallback for NAT traversal)

Encryption:
- AES-256 end-to-end encryption for streams
- DTLS + SRTP (WebRTC standards)
- Encrypted session keys using RSA-2048

Infrastructure:
- Firebase Hosting (web viewer)
- Cloudflare CDN
- Twilio or custom TURN server

---

### 4.3 Database Structure (Firestore)

Collections:
- users
- sessions
- session_participants
- viewer_events
- security_logs

All stored items contain **no content data** — only metadata like timestamps, permissions, and logs.

---

### 4.4 Performance Requirements

- Stream latency: 200–300 ms
- Reconnect time: < 3 seconds
- CPU utilization target: < 50% on 2019+ devices
- Memory usage: < 150 MB per session
- Battery drain: < 8% per 30 minutes


## 5. Security Architecture
Security is a core differentiator for Sync’d.

---

### 5.1 End-to-End Encryption Model
All stream data is encrypted using:
- E2EE AES-256 encryption
- DTLS key exchange
- Session keys rotated every 60 seconds

No content ever reaches:
- Sync’d servers
- Any cloud storage
- Recipient devices

---

### 5.2 Screenshot Detection
Viewer screenshots trigger:
- Immediate alert to sender
- Logged event in session records
- Optional automatic session termination

---

### 5.3 Session Integrity
If a viewer attempts:
- Screen recording
- Casting the display
- Browser dev tools capture

→ Immediate block + warning.

---

### 5.4 Device Verification
Each viewer is assigned:
- Device fingerprint
- Browser signature
- Risk score

Suspicious behavior → connection denial.


## 6. Non-Functional Requirements
### Performance Requirements
- Cold start < 3 seconds
- Session creation < 1.5 seconds
- Viewer join < 2 seconds
- Session resume after network drop < 4 seconds

### Reliability Requirements
- 99% uptime (MVP target)
- Crash-free rate: 98%+
- TURN failover for 12+ network scenarios

### Scalability Requirements
- Unlimited concurrent sessions globally
- Up to 10,000 viewers per minute (for connection requests — not per session)

### Accessibility Requirements
- Text size scaling support
- High contrast mode
- Screen reader compatibility


## 7. Business Logic & Monetisation
The MVP will contain the foundational elements needed to support future monetization.

---

### 7.1 Freemium Tier (MVP)
- Free sharing sessions
- Max 3 viewers
- Max 5 shared items
- Max session duration: 15 minutes
- Watermark “Shared via Sync’d”

---

### 7.2 Premium Tier (Post-MVP)
Unlocks:
- Unlimited session time
- Up to 20 viewers
- No watermark
- Offline P2P mode
- Advanced controls (PIN lock, screenshot hard-block)
- Priority-quality streaming

---

### 7.3 Enterprise Tier (Post-MVP)
Features:
- Admin console
- Usage analytics
- Compliance mode (SOC2 logging)
- Company SSO login
- Team roles & permissions


## 8. Roadmap (12 Months)
### Phase 1 — MVP Launch (Months 1–6)
- Real-time mirroring
- Viewer access via QR/link
- Basic permissions
- Secure sessions

### Phase 2 — Collaboration Features (Months 6–9)
- Annotation tools
- Remote presenter mode
- Multi-sender handoff

### Phase 3 — Offline & Enterprise (Months 9–12)
- Offline P2P mode
- Team dashboards
- Enterprise SSO
- Audit logs

### Phase 4 — Scale & Monetize (12+ Months)
- Paid subscriptions
- Global partnerships
- API access for enterprise clients
