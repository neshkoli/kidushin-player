# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A single-file static audio player (`index.html`) for a collection of Hebrew halacha (Jewish law) lecture MP3s on the topic of Kiddushin (betrothal). The lectures cover topics from Even HaEzer and Choshen Mishpat sections of the Shulchan Aruch.

No build step, no dependencies, no package manager — open `index.html` directly in a browser.

## Architecture

`index.html` contains inline CSS, inline JavaScript, and the HTML structure. The MP3 files sit alongside it in the same directory. Track metadata and ordering live in `tracks.json`.

### tracks.json

The source of truth for track order and grouping. Structure:

```json
{
  "אבן העזר": {
    "הלכות קידושין": [
      { "סימן": "כו", "שם": "במה אישה מתקדשת", "file": "סימן כו אבן העזר.mp3" }
    ]
  }
}
```

Top-level keys are sefer names; their values are objects whose keys are הלכות section titles; each section is an array of track entries with `סימן`, `שם`, and `file`.

**Key JS globals:**
- `tracks[]` — flat ordered array of MP3 filenames, built at runtime from `tracks.json`
- `trackNames[]` — parallel array of display labels (`סימן X – name`)
- `currentIndex` — index into `tracks[]`, or `-1` if nothing loaded yet
- `isPlaying` — boolean playback state
- `audio` — the single `<audio>` element (hidden)

**Core functions:**
- `buildList(data)` — iterates `tracks.json`, renders `.section-header` / `.group-header` / `.track-item` elements and populates `tracks[]` and `trackNames[]`
- `loadTrack(index, autoPlay)` — sets `audio.src` to `encodeURIComponent(filename)`, updates UI
- `setPlaying(state)` — syncs play/pause icon and `.playing` CSS class on track items

**UI components:**
- `.player-card` — now-playing display, progress bar, and transport controls (prev/play/next)
- `.track-list` — clickable list built dynamically from `tracks.json` at page load; includes `.section-header` (sefer) and `.group-header` (הלכות section) labels between track items

**Progress bar seek** is handled via a click listener on `.progress-bar-bg`, computing seek position from `clientX` relative to the bar's `getBoundingClientRect()`.

## Adding or Reordering Tracks

1. Drop the `.mp3` file into this directory.
2. Add/reorder the entry in the appropriate section of `tracks.json`.

The display label is built automatically as `סימן X – שם` from the JSON fields.
