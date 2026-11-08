# Break Reminder

A tiny, portable break reminder for Windows. It nudges you out of the focus tunnel and makes sure you take real screen breaks – for the sake of your health.

Break Reminder is a single file. No installation, no admin rights, no dependencies. Put it on a USB stick or a network drive and run it anywhere.

> **Status:** Version 1.0 – all core features are done. Next up: a PowerShell version. See [Roadmap](#roadmap).

---

## How it works

Everything is controlled manually with a single button that switches between two actions. There is no automatic idle detection via mouse or keyboard on purpose: staring at the screen without touching anything is still screen time.

- **start work** – the work timer runs. Clicking it after a break ends the break and the work timer continues.
- **start break** – the work timer pauses and the break timer starts.

The button always shows the action the next click will trigger. A colored status line shows what is going on right now: *free time* (not started yet, purple), *working* (pink) or *on break* (cyan). Below it you see the total work time and the break time, both as `hh:mm:ss`.

To finish for the day, simply **close the window**. Nothing is saved, so closing resets everything.

### Why there is no Stop button

A Stop or Reset button would make it far too easy to cheat: one click after 85 minutes and the clock starts over without a real break. Closing the window has the same effect, but it is a more deliberate action than a quick click in the app. So by design there are only two actions: work and break.

---

## The rules

### Breaks

- A break only **counts if it lasts at least 5 minutes**. It then counts with its full length (a 7-minute break adds 7 minutes).
- Breaks **shorter than 5 minutes** do not count towards the break credit. The work timer still stays paused during them.
- As soon as the **break credit reaches 10 minutes or more**, everything is reset: work time, break credit and reminders start over.

Short interruptions like grabbing a coffee are not a real break. The 5-minute minimum is a compromise: if you can't take 10 minutes in one go, two shorter breaks of at least 5 minutes each are fine too.

### Reminders

Reminders are based on **actual work time**, not wall-clock time. Breaks do not count as work time.

| Work time | Reminder |
|---|---|
| 60 min | "time for a break!" – the window moves to the center of the main screen. Dismissible. |
| 70 min | Same as above |
| 80 min | Same as above |
| 90 min and every 10 min after | **Forced break:** the window fills the whole screen and the reminder can no longer be dismissed. It only disappears once the break credit is full. |

The window can still be minimized at any time, even during a forced break.

### Example

| What happens | Work time | Break credit |
|---|---|---|
| Work 40 min | 40 | 0 |
| 3 min break | 40 (paused) | 0 (too short) |
| Work 20 min | 60 → first reminder | 0 |
| 7 min break | 60 | 7 |
| Work briefly, 3 min break | ~61 | 7 (3 min too short) |
| 5 min break | – | 12 → **reset** |

### No tracking

There are no logs and no statistics. Break Reminder only cares about the current stretch of work.

---

## Usage

1. Download `breakReminder.hta` from the [Releases](../../releases) page.
2. If Windows blocks the file because it was downloaded from the internet: right-click → *Properties* → tick **Unblock** → *OK*.
3. Double-click the file. It opens as a small window in the center of your main screen.
4. Click **start work** and get to work.

### Requirements

- Windows (uses the built-in `mshta.exe`, no installation needed)
- Some corporate security policies or antivirus tools block HTA files. If it does not start, that is the most likely reason.

---

## Known limitations

**The window cannot bring itself to the front.** Windows deliberately prevents programs from pushing themselves in front of the window you are working in, so a reminder can appear behind other windows. The following approaches were tested and did not work reliably: `window.focus()`, `alert()`, `WScript.Shell.AppActivate`, and moving the window off-screen and back. The window only came to the front when it was moved to a different monitor.

**Tip:** Keep the small window visible somewhere, for example in a corner of your screen or on a second monitor.

A planned PowerShell version (see Roadmap) is expected to solve this, as it can keep a window always on top.

---

## How it's built

- **HTA (HTML Application):** a plain HTML file with the `.hta` extension. Windows runs it with the built-in `mshta.exe` as a standalone window without a browser.
- **One file:** HTML, CSS and JavaScript all live in a single file.
- **ES5 JavaScript:** HTAs use the legacy Internet Explorer engine, so the code sticks to ES5 (`var`, `function`, no arrow functions, no template literals, no classes).
- **Timestamps instead of ticks:** elapsed time is calculated from `Date.now()` rather than by counting timer ticks, because timers get throttled when the window is in the background. A single ticker refreshes the display once per second.
- **Look:** dark theme in the colors of [digicoon.de](https://digicoon.de/). The font [Space Grotesk](https://github.com/floriankarsten/space-grotesk) is embedded as Base64 in the file, so it works offline without being installed. Space Grotesk is licensed under the SIL Open Font License 1.1, which allows embedding.
- **State handling:** the app is always in one of three states (`idle`, `working`, `onBreak`). The toggle button checks the current state and decides whether to start working or start a break.

### Development

- `breakReminder.html` is the source file. It can be opened and debugged in any browser. Window size and position are only applied in the HTA.
- To test it as an application, copy it to a separate test folder, rename the copy to `breakReminder.hta` and double-click it.
- The `.hta` file is never edited directly and is not committed; it is attached to GitHub Releases instead.
- **Time lapse for testing:** the constant `MINUTE` at the top of the script defines how long one minute is in milliseconds. Set it to a small value (e.g. `1000` or `100`) to test the rules quickly, and back to `60 * 1000` before a release.

---

## Roadmap

- [x] Show a running work timer
- [x] Work / Break with separate work and break timers
- [x] Status line (*free time* / *working* / *on break*)
- [x] Break credit (5-minute minimum, reset at 10 minutes)
- [x] Dismissible reminders at 60, 70 and 80 minutes
- [x] Forced break at 90 minutes (full screen, no dismiss)
- [x] Single toggle button (Work ↔ Break)
- [x] Styling (digiCoon colors, card layout, embedded font)
- [x] Testing in the legacy engine and first release (v1.0)
- [ ] Version 2 in PowerShell (always-on-top window)
