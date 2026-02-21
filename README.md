# CONSENSIFY
### San Viceroy Public Consensus Gate

> *"The city only moves when people publicly agree."*

A civic behavior modulation system built for the IETE Hackathon. Consensify turns collective agreement into visible, live infrastructure — citizens lock tokens behind public stances, and the city unlocks resources only when enough people commit. No force. No punishment. Only mutual dependency and social proof.

---

## The Concept

Citizens of San Viceroy behave like NPCs: they ignore official warnings, trust influencers over authorities, and react emotionally to rumors. Traditional public awareness campaigns fail because people don't feel involved.

Consensify solves this by treating citizens as players, not users. Instead of broadcasting instructions, the system creates **Consensus Gates** — public thresholds that unlock real city resources when enough citizens visibly opt in to a collective stance. The mechanic taps three proven behavioral levers simultaneously:

- **Social proof** — citizens can see the bar filling in real time
- **Loss aversion** — the unlock only happens if *enough* people commit
- **Identity signaling** — locking a token is a public act of reputation

The result is a coordination engine that feels like a game but functions like civic infrastructure.

---

## Features

### Consensus Board
The main public dashboard. Shows three live consensus cards per district — **Calm**, **Verify**, and **Aid** — each with animated percentage bars, threshold markers, and real-time state. When a stance crosses 60%, the card enters `active-protocol` state with a glitch animation. A live commitment stream logs every token lock publicly.

### Token Wallet System
Each citizen holds 3 tokens per type (Calm, Verify, Aid). Tokens are not currency — they are **public commitments**. Locking a token consumes it and boosts that stance's percentage. The wallet depletes visually in the header as tokens are spent, creating a meaningful resource constraint.

### Anti-Gaming Mechanics
Two systems prevent token farming and manipulation:

- **30-second cooldown throttle** between token locks of the same type, with a live countdown on the button
- **Diminishing returns** — each successive lock of the same type in the same district loses 20% effectiveness, capped at +15% max boost per session, shown as a visible indicator on the card

### Influencer Catalyst Layer
Three named influencers (Vex_Neon, Truth_Leak, Zero_Day) each back a stance. When an active influencer's stance matches your token lock, you receive a **1.5x momentum multiplier**. Influencers can't command outcomes — they can only accelerate existing momentum. A dedicated Catalysts page shows each influencer's bio, current stance, and momentum meter.

### District System
The city is divided into districts (Old Dock, Midtown), each with independent consensus state. Citizens toggle between districts on the board. A dedicated Districts page shows per-district breakdowns with individual bars for all three stances, mood descriptors, and a mini event feed per district.

### Social Entropy Engine
A `requestAnimationFrame` loop applies continuous micro-jitter (±0.3%) to all district values every second, simulating organic public sentiment drift. A separate background nudge fires every 4 seconds, adding small random increments to mimic citizens joining passively. The board never feels static.

### Commit Flow
A structured two-step commitment page: choose district → choose token type → preview the public consequences of your commitment → confirm and lock. The preview text updates live as selections change, making the stakes explicit before committing.

### Protocol Status Timer
When a gate activates (crosses 60%), a protocol status indicator appears on the board with a live elapsed timer showing how long the consensus has been active.

### Screen Mode
A toggle in the navigation bar hides all UI chrome (nav, wallet, commit buttons) and scales the consensus board for projection onto a large display. Designed for jury demos and public city screens.

### Transparency Page
An in-product ethics declaration covering: no personal data collection, no behavioral profiling, no identity verification, voluntary participation only, and a plain-English explanation of all token mechanics and influencer rules. This is part of the product, not a footnote.

---

## Pages

| Page | Route | Purpose |
|---|---|---|
| Board | `#board` | Main live consensus view with gate cards and commitment stream |
| Commit | `#commit` | Structured token commitment flow |
| Districts | `#districts` | Per-district breakdown and mood tracking |
| Catalysts | `#influencers` | Influencer profiles, stances, and momentum meters |
| Transparency | `#transparency` | Ethical design documentation and mechanic rules |

---

## Token Mechanics Reference

| Rule | Detail |
|---|---|
| Wallet size | 3 tokens per type per session |
| Base boost per lock | +15% to district stance |
| Influencer multiplier | 1.5x if active influencer backs same stance |
| Cooldown | 30 seconds between locks of same type |
| Diminishing returns | −20% effectiveness per successive lock |
| Minimum boost | 0% (never negative) |
| Maximum boost | 15% per lock (capped) |
| Threshold | 60% for all stances |
| Wallet refresh | Page reload |

---

## City State Data Model

```
CITY_STATE
├── districts
│   ├── Old Dock  { calm, verify, aid }
│   └── Midtown   { calm, verify, aid }
├── global        { calm, verify, aid }   ← weighted average
├── currentDistrict
├── lockThrottle  { calm, verify, aid }   ← timestamp per type
├── diminishingReturns { district: { type: amount } }
├── influencerMomentum { name: { stance, active, momentum } }
├── protocolStartTime
├── baseValues    { district: initial snapshot }
└── commitTrail   [ log entries ]

TOKEN_WALLET
├── calm: 3
├── verify: 3
└── aid: 3
```

---

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                   CONSENSIFY (SPA)                   │
│                  Single HTML File                    │
└──────────────────────────┬──────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
   │  Hash Router│  │  CITY_STATE │  │TOKEN_WALLET │
   │  #board     │  │  (in-memory)│  │  (in-memory)│
   │  #commit    │  │             │  │             │
   │  #districts │  └──────┬──────┘  └──────┬──────┘
   │  #influencers│        │                │
   │  #transparency        ▼                ▼
   └─────────────┘  ┌─────────────────────────────┐
                    │      RENDER FUNCTIONS        │
                    │  renderCards()               │
                    │  renderInfluencers()          │
                    │  renderHeatMeters()           │
                    │  renderGlobal()              │
                    │  renderWallet()              │
                    │  renderCommitTrail()         │
                    │  renderDistrictGrid()        │
                    └──────────────┬──────────────┘
                                   │
                    ┌──────────────▼──────────────┐
                    │       SIMULATION LOOPS       │
                    │                              │
                    │  requestAnimationFrame       │
                    │  → socialEntropy() (1s)      │
                    │  → checkGateStatus()         │
                    │  → updateThrottleButtons()   │
                    │  → updateProtocolTimer()     │
                    │                              │
                    │  setInterval (4s)            │
                    │  → external nudge            │
                    └──────────────────────────────┘
```

---

## How to Run

Consensify is a single self-contained HTML file with no build step, no dependencies, and no server required.

```bash
# Option 1 — open directly in browser
open consensify.html

# Option 2 — serve locally (avoids any browser restrictions)
npx serve .
# then visit http://localhost:3000/consensify.html

# Option 3 — Python simple server
python3 -m http.server 8080
# then visit http://localhost:8080/consensify.html
```

No npm install. No API keys. No database. Opens and runs immediately.

---

## Demo Script (Jury Walkthrough)

**Setup:** Open `consensify.html` in Chrome. Click **SCREEN MODE** to project the board cleanly.

**Step 1 — Establish the scenario**
Point to the Board page. Old Dock district is selected. Three stances are live — Calm at ~45%, Verify at ~30%, Aid at ~25%. Explain: "The city unlocks resources only when 60% of citizens publicly commit to a stance."

**Step 2 — Show the entropy**
Wait 10 seconds. The bars drift slightly on their own. "This is organic public sentiment — the city is never static."

**Step 3 — Lock a token**
Navigate to the **Commit** page. Select Old Dock → Calm → confirm. "I've locked a Calm token. Watch the bar." Return to Board. Calm has jumped.

**Step 4 — Show diminishing returns**
Try to lock another Calm token immediately. The button shows "COOLDOWN 30s". "The system prevents token farming — 30 second throttle, and each successive lock is 20% less effective."

**Step 5 — Activate an influencer**
Navigate to **Catalysts**. Show Vex_Neon backing Calm with 65% momentum. "When I lock a token that matches an active influencer's stance, I get a 1.5x boost. Influencers accelerate momentum — they can't command it."

**Step 6 — Watch threshold approach**
Return to Board. Calm is approaching 60%. The bar starts pulsing (redline animation). "The crowd is feeling the momentum."

**Step 7 — Threshold breach**
When Calm crosses 60%, the card enters active-protocol state with the glitch animation. Protocol timer starts. "Safe routes unlocked. Old Dock is coordinated."

**Step 8 — Show the Districts page**
Navigate to **Districts**. Show Midtown has different values — the system is spatially aware.

**Step 9 — Close on Transparency**
Navigate to **Transparency**. "Everything the system does is declared here, inside the product. No surveillance. No profiling. Citizens know the rules before they play."

---

## Ethical Design Decisions

**No individual surveillance.** All metrics are computed from district-level aggregates. No individual votes, clicks, or identities are tracked or stored.

**Opt-in only.** Token locking is entirely voluntary. Non-participation carries no penalty. Citizens choose to engage because coordination is how the city works, not because they're forced to.

**Transparent mechanics.** The throttle, diminishing returns, influencer multiplier, and threshold rules are all documented publicly inside the product on the Transparency page. Citizens know exactly how the system works before they commit.

**No dark patterns.** No manufactured urgency, no social shaming of non-participants, no hidden score that affects access. The only pressure is the visible consensus bar — which is the same pressure that exists in any public vote.

**Influencers as catalysts, not commanders.** Influencers provide a momentum multiplier but cannot single-handedly push a stance to threshold. They need genuine community backing to matter.

---

## Known Limitations (Current Prototype)

- Token wallet resets on page reload — no persistence
- Only two districts seeded (Old Dock, Midtown)
- Influencer staking button on Catalysts page is visual only — does not yet write back to board state
- No unlock overlay/celebration animation on threshold breach
- All three stances share the same 60% threshold (differentiated thresholds planned)
- No NPC dialogue / street chatter layer

---

## Roadmap

**Near-term**
- Full-screen unlock overlay when threshold is breached
- Differentiated thresholds per stance (Calm 60%, Verify 55%, Aid 65%)
- Influencer staking writes to board state with visible endorsement badge
- NPC street dialogue that shifts as consensus % changes

**Medium-term**
- SMS-based token locking for non-smartphone citizens (`reply 1 for CALM`)
- Physical kiosk participation nodes feeding into the same board
- Influencer Accountability Score — post-event tracking of whether endorsed stances led to good outcomes
- Multiple concurrent scenarios (election + emergency simultaneously)

**Long-term**
- Real backend with Socket.io for genuine multi-user real-time consensus
- Authority override system with mandatory public justification log
- Verify Gate for misinformation — community votes Verify or Dismiss on active rumors
- City administrator dashboard for scenario management

---

## Built At

**IETE Hackathon** — Track: Citizen Behavior Modulation System
**Team:** San Viceroy Grid Engineers
**Stack:** Vanilla HTML + CSS + JavaScript, Orbitron + Rajdhani (Google Fonts)
**Build time:** 4 hours

---

*"You're not persuading individuals. You're engineering collective agreement as a spectacle."*
