# PresenceHub — Couch Link (prototype)

**Not a call. Company.** A tablet on the couch becomes the away parent: his face
shows up there (and turns when he looks around); he sees the room from his phone
in the hotel and can drag to look around. Nobody performs. He's just... there.

Two rituals are built in on purpose — they're the product thesis:

- **The knock.** He can't just appear. The couch tablet glows amber and chimes;
  someone at home taps "Let them in." Who taps, and how willingly, is the demand
  signal we're testing.
- **The head-turn.** When he pans the room, his face on the couch tablet visibly
  turns that way. His gaze is always legible — presence, not surveillance.

## Test it in 60 seconds (same machine)

1. `node serve.js` from the project root (already there), open
   `http://localhost:4173/PresenceHub/index.html` in **two tabs**.
2. Tab 1: **This is the couch** → allow camera → **Open the door** → note the 4-letter code.
3. Tab 2: **I'm away** → type the code → **Knock**.
4. Tab 1 glows and chimes → **Let them in.** You're connected. Drag the room view in tab 2
   and watch the face tilt in tab 1.

No camera? It falls back to an animated test pattern automatically, so the
connection pipeline is still testable.

## Put it on the internet (for the real cousin test)

Cameras require HTTPS, so for two real devices in two cities:

1. Go to **netlify.com/drop** (free, no account needed to start).
2. Drag the `PresenceHub` folder onto the page.
3. Send the https link it gives you to both devices. Done.

(GitHub Pages or Vercel work too. The page is a single static file.)

## The movie-night protocol

- Home: prop the tablet on the couch, camera facing the room, next to the kid.
- Away: knock a few minutes before the show starts. Then **don't perform** — just watch along.
- The metric: count spontaneous unprompted remarks the kid makes *to the tablet*
  ("did you see that?!"). That's co-presence. Zero after 3–4 sessions = signal too.

## 360 cameras

The viewer auto-detects an equirectangular (2:1) feed and switches to true
look-around rendering (WebGL, drag to pan; "360" button forces it on/off).

- Works today: any camera that shows up as an **equirect webcam** — e.g. Ricoh
  Theta's native webcam mode, or Insta360 desktop app → OBS **virtual camera**.
- Without one: any wide phone/tablet camera works — the viewer digitally pans a
  zoomed wide feed. Less magic, same test.

## Tech validation ladder

Run these in order — each rung isolates one layer. On any failure, run
**🔧 Run connection check** (landing page) on that network and screenshot it.
In a session, tap the timer chip to see live stats: which network path the
connection took (LAN / STUN / TURN), RTT, bitrate, resolution.

| Rung | Test | Proves |
|------|------|--------|
| 0 | Two tabs, same machine | App + signaling broker |
| 1 | Two devices, same Wi-Fi | Real cameras/mics, mobile autoplay quirks |
| 2 | Phone on cellular ↔ home Wi-Fi | NAT traversal across networks (do this solo: turn off Wi-Fi on one phone) |
| 3 | Hotel Wi-Fi ↔ home | The real gauntlet — may need TURN |
| 4 | 60+ min session | Endurance; watch the stats overlay for drops |
| 5 | 360 camera as webcam | The only rung needing hardware |

## TURN relay (needed only if rung 2/3 fails)

There is no reliable free-anonymous TURN server (we probed the known ones —
dead). If the connection check shows `STUN ✗` or a hotel network blocks the
call, get free credentials: **metered.ca** → sign up (free, 50GB/mo) → copy
your TURN URLs + username + credential. Then open the app once per device as:

```
https://josharend.github.io/couch-link/#turn=turn:a.relay.metered.ca:80,turn:a.relay.metered.ca:443|USERNAME|CREDENTIAL
```

It persists in localStorage; afterwards the plain link works on that device.
The connection check reports whether TURN is configured and reachable.

## Known limits (prototype-honest)

- Signaling rides the free public PeerJS broker; media is peer-to-peer.
- One visitor at a time, no accounts, no persistence. Refresh = start over.
- Codes are 4 letters and unauthenticated. Fine for family testing, not for real use.
- No auto-rejoin after a dropped connection yet — you knock again.

## Debug

With the couch side open, run `PH.selfTest()` in the browser console to simulate
a visitor knocking from a second peer with a test-pattern stream.
