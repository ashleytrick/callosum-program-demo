# C01 / C02 / C03 — Cardholder Active Screens

**Session states:** LIVE_ROUND  
**Role:** CARDHOLDER only

---

## Visual design system

Physical card design reference: uploaded by Ashley 2026-10-11 (issue #83).

### Level color tokens

Each card's level determines its accent color. Apply the level token to the card identity block's top border and the right-call banner background in C02/C03.

| Level | Token name | Physical card color | Notes |
|---|---|---|---|
| L1 | `--level-1` | Dark teal | Default deck |
| L2 | `--level-2` | Dark navy/blue | Default deck |
| L3 | `--level-3` | Copper/orange-brown | Default deck |
| L4 | `--level-4` | TBD | Not shown in current card set; placeholder token required |

Exact hex values are a visual sign-off decision, not fixed by this spec. The physical card palette is the reference. The spec records the semantic token mapping only. When the level cannot be determined from the projection, fall back to a neutral surface.

### Behavior types and icons

Each card has a behavior type. The type name and an icon appear together as the card's primary identity block in C02/C03.

| Behavior type | Physical icon | Tagline |
|---|---|---|
| CLINGY | Magnet | "You love this chat. Make it hard for them to leave." |
| QUICK YES | Lightning bolt | "You get it fast and say yes fast. If they keep selling after your yes, keep the card." |
| POLITE | Smiley face | "Warm and nice to everyone. Give nothing away unless asked." |
| HARD TO GET | Open door | "You want to leave. Start wrapping up after a minute. Stay only if they hook you fast." |
| SKEPTIC | Shield with question mark | "Push back on one thing. Warm up only if the answer is specific." |
| WILDCARD | Dice | "Unpredictable. Change the topic or your mood once or twice." |
| QUIET | Low-volume speaker | "Short answers. Open up only to good questions." |
| BIG TALKER | Megaphone | "Big claims, big names. Go vague when asked for specifics." |

Icons are rendered from the design asset set (not emoji); style references the physical card icons. Behavior names are always uppercase in the card identity block.

### Content zone hierarchy

The physical card defines four content zones. In C02 they are ordered by mid-conversation priority:

1. **Card identity** — behavior icon, name, tagline, card ID, level. Orientation at a glance; shown at the top.
2. **Right-call banner** — the intended move and instruction. Most critical during a live conversation; visually dominant, dark background, full card width.
3. **Scripted lines** — ASK THEM and SAY THIS ONCE. Active conversation reference; prominent, scannable.
4. **Private context** — WHO YOU ARE and WHAT'S REALLY GOING ON. Persona depth for preparation; collapsed by default in C02 to reduce cognitive load mid-conversation.

---

## C01 — Own Hand Index

### Purpose
The Cardholder's resting state: their hand of cards (up to 4, one per level L1–L4). The screen shows what cards they hold and which is active.

### Layout

```
+------------------------------------+
|  [Round badge]  [Timer display]    |
|                                    |
|  [COPY: hand.heading]              |  <- "Your cards"
|                                    |
|  +------------------------------+  |
|  |  L1  [Card name]    ACTIVE ->|  |  <- tapping opens C02/C03 (active card)
|  +------------------------------+  |
|  +------------------------------+  |
|  |  L2  [Card name]    [->]     |  |  <- can tap to preview (read-only)
|  +------------------------------+  |
|  +------------------------------+  |
|  |  L3  [Card name]    [->]     |  |
|  +------------------------------+  |
|  +------------------------------+  |
|  |  L4  [Card name]    [->]     |  |
|  +------------------------------+  |
|                                    |
|  [COPY: hand.hint]                 |  <- "Tap a card to see its details"
|                                    |
|  [Language toggle]                 |  <- EN / FR (affects C02 vs C03)
|                                    |
|  [Incoming ->]                     |  <- badge if a Founder is approaching (-> C04)
+------------------------------------+
```

### States

| State | Condition | UI change |
|---|---|---|
| Normal | Cards present, no interaction | As above |
| Incoming approach | Founder scanned Cardholder | "Incoming" badge pulses |
| No active card | All cards transferred/retired | [COPY: hand.empty]: "All cards played" |
| Auto-drawing | Last card triggered auto-draw | Loading indicator on next slot |

### Active card indication
- Card order is the **engine's hand order**, not level order. Per DEC-002 the 4 initial cards (one per level) are in a seeded-random shuffled order; the rows above are illustrative and levels may appear in any order (e.g. L3, L1, L4, L2). The design never sorts by level.
- The **active card is the first active card in that order** (shuffled first-active order). It is clearly distinguished. The UI renders the order delivered in `self.cards` and does not compute its own.
- Only cards in the projection are shown. The current projection omits retired cards, so retired cards are **not** displayed (no struck-through rows). A retired-card history is a proposed extension (CD-09), gated until explicitly approved.
- Which entry is "active" depends on the projection preserving hand order or exposing an active marker; see `contract-dependency-matrix.md` CD-08.

### Rules
- The Cardholder's own hand is fully visible to themselves.
- Other participants do not see this screen or its contents.
- When the active card is retired (transferred), the next first-active card in shuffled order becomes active. No level-based ordering applies.
- DEC-002: Cardholder starts with 4 cards (one per level, shuffled). The Cardholder must retire all 4 initial cards before any deck draw; then each further retirement triggers an automatic `CARD_DRAWN` while the deck has cards (deck cards may be any level). No manual draw exists.

### Secrecy audit
- **Own cards:** shown (Cardholder's private information) ✓
- **Other Cardholders' hands:** NOT shown ✓
- **Founder identities / roles:** NOT shown ✓
- **FacilitatorSessionView:** absent ✓

---

## C02 — Active Card (English)

### Purpose
Detail view of the Cardholder's currently active card, displayed in English. Primary reference during a conversation — the Cardholder uses this to evaluate a Founder's pitch and make the right call.

### Layout

```
+--------------------------------------------+
|  [<- Back to C01]               [FR ->]   |
|                                             |
|  +-- Card identity (level accent top) ----+|
|  |  [Behavior icon]  [BEHAVIOR NAME]      ||  <- e.g. magnet + CLINGY
|  |  [Behavior tagline]                    ||  <- italic/secondary weight
|  |  [Card ID]  Level [N]  [dot dots]      ||  <- e.g. A1 · Level 1 · (filled)(empty)(empty)(empty)
|  +------------------------------------------+|
|                                             |
|  +-- Right-call banner (dark background) -+|
|  |  THE RIGHT CALL                        ||  <- small uppercase label
|  |  [MOVE LABEL]                          ||  <- bold, large; e.g. MOVE ON
|  |  [Move instruction]                    ||  <- e.g. "Exit gracefully."
|  +------------------------------------------+|
|                                             |
|  [COPY: card.ask_them]                      |  <- "Ask them:"
|  [Challenge text]                           |
|                                             |
|  [COPY: card.say_this]                      |  <- "Say this once:"
|  "[Say-this-once text]"                     |  <- bold; quotation treatment
|                                             |
|  > [COPY: card.your_character]              |  <- <details> collapsed by default
|    [COPY: card.who_you_are]                 |    "Who you are"
|    [Identity text]                          |
|    [COPY: card.whats_really_going_on]       |    "What's really going on"
|    [Hidden motivation text]                 |
|                                             |
|  [Incoming ->]                              |  <- badge if approach active -> C04
+--------------------------------------------+
```

### Card identity block

- Level accent color (`--level-N`) applied as the block's top border.
- Behavior icon and behavior name on the same line, icon leading. Name is uppercase.
- Behavior tagline below the name in italic or secondary weight.
- Card ID and level indicator (filled/empty dots) on a third line, de-emphasized.
- The card ID (e.g. "A1", "B7", "C3") is shown for reference; it is the Cardholder's private identifier and must not appear on any Founder-facing surface.

### Right-call banner

- Full card width; dark background surface using the level's dark variant (`--level-N-dark`) or a neutral dark surface.
- "THE RIGHT CALL" in small uppercase as a de-emphasized label above the move.
- Move label (e.g. MOVE ON, GO FOR IT, LATER, SMALL ASK) bold and large — the most visually prominent text on the screen.
- Move instruction below the label in smaller body weight (e.g. "Exit gracefully.", "Ask for a concrete next step.").
- This banner is the most privacy-sensitive element: the intended move the Founder is trying to guess. It is shown here because the Cardholder is its holder.

### Scripted lines

- "Ask them:" label (small, muted) above the challenge text; challenge text in body weight.
- "Say this once:" label (small, muted) above the scripted line.
- Scripted line in bold weight with quotation treatment matching the physical card (e.g. `"That's so cool."`).
- These two elements are visually grouped between the right-call banner and the character details.

### Your character (collapsible)

- `<details>` element, **collapsed by default** in C02.
- Summary text: "Your character" ([COPY: card.your_character]).
- When expanded: "Who you are" subheading followed by identity text; "What's really going on" subheading followed by hidden motivation text.
- Labels use copy keys `card.who_you_are` and `card.whats_really_going_on`.
- Collapsed by default because during a live conversation the right call and scripted lines are the primary reference; the persona context is preparation, not in-the-moment guidance.
- The Cardholder can expand at any time to review their character depth.

### Projection dependency

The following fields are required by this spec and must be confirmed present in `SessionView.self.cards[]`:

| Field | Status | Notes |
|---|---|---|
| Card level | Existing | Used for level token |
| Card name (behavior type) | Existing | Displayed as BEHAVIOR NAME |
| `askThem` (challenge text) | Existing | Displayed as scripted line |
| `sayThisOnce` | Existing | Displayed as scripted line |
| `intendedMove` (right call) | Existing | Displayed in right-call banner |
| Behavior tagline | **Pending CD** | New field; see below |
| Behavior icon identifier | **Pending CD** | New field; see below |
| Card ID (e.g. "A1") | **Pending CD** | New field; see below |
| `whoYouAre` | **Pending CD** | New field; see below |
| `whatsReallyGoingOn` | **Pending CD** | New field; see below |

**New contract dependency required:** CD-XX — cardholder card detail projection (`behaviorType`, `tagline`, `cardId`, `whoYouAre`, `whatsReallyGoingOn`). Until this is resolved, stub absent fields with placeholder treatment and [COPY: card.detail_unavailable]. This CD entry should be added to `docs/design/contract-dependency-matrix.md` as a Proposed/Gated item before implementation.

### Critical privacy note

- The right-call banner shows the Cardholder's intended move. The Founder must **not** be able to see this screen or deduce its content from any other surface.
- WHO YOU ARE and WHAT'S REALLY GOING ON are the Cardholder's private persona — never shown to any other participant.
- The card ID, behavior tagline, and identity/motivation text are all `ROLE_SECRET` fields. They must never appear in any Founder-facing projection or public event payload.

### Rules
- This screen is only accessible to the Cardholder — no other role.
- If the round ends while viewing, return to C01.
- "FR ->" opens C03 (French variant of same card).
- Level accent color degrades gracefully if level is unknown.

### States

| State | Condition | UI change |
|---|---|---|
| Normal | Active card present | Layout above |
| Incoming approach | Founder has scanned | "Incoming" badge pulses; tap -> C04 |
| Detail loading | New projection fields pending CD | Stub text for absent fields |

### Secrecy audit
- **Intended move (right-call banner):** shown only to Cardholder ✓
- **Card challenge text:** shown only to Cardholder ✓
- **WHO YOU ARE / WHAT'S REALLY GOING ON:** shown only to Cardholder ✓
- **Card ID, behavior tagline, identity/motivation:** shown only to Cardholder ✓
- **Other Cardholders' cards:** NOT shown ✓
- **Founder scores/roles:** NOT shown ✓

---

## C03 — Active Card (French)

### Purpose
French interface and reference variant of C02. Required ASK THEM / SAY THIS ONCE lines stay in English and remain visible for spoken play. Approved French reference translations may appear alongside them; they never replace the English lines.

### Layout
Same as C02 with:
- `[EN ->]` language toggle (returns to C02)
- French interface labels (copy keys in French)
- Card identity block: behavior name in English (names are always English); tagline in French if an approved translation record exists
- "Ask them" / "Say this once" labels and lines remain English (spoken play requirement); French reference translations may appear below in lighter weight when approved translation records exist
- Right-call banner: move label in English; instruction line in French if approved translation exists (e.g. "Sortez gracieusement.")
- "Your character" details: WHO YOU ARE / WHAT'S REALLY GOING ON in French if approved translation records exist
- Move label in French context (e.g. "AVANCER — Fonce — tente le tout pour le tout")

### Rules
- Language toggle always available on C02 and C03.
- Pitch language is always English; C03 is for the Cardholder's own reference.
- Both screens show the same card — only the displayed language changes.
- Translation records must follow the merged localization specification; no automatic translation of card content is assumed.

---

## Accessibility (all screens in this file)

- C01 card list: each card row is a focusable element with accessible name including level and card name; active card additionally labelled "active"
- C01 incoming badge: `aria-live="assertive"` so approaching Founder is announced immediately
- C02/C03 card identity block: behavior name and level are text elements, not icon-only; behavior icon has `aria-hidden="true"`; the accessible name of the identity block includes behavior type name and level
- C02/C03 right-call banner: move label is a text heading (`<h2>` or equivalent); not conveyed by color alone; section has `aria-label="The right call"`; level-color treatment does not carry meaning by color alone (move label text is the primary signal)
- C02/C03 scripted lines: "Ask them" and "Say this once" sections use `<section>` with accessible heading
- C02/C03 "Your character" disclosure: `<details>/<summary>` with accessible expanded/collapsed state; summary text is readable as standalone; subheadings "Who you are" and "What's really going on" are accessible headings when expanded
- C02/C03 language toggle: `aria-label="Switch to French"` / `"Switch to English"`; toggle is a `<button>`, not a `<select>`
- All actionable elements: minimum 44x44 dp touch targets
