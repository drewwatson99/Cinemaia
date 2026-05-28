# CINEMANIA

A peer-to-peer movie nerd party game. Inspired by [Cinephile: A Card Game](https://www.cinephilegame.com/). One self-contained `index.html`. No backend, no accounts, no API keys, no setup. Share via text.

## Five modes + custom rules

| Mode | Difficulty | What you do |
|---|---|---|
| **Filmography** | Easy | Draw an actor card. Take turns naming films featuring them. Stumped = out. Last player standing wins +1. |
| **Head On** | Easy | Card on your forehead, friends give you 60 seconds of clues. +1 per correct guess, −1 per pass. Rotate. |
| **Movie / Actor** | Intermediate | Chain Movie → Actor → Movie → Actor. No repeats. Stumped = out. Last standing wins. |
| **Take Six** | Intermediate | Each player gets 6 cards. Play one per turn by connecting it to the anchor card via Six Degrees. First to empty hand wins; lower degrees = lower score. |
| **Six Degrees** | Expert | Two cards drawn. First player to submit a valid connection chain (≤6 degrees) wins the round. |
| **Custom Rules** | Anything | Write your own game title + rules text. App draws cards + tracks scores (manual ±1 buttons). |

## ~340 actor deck with preset filters

Bundled with the app — no key, no setup. Each card gets a unique colorful gradient based on the actor's name.

Host can filter the deck by **preset** before each game:

- Full deck (all cards)
- The 2010s (2010–2019)
- Modern era (2000+)
- Last 5 years
- Classic Hollywood (pre-1980)
- Oscar contenders only
- Comedy night
- Action movies
- Dramas
- Horror & thrillers
- Romance
- Sci-fi & fantasy
- Actors born 1960 or later
- Actors born 1980 or later

A live "X cards match" counter shows how big your filtered deck is before you start.

---

## 🚀 Get it live (Netlify Drop — 30 seconds)

The fastest, easiest path. Free. HTTPS by default.

1. Open [**app.netlify.com/drop**](https://app.netlify.com/drop) in your browser
2. Drag the entire **Cinephile Game** folder onto the page (the folder, not just the file)
3. Wait ~10 seconds — Netlify gives you a URL like `https://wonderful-tesla-abc123.netlify.app/`
4. **Bookmark that URL.** That's your game's home address.

If you'd like a custom subdomain (e.g. `cinemania-drew.netlify.app`):
- Sign up free at netlify.com (the deploy is anonymous until you claim it)
- Site Settings → Change site name → pick something memorable
- Optional: point a real domain like `movienight.drew.com` at it (Site Settings → Domain Management)

### Alternative hosts

- **Cloudflare Pages** — `pages.cloudflare.com`, drag the folder, similar to Netlify
- **GitHub Pages** — push the folder to a repo, Settings → Pages → enable on `main` branch
- **Vercel** — `vercel.com`, drag folder

All of these give you free HTTPS.

---

## 📲 How to share with friends

### Quick version

1. Open your Netlify URL on your phone/laptop
2. Tap **Create a new room**
3. Tap **Copy share link** — this gives you a URL like
   `https://yoursite.netlify.app/?room=K7QM3X`
4. Paste that link into your group chat
5. Friends tap it → it opens straight to the join screen with the code pre-filled → they just enter their name and join

### Word-for-word text template

> Hey, made a movie nerd game we can play tonight 🎬
> Open this and tap Join: https://yoursite.netlify.app/?room=K7QM3X

That's it. The room code is in the URL, so they don't have to type anything.

### Tips for the host

- The room dies when **you** close your tab — so keep the tab open during play
- You can switch game modes and presets between rounds without making a new room (Final Cut → "Play another game")
- 4–6 players is the sweet spot for most modes
- For Head On, only the holder should see their phone; everyone else sees the card
- For Six Degrees / Take Six chains, agree up front what counts (the original Cinephile rules say "cameos count, animated films and voiceovers do not")

---

## 🔒 Security & privacy

| Concern | What's actually happening |
|---|---|
| **No API keys, no third-party data services** | Deck is fully bundled in the file. Nothing leaves the page. |
| **Player names + scores** | Live only during the session. Nothing persists when the room closes. |
| **Game traffic** | Peer-to-peer via WebRTC, encrypted in transit (DTLS). The PeerJS broker only helps with the initial handshake; it never sees game data. |
| **Random people guessing your room code** | Room codes are 6 characters from a 32-letter alphabet → about 1 billion combinations. Practically unguessable. |
| **Malicious peer spamming** | Host-side rate limiter caps each guest at 20 messages/second; excess is silently dropped. |
| **XSS / injection** | All user-typed text (names, chains, custom rules) is rendered via `textContent`, never `innerHTML`. |
| **Tracking / analytics** | None. Only third-party scripts: PeerJS (CDN unpkg.com) and Google Fonts. |
| **Persistent data** | Only your name in localStorage on your own device. Cleared if you clear browsing data. |

**One thing to know:** in Custom Rules mode, any player in the room can tap ±1 to change anyone's score (honor system, by design). Don't share custom-mode links with strangers.

---

## Known limitations

- WebRTC can be flaky on restrictive corporate / hotel Wi-Fi (NAT). Home Wi-Fi and cellular work fine.
- The room dies when the host closes their tab — trade-off for the no-backend design.
- Honor-system voting for Six Degrees / Take Six chains: agree on rules before starting, especially around animation / cameos.
