# CINEMANIA — Game Documentation & Developer Guide

## Overview

CINEMANIA is a browser-based multiplayer movie trivia party game. It runs entirely client-side as a single HTML file, using **PeerJS** (WebRTC) for peer-to-peer multiplayer — no server required.

**Live URL:** Host the `index.html` file anywhere (GitHub Pages, Netlify, any static host) and share the link.

---

## Architecture

| Component | Technology |
|-----------|-----------|
| Frontend | Vanilla JS, single `index.html` file |
| Styling | CSS custom properties, responsive design |
| Multiplayer | PeerJS (WebRTC peer-to-peer) |
| Data | Built-in JSON deck (no external API needed) |
| Hosting | Any static file server |

### How Multiplayer Works

1. **Host** creates a room → registers a PeerJS peer ID (`cinemania-room-XXXXXX`)
2. **Guests** join by connecting to that peer ID via the PeerJS signaling server
3. All game state lives on the host and is broadcast to guests
4. Guests send actions (submit chain, vote, name film, etc.) as messages to the host
5. ICE servers (STUN + TURN) handle NAT traversal for players on different networks

### Connection Flow
```
Guest → PeerJS Signaling Server → finds Host peer
Guest ←→ Host (direct WebRTC data channel, relayed via TURN if needed)
```

---

## Game Modes

### 1. Six Degrees (Expert)
- Two actor cards are drawn
- Players race to connect them through shared films in ≤ 6 steps
- First valid submission wins; others vote to accept/reject
- Scoring: +1 per accepted chain

### 2. Filmography (Easy)
- One actor card is drawn
- Players take turns naming films featuring that actor
- If you can't name one, you're eliminated for the round
- Last player standing must name one more film to win

### 3. Head On (Easy)
- One player holds their phone to their forehead (card facing outward)
- Other players give 60-second clues (no saying the actor's name or film title)
- Guesser taps "Got it!" or "Pass"

### 4. Movie / Actor (Intermediate)
- A chain game: start with an actor, name a film → name an actor from that film → name a different film, etc.
- No repeats allowed
- If you're stumped, you're out

### 5. Take Six (Intermediate)
- Each player gets 6 cards in hand
- On your turn, play a card and explain how its actor connects to the anchor card via Six Degrees
- Others vote on validity
- First to empty their hand wins (lower cumulative degrees = tiebreaker)

### 6. Custom (Anything)
- Host writes custom rules
- App just tracks cards + scores
- Players use the built-in deck however they want

---

## Data Structure

### Actor Card
Each card in `game-data.json` → `actors[]` has:

```json
{
  "actor": "Tom Hanks",
  "film": "Forrest Gump",
  "year": 1994,
  "actorBorn": 1956,
  "genres": ["drama", "romance"],
  "oscar": true
}
```

| Field | Type | Description |
|-------|------|-------------|
| `actor` | string | Full actor name (must be unique in deck) |
| `film` | string | One notable film for the card face |
| `year` | number | Film release year |
| `actorBorn` | number | Actor's birth year (for filtering) |
| `genres` | string[] | Genre tags for the film |
| `oscar` | boolean | Whether film/actor was Oscar-nominated |

### Hint Data
Each entry in `game-data.json` → `hints` has:

```json
{
  "Actor Name": {
    "extraFilms": ["Film 1", "Film 2", ...],
    "coStars": ["Actor A", "Actor B", ...]
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `extraFilms` | string[] | Additional films by this actor (shown as hints) |
| `coStars` | string[] | Famous actors they've worked with (up to 10) |

---

## How to Edit the Data

### Adding a New Actor
1. Open `game-data.json`
2. Add a new object to the `actors` array
3. Make sure the `actor` name is unique (duplicates are auto-removed)
4. Optionally add hint data in the `hints` object

### Adding Hints for an Existing Actor
1. Find or create their entry in the `hints` object
2. Add `extraFilms` (other movies they're in) and `coStars` (famous people they've worked with)

### Important: The JSON file is for reference/editing
The game currently reads from the inline `BUILTIN_DECK` and `ACTOR_HINTS` in `index.html`. To sync changes from `game-data.json` into the game, copy the data into the corresponding arrays in `index.html`. (Future versions could load from the JSON directly.)

---

## Preset Filters

Presets filter the deck before each game:

| ID | Filter Logic |
|----|-------------|
| `all` | No filter — full deck |
| `2010s` | year 2010–2019 |
| `modern` | year ≥ 2000 |
| `classic` | year < 1980 |
| `recent` | year ≥ 2019 |
| `oscar` | oscar === true |
| `comedy` | genres includes "comedy" |
| `action` | genres includes "action" |
| `drama` | genres includes "drama" |
| `horror` | genres includes "horror" or "thriller" |
| `romance` | genres includes "romance" |
| `scifi` | genres includes "sci-fi" or "fantasy" |
| `modern_actors` | actorBorn ≥ 1960 |
| `young_actors` | actorBorn ≥ 1980 |

---

## Skip & Hint System

Players can access hints during gameplay:

- **Skip**: Draws a new card (no penalty, but round/time still passes)
- **Show More Films**: Reveals 6-8 other films by the current actor
- **Show Co-Stars**: Reveals up to 10 famous actors they've previously worked with

Hints are shown locally only (each player sees their own hint state — it's not broadcast to other players).

---

## Networking Details

### ICE Servers (for NAT traversal)
The game uses:
- Google STUN servers (`stun.l.google.com`)
- Metered.ca TURN servers (free tier, relays traffic when direct connection fails)

### Connection Retry Logic
- Guests retry up to 3 times with 2-second delays
- Each attempt has an 8-second timeout
- Auto-reconnect on signaling server disconnect

### PeerJS IDs
- Host: `cinemania-room-{ROOM_CODE}` (e.g., `cinemania-room-ABC234`)
- Guest: `cinemania-room-guest-{random8chars}`

---

## Deployment

### GitHub Pages
1. Push `index.html` to a GitHub repo
2. Enable GitHub Pages in Settings → Pages → deploy from branch
3. Share the Pages URL with friends

### Any Static Host
Just serve `index.html`. No build step, no dependencies, no server.

---

## File Structure

```
├── index.html        # Complete game (HTML + CSS + JS, all-in-one)
├── game-data.json    # Editable actor data, hints, and config (reference file)
├── GAME-INFO.md      # This documentation file
└── README.md         # Brief project readme
```

---

## Tech Stack & Dependencies

| Dependency | Version | CDN |
|-----------|---------|-----|
| PeerJS | 1.5.4 | unpkg.com/peerjs@1.5.4/dist/peerjs.min.js |
| Google Fonts | — | Bagel Fat One, DM Sans, Fraunces |

No npm, no build tools, no framework.

---

## Stats

- **Total actors in deck:** ~210 (after deduplication)
- **Actors with hints:** ~40 (most popular/recognizable)
- **Genres covered:** 16
- **Decades covered:** 1920s through 2020s
- **Supported players:** 1–20+ (no hard limit, but works best with 2–8)
