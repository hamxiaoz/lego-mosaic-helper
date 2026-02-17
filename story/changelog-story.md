# Building the LEGO Mosaic Helper — A Story

## The Problem

I was building a 48x48 LEGO Art mosaic — a big grid of tiny dots in about 5 colors. The plate is huge and has no reference points. You're constantly counting dots, losing your place, and second-guessing which color goes where. I needed a digital companion.

## Version 1: The First Working App

The idea was simple: take a photo of the reference image, have the app figure out which color goes in each of the 2,304 positions, and show it as an interactive grid.

The first version had all the basics:
- **Drag and drop** your reference photo
- **Auto-color detection** — the app samples each dot position and snaps it to the nearest LEGO color (yellow, black, white, light gray, dark gray)
- **Hover to see distances** — move your mouse over any dot and it shows how far you are from the next color change in every direction (up, down, left, right), drawn as red lines with numbers
- **Crosshair highlight** — the full row and column light up so you don't lose your place
- **Coordinates** — always shows which row and column you're on (1-indexed, like a spreadsheet)
- **Zoom and pan** — scroll to zoom in, drag to move around
- **Section grid** — toggle bold lines every 8 dots, splitting the plate into 36 sections for easier chunking
- **Correction mode** — click any dot to cycle its color if the auto-detection got it wrong

All of this in a single HTML file. No install, no build tools — just open it in a browser.

![The starting screen](0.png)
![First load of the mosaic](1.png)

## Fix: Distance Was Off By One

The first bug report: the distance numbers were including the different-colored dot in the count. If you were 3 same-colored dots away from a boundary, it showed "4" because it counted the boundary dot too. Fixed it to only count the same-color dots between you and the change — which is what you actually need when you're counting studs on the real plate.

![Fixing the distance calculation](2%20update%20distance.png)

## The "Zero Distance" Problem

After fixing the count, a new issue appeared. When the very next dot in a direction was already a different color, the distance was 0 — and the app just showed... nothing. No line, no number. But that's exactly when you need information most!

![Hovering on a dot with no distance shown](3%20not%20showing%20distance.png)

The question was: what *should* it show? After thinking it through, the answer was clear. Two cases:

**Case 1:** You're in a run of same-colored dots. Show how many extend in that direction (red line). This tells you "I need to place 3 more yellow dots to the right."

**Case 2:** The next dot is already different. Show how long *that* block is (cyan line). This tells you "there are 5 dark gray dots to my right before the color changes again" — a useful landmark for counting.

![Planning the new distance logic](4.png)
![The user approves the plan](4%201.png)

Now every direction always shows something useful, and the two cases are color-coded so you can tell them apart at a glance.

![Cyan lines showing adjacent-block distances](5%201.png)
![Mixed red and cyan rays in action](5%202.png)

## Simplifying: No More Crop Step

The original version had a crop/alignment step after loading an image — you'd drag four corner handles to line up with the plate edges. But in practice, the reference photos are already just the plate. Removed the whole crop UI and made it go straight from image load to the interactive grid. One less step.

## Going Self-Contained: Embedded Demo

To make the app shareable as a single file that works anywhere — even hosted on the web — the reference mosaic image was embedded directly into the HTML as base64 data. Now there's a "Load Demo" button that instantly shows the mosaic without needing any file. The HTML grew to about 500KB, but it's fully self-contained. No server needed.

## The Real Workbench

And here's what it all looks like in the real world — the actual LEGO mosaic in progress, with bags of dots sorted by color and a row counter on the side.

![The real LEGO mosaic workbench](workbennch.jpeg)

---

*Built with Claude Code in a single session. The entire app is one HTML file.*
