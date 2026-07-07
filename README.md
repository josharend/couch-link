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

## Known limits (prototype-honest)

- Signaling rides the free public PeerJS broker; media is peer-to-peer. No TURN
  relay, so a strict hotel network may block it — that failure is itself data
  (it's the infrastructure a real product would pay for).
- One visitor at a time, no accounts, no persistence. Refresh = start over.
- Codes are 4 letters and unauthenticated. Fine for family testing, not for real use.

## Debug

With the couch side open, run `PH.selfTest()` in the browser console to simulate
a visitor knocking from a second peer with a test-pattern stream.
