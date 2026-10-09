# Break Reminder

A tiny, portable break reminder for Windows. It nudges you out of the focus tunnel and makes sure you take real screen breaks – for the sake of your health.

Break Reminder is a single file. No installation, no admin rights, no dependencies. Put it on a USB stick or a network drive and run it anywhere.

> **Status:** Work in progress. See [Roadmap](#roadmap).

---

## How it works

Everything is controlled manually with buttons. There is no automatic idle detection via mouse or keyboard on purpose: staring at the screen without touching anything is still screen time.

- **Work** – the work timer runs. Clicking *Work* after a break ends the break and the work timer continues.
- **Break** – the work timer pauses and a break timer starts.

To finish for the day, simply **close the window**. Nothing is saved, so closing resets everything.

### Why there is no Stop button

A Stop or Reset button would make it far too easy to cheat: one click after 85 minutes and the clock starts over without a real break. Closing the window has the same effect, but it is a more deliberate action than a click right next to *Break*. So by design there are only two actions: Work and Break.

---

## The rules

### Breaks

- A break only **counts if it lasts at least 5 minutes**. It then counts with its full length (a 7-minute break adds 7 minutes).
- Breaks **shorter than 5 minutes** do not count towards the break credit. The work timer still stays paused during them.
- As soon as the **break credit reaches 10 minutes or more**, everything is reset (work time and break credit back to zero).

Short interruptions like grabbing a coffee are not a real break. The 5-minute minimum is a compromise: if you can't take 10 minutes in one go, two shorter breaks of at least 5 minutes each are fine too.

### Reminders

Reminders are based on **actual work time**, not wall-clock time.

| Work time | Reminder |
|---|---|
| 60 min | Reminder (dismissible) |
| 70 min | Reminder (dismissible) |
| 80 min | Reminder (dismissible) |
| 90 min | **Strong reminder** (e.g. full screen, flashing, sound) |

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
3. Double-click the file. It opens as its own window.
4. Click **Work** and get to work.

### Requirements

- Windows (uses the built-in `mshta.exe`, no installation needed)
- Some corporate security policies or antivirus tools block HTA files. If it does not start, that is the most likely reason.

---

## How it's built

- **HTA (HTML Application):** a plain HTML file with the `.hta` extension. Windows runs it with the built-in `mshta.exe` as a standalone window without a browser.
- **One file:** HTML, CSS and JavaScript all live in a single file.
- **ES5 JavaScript:** HTAs use the legacy Internet Explorer engine, so the code sticks to ES5 (`var`, `function`, no arrow functions, no template literals, no classes).
- **Timestamps instead of ticks:** elapsed time is calculated from `Date.now()` rather than by counting timer ticks, because timers get throttled when the window is in the background.

### Development

- `breakReminder.html` is the source file. It can be opened and debugged in any browser.
- To test it as an application, copy it to `breakReminder.hta` and double-click the copy.
- The `.hta` file is never edited directly and is not committed; it is attached to GitHub Releases instead.

---

## Roadmap

- [ ] Show a running work timer
- [ ] Work / Break with separate work and break timers
- [ ] Single toggle button (Work ↔ Break) and a status line
- [ ] Break credit (5-minute minimum, reset at 10 minutes)
- [ ] Dismissible reminders at 60, 70 and 80 minutes
- [ ] Strong reminder at 90 minutes
- [ ] HTA polish and testing in the legacy engine