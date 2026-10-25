# 🎵 Resonance — A Full-Featured Music Streaming Web App (Spotify Clone)

> **Project by Team No. 21**  
> A front-end engineering project that faithfully recreates the core experience of a modern music streaming platform — built entirely with Vanilla HTML, CSS, and JavaScript. No frameworks. No build tools. Just clean, well-structured, browser-native code.

---

## 📌 Table of Contents

1. [Project Overview](#project-overview)
2. [Live Demo & How to Run](#live-demo--how-to-run)
3. [Technology Stack](#technology-stack)
4. [Architecture Overview](#architecture-overview)
5. [Module Breakdown](#module-breakdown)
   - [data.js — Music Database](#datajs--music-database)
   - [auth.js — Authentication Engine](#authjs--authentication-engine)
   - [store.js — User Data Store](#storejs--user-data-store)
   - [player.js — Playback Engine](#playerjs--playback-engine)
   - [search.js — Search Engine](#searchjs--search-engine)
   - [ui.js — Shared UI Helpers](#uijs--shared-ui-helpers)
   - [app.js — SPA Shell & Router](#appjs--spa-shell--router)
   - [style.css — Design System](#stylecss--design-system)
6. [Pages & Views](#pages--views)
7. [Features In Detail](#features-in-detail)
   - [🔐 Authentication System](#-authentication-system)
   - [🎵 Music Playback Engine](#-music-playback-engine)
   - [📂 Playlist Management](#-playlist-management)
   - [❤️ Liked Songs](#️-liked-songs)
   - [🔎 Search](#-search)
   - [🎲 Smart Mixes (Personalization)](#-smart-mixes-personalization)
   - [📋 Queue Management](#-queue-management)
   - [👤 User Profile](#-user-profile)
   - [💎 Premium Page](#-premium-page)
   - [📞 Contact Form](#-contact-form)
8. [JavaScript Concepts Used](#javascript-concepts-used)
   - [Promises](#promises)
   - [IIFE (Immediately Invoked Function Expression)](#iife-immediately-invoked-function-expression)
   - [Event Delegation](#event-delegation)
   - [Module Pattern](#module-pattern)
   - [Hash-Based Client-Side Routing (SPA Router)](#hash-based-client-side-routing-spa-router)
   - [Observer / Pub-Sub Pattern](#observer--pub-sub-pattern)
   - [localStorage Persistence](#localstorage-persistence)
   - [Web Crypto API](#web-crypto-api)
   - [DOM Manipulation & innerHTML Rendering](#dom-manipulation--innerhtml-rendering)
   - [Keyboard Shortcuts](#keyboard-shortcuts)
9. [Data Model](#data-model)
10. [Responsive Design](#responsive-design)
11. [Security Considerations](#security-considerations)
12. [Project File Structure](#project-file-structure)
13. [Known Limitations & Future Improvements](#known-limitations--future-improvements)

---

## Project Overview

**Resonance** is a Spotify-inspired music streaming web application built as a front-end engineering (FEE) project. It demonstrates how a feature-complete, production-quality Single Page Application (SPA) can be built using only browser-native technologies — no React, no Vue, no Angular, no bundlers.

The app simulates a real music streaming experience including:

- A fully functional audio player with play/pause, skip, seek, shuffle, and repeat
- A complete user authentication flow (sign up, log in, log out) with password hashing
- Per-user data persistence using `localStorage`
- Dynamic playlist creation, renaming, and deletion
- Song liking, queue management, and recently played history
- Smart algorithmic playlists (Daily Mix, Discover Weekly, Release Radar, Repeat Rewind)
- A real-time search engine filtering songs, artists, albums, and playlists
- Artist and album detail pages
- A user profile page with editable name and real-time stats
- A simulated Premium upgrade page
- A contact form with local storage submission
- Fully responsive design for desktop, tablet, and mobile

---

## Live Demo & How to Run

This project has **zero dependencies** and **no build step required**.

### Option 1: Open directly in a browser
Simply open `index.html` in any modern browser (Chrome, Firefox, Edge, Safari).

```bash
# Navigate to the project folder
cd spotify-clone-FEE-project-v2-main

# Open the main file
start index.html   # Windows
open index.html    # macOS
```

### Option 2: Use a local dev server (recommended)
Using VS Code's **Live Server** extension or Python's built-in server avoids any file-protocol limitations:

```bash
# Python 3
python -m http.server 8080

# Then visit:
# http://localhost:8080
```

---

## Technology Stack

| Category | Technology |
|---|---|
| **Markup** | HTML5 (Semantic elements, ARIA attributes) |
| **Styling** | Vanilla CSS3 (CSS Grid, Flexbox, custom properties, media queries) |
| **Logic** | Vanilla JavaScript (ES5/ES6, no frameworks, no build tools) |
| **Data Persistence** | Browser `localStorage` API |
| **Audio Playback** | HTML5 `<audio>` element |
| **Cryptography** | Web Crypto API (`crypto.subtle.digest`) |
| **Routing** | Hash-based client-side routing (`window.location.hash`, `hashchange` event) |
| **Audio Source** | SoundHelix freely streamable MP3 demo tracks |
| **Design Inspiration** | Spotify (Dark mode, green accent, card-based layout) |

---

## Architecture Overview

Resonance follows a **modular, script-tag-based architecture**. Each module is wrapped in an **IIFE (Immediately Invoked Function Expression)** to avoid polluting the global namespace. Modules communicate by attaching a single object to the `window` global (e.g., `window.RDB`, `window.RAuth`, `window.RStore`, `window.RPlayer`, `window.RSearch`, `window.RUi`).

```
┌─────────────────────────────────────────────────────────────────────┐
│                         index.html (SPA shell)                      │
│                                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │
│  │ data.js  │  │ auth.js  │  │ store.js │  │  ui.js   │           │
│  │  (RDB)   │  │ (RAuth)  │  │ (RStore) │  │  (RUi)   │           │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │
│        │              │             │              │                │
│  ┌──────────┐  ┌──────────────────────────────────────────┐        │
│  │search.js │  │              player.js (RPlayer)          │        │
│  │(RSearch) │  │   Single <audio> element, full state mgmt │        │
│  └──────────┘  └──────────────────────────────────────────┘        │
│                                     │                               │
│                       ┌─────────────────────────┐                  │
│                       │   app.js (SPA Router)    │                  │
│                       │  Hash Router + Views +   │                  │
│                       │  Event Delegation        │                  │
│                       └─────────────────────────┘                  │
└─────────────────────────────────────────────────────────────────────┘
```

**Script loading order in index.html:**
```
data.js → auth.js → store.js → ui.js → search.js → player.js → app.js
```
Each module depends only on modules loaded before it.

---

## Module Breakdown

### `data.js` — Music Database

**Global export:** `window.RDB`

This module is the centralized music catalog. It seeds a static in-memory database of artists, albums, and songs, then caches this in `localStorage` under the key `resonance_db_v1`.

**What it contains:**
- **21 songs** across **6 genres** (Rock, Indie, Electronic, Soul, Lo-fi, Pop)
- **6 artists**: Neon Ember, Velvet Static, Crimson Atlas, Ada Solace, Midnight Fold, Glass Parade
- **6 albums**: Slow Burn, Wine & Static, Pulse Theory, Charcoal Hours, Fold Line, Parade
- **5 curated playlists** owned by "Resonance"
- **4 default user playlists** seeded to every new account on first sign-up

**Key functions exposed on `RDB`:**
| Function | Description |
|---|---|
| `getSongs()` | Returns all 21 songs |
| `getSong(id)` | Returns a single song by ID |
| `getArtists()` | Returns all 6 artists |
| `getArtist(id)` | Returns a single artist by ID |
| `getAlbums()` | Returns all 6 albums |
| `getAlbum(id)` | Returns a single album by ID |
| `getCuratedPlaylists()` | Returns the 5 Resonance-owned playlists |
| `songsFor(ids[])` | Resolves an array of song IDs to full song objects |
| `formatDuration(seconds)` | Converts seconds to "m:ss" string |
| `coverGradient(seed)` | Generates a deterministic CSS gradient for a given ID string (used as cover art) |

**Cover art generation:**
Since no image files are used, all cover art is generated via a `coverGradient()` function. It hashes the ID string into a number, picks two hues from a fixed palette, and returns a CSS `linear-gradient`. This ensures every card has a unique but visually consistent thumbnail without any image assets.

**Audio sources:**
9 real, freely streamable MP3 tracks from SoundHelix are cycled across the 21 songs using modulo arithmetic, giving every song a real playable audio file.

---

### `auth.js` — Authentication Engine

**Global export:** `window.RAuth`

A fully client-side authentication system that persists user credentials in `localStorage`. It is structured to mirror a real backend auth API — all public methods return Promises, so swapping in a real backend later would only require changing the implementations, not the callers.

**User data storage:**
- Users stored under key `resonance_users_v1` (a JSON object keyed by normalized email)
- Session stored under `resonance_session_v1`

**Password Security:**
Passwords are never stored in plaintext. The system uses a **salted SHA-256 hash** via the **Web Crypto API** (`crypto.subtle.digest`). A unique salt is generated per user (`email + ":" + timestamp`). If the browser doesn't support SubtleCrypto (very rare), a simple numeric fallback hash is used instead. Both paths return a Promise, so callers always see a uniform interface.

**Key methods on `RAuth`:**
| Method | Returns | Description |
|---|---|---|
| `signup(email, password, profileName)` | `Promise<{ok, error?, user?}>` | Validates inputs, hashes password, saves new user, creates session |
| `login(email, password)` | `Promise<{ok, error?, user?}>` | Verifies credentials by re-hashing password and comparing |
| `logout()` | `void` | Removes session from both `sessionStorage` and `localStorage` |
| `getSession()` | `object | null` | Returns the current session (userId, email, profileName) |
| `isLoggedIn()` | `boolean` | Quick check whether a session exists |
| `updateProfile(userId, changes)` | `{ok, user?}` | Updates profile name for a user |
| `validateEmail(email)` | `boolean` | Regex-based email format validation |
| `validatePassword(password)` | `boolean` | Checks minimum 6 characters |

---

### `store.js` — User Data Store

**Global export:** `window.RStore`

Per-user data isolated to each logged-in user. All data is stored in `localStorage` under a user-scoped key (`resonance_user_<userId>_v1`). No user can ever see another user's data.

**What is stored per user:**
- `likedSongs` — array of liked song IDs (newest first)
- `playlists` — array of playlist objects `{id, name, description, owner, songs[], created}`
- `recentlyPlayed` — array of `{songId, playedAt}` records (capped at 50 entries)
- `playCounts` — object mapping `songId → play count`
- `playerSettings` — `{volume, shuffle, repeatMode, queue, currentSongId, contextName}`

**Key methods on `RStore`:**
| Method | Description |
|---|---|
| `initUser(userId)` | Called on sign up. Seeds 4 default playlists from `RDB.getDefaultUserPlaylists()` |
| `toggleLike(songId)` | Adds or removes a song from Liked Songs; returns `{ok, liked}` |
| `getLikedSongs()` | Returns full song objects (via `RDB.songsFor`) for all liked IDs |
| `isLiked(songId)` | Returns `boolean` |
| `getPlaylists()` | Returns all user playlists |
| `createPlaylist(name, desc)` | Creates a new playlist; returns `{ok, playlist}` |
| `renamePlaylist(id, name)` | Renames a playlist; returns `{ok, playlist}` |
| `deletePlaylist(id)` | Deletes a playlist permanently |
| `addToPlaylist(playlistId, songId)` | Adds a song; returns error if already present |
| `removeFromPlaylist(playlistId, songId)` | Removes a specific song |
| `recordPlay(songId)` | Records a play, updates recently played list and play count |
| `getRecentlyPlayed(limit)` | Returns up to `limit` recently played song objects |
| `getRepeatRewind()` | Top 8 most-played songs sorted by play count |
| `getDailyMix()` | Up to 8 songs from the user's top 2 most-played genres |
| `getDiscoverWeekly()` | Up to 8 songs the user has **not** recently played |
| `getReleaseRadar()` | Top 8 most recently released songs (by year, descending) |
| `savePlayerSettings(partial)` | Persists player state for session restoration |
| `getStats()` | Returns `{playlists, liked, plays}` stats for the profile page |

---

### `player.js` — Playback Engine

**Global export:** `window.RPlayer`

The heart of Resonance. A **single `<audio>` element** is created and appended to the document body. All views communicate with this single instance — no view ever creates its own audio element.

**State managed internally:**
```js
{
  contextSongIds: [],     // The full ordered list of songs being played from
  contextName: null,      // e.g. "Liked Songs", "Daily Mix"
  playOrder: [],          // Shuffled or sequential permutation of context indices
  pointer: -1,            // Current position in playOrder
  manualQueue: [],        // Songs explicitly  queued by the user (played next)
  shuffle: false,
  repeatMode: "off",      // "off" | "context" | "song"
  volume: 0.8,
  muted: false,
  currentSong: null
}
```

**Key methods on `RPlayer`:**
| Method | Description |
|---|---|
| `playContext(songIds, startId, name)` | Loads a full context (playlist, album, etc.) and starts playing |
| `playSong(song)` | Plays a single song in a mini-context of length 1 |
| `togglePlay()` | Toggles play/pause |
| `next(auto)` | Advances to next song; respects manual queue, repeat mode, and shuffle |
| `prev()` | If >3s into song, seeks to start; otherwise goes back |
| `seekToFraction(fraction)` | Seeks to a position (0.0–1.0) of the track duration |
| `seekBy(deltaSeconds)` | Relative seek (used by keyboard shortcuts ±5s) |
| `setVolume(v)` | Sets volume 0.0–1.0; updates `muted` state automatically |
| `toggleMute()` | Toggles mute without changing volume value |
| `toggleShuffle()` | Shuffles/unshuffles the play order, keeping current song first |
| `cycleRepeat()` | Cycles through "off" → "context" → "song" → "off" |
| `enqueue(songId)` | Adds a song to the manual queue; shows a toast notification |
| `removeFromQueue(index)` | Removes a song from the manual queue by index |
| `clearQueue()` | Empties the entire manual queue |
| `restoreSettings()` | On page load: restores volume, shuffle, repeat, and last-played song (paused, per browser autoplay policy) |
| `on(callback)` | Registers a listener to receive player state snapshots on every change |
| `off(callback)` | Removes a listener |
| `getState()` | Returns a full state snapshot |
| `isCurrentSong(id)` | Whether the given ID is the current song (playing or paused) |
| `isCurrentlyPlaying(id)` | Whether the given ID is actively playing |

**Shuffle Algorithm:**
A **Fisher-Yates shuffle** is implemented to randomize the play order. When toggling shuffle on, the current song's index is always placed first so playback continues from the current position without interruption.

**Autoplay Promise Handling:**
`audio.play()` returns a Promise in modern browsers. The player carefully guards every call with `.catch()` to silently handle browser-level autoplay blocks (a very common issue in web audio). This prevents unhandled Promise rejections from crashing the app.

**Audio Events Listened to:**
- `timeupdate` — fires every ~250ms during playback to update the seek bar
- `loadedmetadata` — fires when the real audio duration is known
- `play` / `pause` — sync the play button icon
- `ended` — triggers next song logic (or repeat from start for "song" repeat mode)
- `error` — shows a toast if the audio file fails to load

**Keyboard Shortcuts:**
| Key | Action |
|---|---|
| `Space` | Play / Pause |
| `ArrowRight` | Seek forward 5 seconds |
| `ArrowLeft` | Seek backward 5 seconds |
| `M` | Toggle mute |
| `N` | Next song |
| `P` | Previous song |

Keyboard shortcuts are intelligently disabled when the user is typing in a text input or textarea.

---

### `search.js` — Search Engine

**Global export:** `window.RSearch`

A lightweight, client-side full-text search engine that searches across the entire catalog in real-time.

**`RSearch.run(query)`** returns:
```js
{
  songs: [],      // Matched by title, artist name, or album name
  artists: [],    // Matched by artist name
  albums: [],     // Matched by album title or artist name
  playlists: []   // Matched curated + user playlists by name
}
```

Search is case-insensitive and uses a simple `indexOf` substring match (normalized to lowercase). Results are displayed live with an `input` event listener — the UI updates on every keystroke with no debounce needed at this catalog size.

---

### `ui.js` — Shared UI Helpers

**Global export:** `window.RUi`

A shared utility module used on every page of the app. It provides reusable UI primitives.

**Toast Notifications:**
A `toast(message, kind)` function creates animated notification "toasts" (slide-up, fade-in/out) anchored to the bottom-right of the screen. Three kinds exist: `"success"` (green left border), `"error"` (red left border), `"info"` (blue left border). Toasts auto-dismiss after 3.2 seconds.

**Confirm Modal:**
`confirmModal(message, confirmLabel)` returns a **Promise** that resolves to `true` (if the user confirms) or `false` (if cancelled or dismissed). The modal is rendered dynamically into a single reusable overlay element. Clicking outside the modal or pressing `Escape` cancels it.

**SVG Icons:**
A centralized `ICONS` object holds inline SVG strings for all icons used across the app (heart, play, pause, next, prev, shuffle, repeat, volume, mute, queue, dots, close). Using inline SVGs allows them to be colored via CSS `currentColor`.

**Sidebar Auth Footer:**
`renderSidebarAuth()` checks the current session and renders either Sign In / Sign Up links or the user's profile name + Logout into the `[data-sidebar-auth]` element present on every page.

**`timeAgo(isoString)`:**
Converts an ISO date string to a human-readable relative time ("just now", "5m ago", "3h ago", "2d ago").

---

### `app.js` — SPA Shell & Router

**This is the main application file** — 765 lines that bind everything together.

**Hash Router:**
The app uses `window.location.hash` for client-side routing. Each URL fragment maps to a view:

| Hash Fragment | View Rendered |
|---|---|
| `#/home` | Home page with greeting, quick links, curated playlists, mixes, recently played |
| `#/search` | Live search page |
| `#/liked` | Liked Songs playlist (login required) |
| `#/playlist/:id` | Playlist detail page (curated or user-owned) |
| `#/artist/:id` | Artist detail page with popular songs and albums |
| `#/album/:id` | Album detail page with all tracks |
| `#/mix/:key` | Smart mix page (daily, discover, release, repeat) |
| `#/collection/popular` | All curated playlists |
| `#/collection/made-for-you` | All smart mixes |
| `#/profile` | User profile page with stats and name editor |
| `#/premium` | Premium plans comparison page |
| `#/queue` | Opens queue modal (redirects to `#/home`) |

**View Renderers:**
Each view is a function that generates and injects HTML into `#view-root`. Views are never painted with separate `.html` files — the entire app lives in a single `index.html`, and the router swaps view content dynamically.

**Event Delegation:**
Instead of attaching individual event listeners to every button, a **single click listener** is attached to `document.body`. It reads `data-action` attributes from the closest ancestor element. This pattern is extremely efficient and works even for dynamically rendered HTML.

Actions handled by delegation:
- `play-song` — Resolves context, then calls `RPlayer.playContext()`
- `play-context` — Plays a whole playlist, album, or mix
- `like` — Toggles liked status on a song
- `queue-add` — Adds a song to the manual queue
- `add-to-playlist` — Opens the "Add to Playlist" modal
- `remove-from-playlist` — Removes a song from a user playlist
- `open-artist` — Navigates to an artist page
- `open-album` — Navigates to an album page
- `create-playlist` — Opens the Create Playlist modal
- `rename-playlist` — Opens the Rename Playlist modal
- `delete-playlist` — Shows a confirm modal, then deletes
- `open-premium` — Navigates to the Premium page
- `fake-upgrade` — Shows a "demo only" info toast

**Player Bar Wiring:**
The persistent player bar at the bottom of the screen is wired directly to `RPlayer` events. `wirePlayerBar()` caches all DOM element references once on load, then registers a listener with `RPlayer.on(renderPlayerBar)` which fires on every state change. The player bar updates the seek bar, time displays, song title, artist, like button, shuffle/repeat active states, volume, and context label.

**Context Resolution:**
The `resolveContext(type, id)` function converts a context type & ID into a list of song IDs at click-time. This ensures that playing "Liked Songs" or "a Playlist" always starts from the current, up-to-date state of that list, not a stale snapshot.

---

### `style.css` — Design System

A 789-line CSS file that implements the entire visual design system from scratch.

**CSS Custom Properties (Design Tokens):**
```css
:root {
  --bg-dark:        black;
  --bg-card:        #181818;
  --bg-card-hover:  #282828;
  --theme-green:    #1DB954;   /* Spotify signature green */
  --text-main:      #ffffff;
  --text-muted:     #b3b3b3;
}
```

**Layout System:**
- **App shell**: CSS `flexbox` — sidebar fixed left, main content takes remaining width
- **Card grids**: CSS `grid` with `repeat(5, 1fr)` on desktop, adaptively collapsing on smaller screens
- **Quick access grid**: `repeat(3, 1fr)` on desktop, `repeat(2, 1fr)` on tablet
- **Player bar**: CSS `grid` with `1fr minmax(320px, 2fr) 1fr` column template
- **Song rows**: CSS `grid` with fixed columns for number, art, title, album, and actions

**Key UI Components Styled:**
- `.card` + `.card-thumb` + `.play-btn` (animated hover reveal with `translateY`)
- `.song-row` (hover state swaps number for play icon using `display:none` toggling)
- `.player-bar` (fixed bottom position, slides in/out with CSS `transform: translateY`)
- `.toast` + `.toast-show` (CSS transform + opacity animation)
- `.modal-overlay` + `.modal-box` (dark overlay + centered card)
- `.btn` (green pill button with scale hover) and `.btn-ghost` (outlined variant)
- `.seek-bar` + `.volume-bar` (custom `<input type=range>` styling cross-browser)
- `.profile-avatar` (initial-letter avatar with gradient background)
- `.plan-card` + `.plan-card-featured` (premium pricing card with green border highlight)

---

## Pages & Views

| Page / File | Type | Description |
|---|---|---|
| `index.html` | SPA Host | Renders all in-app views via hash routing |
| `signin.html` | Static Page | Login form - calls `RAuth.login()` via Promise |
| `signup.html` | Static Page | Registration form - calls `RAuth.signup()` via Promise |
| `logout.html` | Redirect | Calls `RAuth.logout()` then redirects to `signin.html` |
| `about.html` | Static Page | About Resonance - mission statement |
| `contact.html` | Static Page | Contact form with client-side validation and localStorage submission |

**In-app Views (rendered by `app.js`):**
- **Home** — Time-aware greeting, quick access links, curated playlists grid, smart mixes grid, recently played section (if logged in)
- **Search** — Live search with sectioned results (Songs, Artists, Albums, Playlists)
- **Liked Songs** — Full song list of liked tracks (login-gated)
- **Playlist Detail** — Cover, metadata, song list; Rename/Delete controls for owned playlists
- **Artist Detail** — Artist bio, top 5 popular songs, all albums in a grid
- **Album Detail** — Album artwork, year, tracklist
- **Mix Detail** — Algorithm-generated playlist with descriptive header
- **Profile** — Avatar, stats (playlists, liked songs, total plays), editable profile name
- **Premium** — Free vs Premium plan comparison with pricing cards

---

## Features In Detail

### 🔐 Authentication System

The authentication flow is entirely client-side and zero-backend:

1. **Sign Up**: User enters email, password (min 6 chars), and a display name. Client-side validation runs first. Then `RAuth.signup()` generates a unique salt, hashes the password with SHA-256 (Web Crypto API), stores the user in `localStorage`, creates a session, and seeds the new user's library with 4 default playlists (from `RDB.getDefaultUserPlaylists()`).

2. **Sign In**: User enters credentials. `RAuth.login()` retrieves the stored user by email, re-hashes the entered password with the stored salt, and compares. On match, a session is written and the app redirects to Home.

3. **Session Management**: The session object `{userId, email, profileName}` is written to `localStorage`. On every page load, `RAuth.getSession()` reads it. `ui.js` uses this to render the correct sidebar footer (Sign In/Sign Up vs. username + Logout).

4. **Logout**: `RAuth.logout()` removes the session key from both `localStorage` and `sessionStorage` to handle edge cases.

5. **Auth Guards**: Routes like Liked Songs and Profile check `RAuth.isLoggedIn()` before rendering. If unauthenticated, an empty state with a "Sign In" button is shown. Interactive actions (like, add to playlist, create playlist) show a toast: "Log in to do that."

---

### 🎵 Music Playback Engine

The player operates as a **stateful singleton**:

- Clicking "Play" on any song, album, artist, playlist, or mix calls `RPlayer.playContext(songIds, startId, contextName)`, which loads the full context into player state and starts from the selected song.
- The player tracks a **pointer** into the **play order** (shuffled or sequential). "Next" and "Prev" advance or move back this pointer.
- **Shuffle** recalculates the play order using Fisher-Yates while keeping the current song first.
- **Repeat** has three modes: `off` (stop at end), `context` (loop the playlist), `song` (loop one song). Cycling is implemented by rotating through an ordered string array.
- **Seek bar**: An `input[type=range]` is mapped 0–1000 to represent 0%–100% of the track. A `seeking` flag prevents the bar from jumping while the user is dragging.
- **Volume**: `input[type=range]` 0–100 mapped to 0.0–1.0 for `audio.volume`.
- Every state change emits a **snapshot** to all registered listeners. `app.js` uses this to re-render the player bar in real-time.
- **Session Restoration**: On page load, the last-played song is restored to the player bar in a loaded-but-paused state (respecting browser autoplay policies).

---

### 📂 Playlist Management

- **Create**: Via the sidebar "+" button or the "New Playlist" button inside the Add to Playlist modal. A modal form collects name and description.
- **Rename**: Available on user-owned playlist detail pages via a "Rename" button with a modal form.
- **Delete**: A "Delete" button shows a **Promise-based confirm modal**. Only on user confirmation does `RStore.deletePlaylist()` execute.
- **Add Song**: Each song row has a "⋯" (dots) button opening a modal listing all user playlists. Songs already in a playlist are marked "Added".
- **Remove Song**: On user-owned playlist detail pages, each song row has an "✕" remove button.
- **Ownership**: Curated playlists (owned by "Resonance") show no edit controls. Only playlists with `editable: true` (i.e., user-created) expose Rename/Delete/Remove actions.

---

### ❤️ Liked Songs

- Toggling the heart icon on any song row or on the player bar calls `RStore.toggleLike(songId)`.
- The Liked Songs page at `#/liked` shows all liked songs as a full playlist, including a large play button that plays the entire collection.
- Like status is reflected live across all views — clicking like in the search results updates the player bar icon simultaneously via `renderRoute()` + `renderPlayerBar()`.

---

### 🔎 Search

- The Search view (`#/search`) renders a text input and live results.
- An `input` event fires `RSearch.run(query)` on every keystroke.
- Results are grouped by type: **Songs** (max 10), **Artists** (chips), **Albums** (cards), **Playlists** (cards).
- Artists are shown as clickable "chips" that navigate to the artist detail page.
- Albums and playlists are shown as hoverable cards with an overlay play button.
- If no results are found, an appropriate empty state message is shown.
- Search results also form a **playback context** — playing any song from results sets the context to all matched song IDs so skip/prev works correctly within the results.

---

### 🎲 Smart Mixes (Personalization)

Four algorithmically generated playlists provide a personalization experience without any ML backend:

| Mix | Algorithm |
|---|---|
| **Daily Mix** | Filters all songs by the user's top 2 most-played genres (by play count). Falls back to all songs if no history. |
| **Discover Weekly** | Returns songs the user has **not** recently played. Shows fresh content. |
| **Release Radar** | Returns the 8 newest songs sorted by release year (descending). |
| **Repeat Rewind** | Returns the 8 songs with the highest play counts — the user's all-time favorites. |

---

### 📋 Queue Management

- Any song can be added to the **manual queue** via the queue icon (≡+) in each song row.
- The Queue button in the player bar opens a **Queue Modal** showing:
  - Now Playing (current song)
  - Next in Queue (manually queued songs, removable)
  - "Clear Queue" button (if queue is non-empty)
  - Next from Context (the upcoming songs from the active playlist/album)
- Individual queued songs can be removed. The modal re-renders in-place without closing.
- The queue takes priority over the context — `RPlayer.next()` always drains the manual queue before advancing in the context.

---

### 👤 User Profile

The Profile page (`#/profile`) is login-gated and shows:
- **Avatar**: Generated from the first letter of the profile name on a green gradient circle
- **Display name and email**
- **Stats**:
  - Number of playlists created
  - Number of liked songs
  - Total play count across all songs
- **Edit form**: Allows the user to change their profile display name. On save, `RAuth.updateProfile()` updates both the user record and the active session, then re-renders the sidebar auth footer live.

---

### 💎 Premium Page

The Premium page (`#/premium`) presents a fictional pricing comparison:
- **Free plan**: Ad-supported, standard quality, unlimited playlists
- **Premium plan ($9.99/mo)**: Ad-free, high quality, "offline-ready design", unlimited skips

Clicking "Upgrade" shows: *"This is a demo — no payment was charged."* — an honest disclosure that no payment is processed.

---

### 📞 Contact Form

The Contact page (`contact.html`) includes:
- Name, Email, and Message fields with client-side validation
- Per-field inline error messages
- Since there is no backend, submissions are stored in `localStorage` under `resonance_contact_submissions_v1`
- A green success banner is shown on submission, the form resets, and a toast notification fires

---

## JavaScript Concepts Used

### Promises

Used in three different patterns throughout the codebase:

**1. `new Promise(resolve)` — Wrapping async user interaction (ui.js)**
```js
function confirmModal(message, confirmLabel) {
    return new Promise(function (resolve) {
        // Modal renders, user clicks Cancel or Confirm
        cancelBtn.onclick = function () { resolve(false); };
        confirmBtn.onclick = function () { resolve(true); };
    });
}
```

**2. `Promise.resolve()` — Synchronous early returns in an async interface (auth.js)**
```js
signup: function (email, password, profileName) {
    if (!validateEmail(email)) return Promise.resolve({ ok: false, error: "..." });
    if (!validatePassword(password)) return Promise.resolve({ ok: false, error: "..." });
    // ...
    return hashPassword(password, salt).then(function (hash) { /* ... */ });
}
```

**3. `.then()` — Chaining on Web Crypto API Promises (auth.js)**
```js
return global.crypto.subtle.digest("SHA-256", enc).then(function (buf) {
    return Array.from(new Uint8Array(buf))
        .map(function (b) { return b.toString(16).padStart(2, "0"); }).join("");
});
```

**4. `.catch()` — Handling browser autoplay rejection (player.js)**
```js
var playPromise = audio.play();
if (playPromise && playPromise.catch) {
    playPromise.catch(function (err) {
        RUi.toast("Couldn't play. Tap play again.", "error");
    });
}
```

---

### IIFE (Immediately Invoked Function Expression)

Every module is wrapped in an IIFE to create a private scope:
```js
(function (global) {
    "use strict";
    // private variables and functions here
    global.RDB = { /* public API */ };
})(window);
```
This prevents variable name collisions between modules without needing a bundler or ES Modules.

---

### Event Delegation

Instead of hundreds of individual `addEventListener` calls:
```js
document.body.addEventListener("click", function handleDelegatedClick(e) {
    var actionEl = e.target.closest("[data-action]");
    if (!actionEl) return;
    var action = actionEl.getAttribute("data-action");
    switch (action) {
        case "play-song": /* ... */ break;
        case "like":      /* ... */ break;
        // etc.
    }
});
```
This single listener handles all interactive actions across dynamically rendered views.

---

### Module Pattern

Each file exposes exactly one global object with a clean public API. Internal implementation details (helper functions, state variables) are invisible to other modules.

---

### Hash-Based Client-Side Routing (SPA Router)

```js
window.addEventListener("hashchange", renderRoute);

function renderRoute() {
    var parts = location.hash.replace(/^#/, "").split("/").filter(Boolean);
    var root = parts[0]; // e.g. "playlist"
    var id = parts[1];   // e.g. "pl_abc123"
    switch (root) {
        case "home":     viewHome();          break;
        case "playlist": viewPlaylist(id);    break;
        case "artist":   viewArtist(id);      break;
        // etc.
    }
}
```
The entire app is a single HTML file. Navigation changes the URL hash, triggering `renderRoute()`, which renders new content into `#view-root`.

---

### Observer / Pub-Sub Pattern

`RPlayer` implements a lightweight observer pattern:
```js
var listeners = [];
function emit() {
    var snapshot = { /* current state */ };
    listeners.forEach(function (cb) { cb(snapshot); });
}

RPlayer.on(renderPlayerBar);   // subscribe
RPlayer.off(renderPlayerBar);  // unsubscribe
```
Any number of UI components can subscribe to player state changes without coupling to internal player logic.

---

### localStorage Persistence

All data is persisted using `localStorage`:
- `resonance_db_v1` — Music catalog (songs, artists, albums)
- `resonance_users_v1` — All registered user credentials
- `resonance_session_v1` — Current login session
- `resonance_user_<userId>_v1` — Per-user data (likes, playlists, history)
- `resonance_contact_submissions_v1` — Contact form submissions

Robust try/catch wrappers on every read/write operation prevent crashes if storage is full or unavailable.

---

### Web Crypto API

```js
var enc = new TextEncoder().encode(salt + ":" + password);
return global.crypto.subtle.digest("SHA-256", enc).then(function (buf) {
    return Array.from(new Uint8Array(buf))
        .map(function (b) { return b.toString(16).padStart(2, "0"); })
        .join("");
});
```
The browser's built-in `crypto.subtle.digest()` is used to produce a hex-encoded SHA-256 hash of the salted password. A numeric fallback handles environments where SubtleCrypto is not available.

---

### DOM Manipulation & innerHTML Rendering

Views are rendered by building HTML strings and assigning them to `viewRoot.innerHTML`. An `escapeHtml()` function sanitizes all user-supplied data before interpolation into HTML strings to prevent XSS:

```js
function escapeHtml(str) {
    return String(str).replace(/[&<>"']/g, function (c) {
        return { "&": "&amp;", "<": "&lt;", ">": "&gt;", '"': "&quot;", "'": "&#39;" }[c];
    });
}
```

---

### Keyboard Shortcuts

```js
document.addEventListener("keydown", function (e) {
    var tag = (document.activeElement && document.activeElement.tagName || "").toLowerCase();
    if (tag === "input" || tag === "textarea") return; // don't hijack while typing
    switch (e.key) {
        case " ":           e.preventDefault(); RPlayer.togglePlay(); break;
        case "ArrowRight":  RPlayer.seekBy(5);  break;
        case "ArrowLeft":   RPlayer.seekBy(-5); break;
        case "m": RPlayer.toggleMute(); break;
        case "n": RPlayer.next();       break;
        case "p": RPlayer.prev();       break;
    }
});
```

---

## Data Model

### Song Object
```js
{
    id: "s1",
    title: "Ember Road",
    artistId: "ar1",
    artist: "Neon Ember",
    albumId: "al1",
    album: "Slow Burn",
    cover: "linear-gradient(...)",   // generated CSS gradient
    audio: "https://soundhelix.com/...",
    duration: 214,                   // seconds
    genre: "Rock",
    year: 2024
}
```

### User Data Object
```js
{
    likedSongs: ["s1", "s3"],
    playlists: [{
        id: "pl_abc123",
        name: "Late Night Drive",
        description: "For the long way home.",
        owner: "you",
        songs: ["s15", "s2", "s13"],
        created: "2026-10-01T..."
    }],
    recentlyPlayed: [{ songId: "s5", playedAt: "..." }],
    playCounts: { "s5": 12, "s3": 7 },
    playerSettings: {
        volume: 0.8,
        shuffle: false,
        repeatMode: "off",
        currentSongId: "s5",
        contextName: "Liked Songs"
    }
}
```

---

## Responsive Design

Three breakpoints implemented:

| Breakpoint | Layout Changes |
|---|---|
| **≤ 1024px (Tablet)** | Sidebar narrows to 200px; card grid to 3 columns; context label hidden from player bar |
| **≤ 768px (Mobile)** | Sidebar slides off-screen (hamburger menu toggle); card grids become horizontal scrollable rows; player bar stacks vertically; album column hidden from song rows; sidebar toggled with `classList.toggle("show")` |

The mobile top bar (hamburger + logo) is hidden on desktop via `display: none`, shown only on mobile.

---

## Security Considerations

| Concern | Mitigation |
|---|---|
| **Password storage** | SHA-256 salted hashing via Web Crypto API. Passwords never stored in plaintext. |
| **XSS prevention** | All user data rendered into HTML goes through `escapeHtml()` first |
| **Session isolation** | User data is scoped by `userId`; session is read-only from `localStorage` |
| **No real backend** | All data is local — no server calls, no real personal data transmitted |
| **Autoplay** | `audio.play()` Promise is caught to handle browser-level autoplay blockage |

⚠️ **Note:** As a client-side-only demo, `localStorage` data is accessible to any JS running on the same origin. This is appropriate for a demo but not for production use without a real backend.

---

## Project File Structure

```
spotify-clone-FEE-project-v2-main/
│
├── index.html          # SPA host — sidebar, player bar, view-root, script tags
├── signin.html         # Login page (standalone)
├── signup.html         # Registration page (standalone)
├── logout.html         # Logout redirect page
├── about.html          # Static about page
├── contact.html        # Contact form page
├── 1.html              # (Additional supporting page)
│
├── data.js             # Music catalog database (RDB)
├── auth.js             # Authentication engine (RAuth)
├── store.js            # Per-user data store (RStore)
├── player.js           # Audio playback engine (RPlayer)
├── search.js           # Full-text search engine (RSearch)
├── ui.js               # Shared UI helpers — toasts, modals, icons (RUi)
├── app.js              # SPA router + view renderers + event delegation
│
└── style.css           # Complete design system — 789 lines of Vanilla CSS
```

---

## Known Limitations & Future Improvements

| Current Limitation | Potential Improvement |
|---|---|
| No real backend — all data is local | Add a Node.js/Express + PostgreSQL backend |
| Passwords stored (hashed) in localStorage | Move auth to a real server with JWT/session cookies |
| Audio files are demo SoundHelix tracks | Integrate Spotify API or upload real audio files |
| No ES Modules / bundler | Migrate to Vite for a proper dev/build pipeline |
| No `async/await` syntax | Refactor Promises to use modern `async/await` for cleaner code |
| Search has no debounce | Add 150ms debounce for large catalogs |
| No PWA / offline support | Add a Service Worker for offline capability |
| Cover art is gradient only | Integrate real album artwork via an API |
| No social features | Add following, sharing playlists, etc. |
| Contact form has no real submission | Wire up an email service (EmailJS, FormSpree, etc.) |

---

© 2026 Resonance — Team No. 21 | Front-End Engineering Project
