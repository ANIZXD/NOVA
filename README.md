# NOVA — hands-free browser voice assistant (Chrome / Edge, MV3)

Say a wake word and NOVA drives the browser. It answers out loud too.

**Commands:** open sites · search (including "search youtube for …") · play/pause ·
next/previous · scroll · volume up/down/set, mute/unmute (*per tab, by name*) ·
new tab · close tab · close browser · reload · back · forward.

**Wake words:** `nova`, `noah`, `nsa`, `innova`, `in nova`, … and after any
wake-word command a **15-second window** where bare commands work without
repeating the wake word ("next", "pause", "mute bairan").

## Install

1. `chrome://extensions` (or `edge://extensions`)
2. Turn on **Developer mode**
3. **Load unpacked** → select the `extension/` folder in this repo

(Or unzip `hey-nova-extension.zip` anywhere and load that folder instead.)

4. Click the NOVA icon → **Start**. A small window opens and starts listening by
   itself. Leave that window visible.

## Why the microphone used to stop working

The voice page used to open as a **tab**. Chrome throttles hidden tabs — both
timers *and* the speech session — so the moment you switched to the tab NOVA had
just opened for you, it went deaf. Worse, `onend`/`onerror` don't always fire
when the session is dropped that way, so the old restart logic sat waiting for an
event that never came.

Fixed in this version:

- The voice page opens in a small **separate window**, not a tab — a visible
  window keeps listening. Clicking Start again **focuses** the existing window
  instead of opening a second one that would fight over the microphone.
- It **auto-starts** listening (`?auto=1`) — no click needed.
- A **2-second keep-alive** revives a silently dropped session, plus immediate
  revival on `visibilitychange` / `focus`.
- A failed `recognition.start()` now `abort()`s and retries (capped) instead of
  leaving the assistant permanently stuck on an `InvalidStateError`.

## Still a browser limitation

Keep the small window **visible** — minimising it can still let Chrome throttle
the page. There is no way to grab an earbud's button and no persistent background
microphone in MV3, so a genuinely hands-free, screen-off assistant needs a native
app instead. This extension is the browser-bound stopgap.
