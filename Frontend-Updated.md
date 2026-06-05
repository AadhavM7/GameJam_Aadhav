# Moon Phase Blackjack - Frontend Documentation

## Overview
The frontend is a premium, multi-page casino-style web application built with HTML, CSS, and vanilla JavaScript. It features three distinct pages: Rules/Introduction, Player Setup, and the full Blackjack Game with realistic table aesthetics and chip visualization.

---

## Architecture: Three-Page Design

### **Page 1: Introduction & Rules**
**Purpose:** Educate players about the game and moon phase mechanics before playing.

**Components:**
- Large gradient title: "🌙 MOON PHASE BLACKJACK 🌙"
- Subtitle: "Where Lunar Forces Determine Your Destiny"
- Rules sections with clear headings:
  - **The Game**: Overview of what Moon Phase Blackjack is
  - **Basic Rules**: Standard Blackjack rules (card values, hitting, standing, doubling down, busting)
  - **Moon Phase Effects**: 8-phase showcase with emoji and effect descriptions
  - **Payouts**: Payout structure for blackjack, wins, pushes, and losses
  
- **Moon Phases Showcase Grid:** 
  - 4 columns (responsive to 2 on tablet, 1 on mobile)
  - Each phase displays emoji, name, and effect
  - Purple accent border for celestial theme
  - Cards with dark background for contrast

- **Navigation:**
  - Gold "PLAY NOW" button → Goes to Setup Page
  - Cyan "CLOSE" button → Stays on intro (for reference)

**Visual Design:**
- Semi-transparent container with glass effect (backdrop-filter blur)
- 3px gold border with large shadow
- Animated slide-up entrance
- Letter-spacing on text for luxury feel
- Gradient text on title (gold → cyan → gold)

---

### **Page 2: Player Setup**
**Purpose:** Collect player name and starting balance before entering the game.

**Components:**
- Title: "Welcome, Player"
- Form with two input fields:
  1. **Player Name** (text input, required)
  2. **Starting Balance** (number input, $100-$10,000, step of $100)
- **Live Balance Preview Box:**
  - Shows "You will start with:"
  - Large gold text: "$XXXX"
  - Updates in real-time as player changes balance
- **Form Actions:**
  - Gold "BEGIN YOUR JOURNEY" button → Starts game
  - Cyan "← BACK" button → Returns to rules

**Visual Design:**
- Same container style as intro page
- Form inputs with gold borders
- Focus states: border turns cyan, glows with box-shadow
- Semi-transparent input backgrounds
- Clean spacing with 25px gap between form fields

---

### **Page 3: The Game - Full Blackjack Experience**
**Purpose:** Provide an immersive, professional blackjack gaming experience with moon phase mechanics.

#### **3.1 Game Header**
**Layout:** Horizontal flex container with space-between alignment

**Components:**
- **Left Side:** Game title "🌙 MOON PHASE BLACKJACK"
- **Right Side:** Game Stats Bar with 4 stat items:
  1. Player Name
  2. Current Balance ($)
  3. Current Bet ($)
  4. Hand Count (game round number)

**Visual:**
- Dark semi-transparent background with gold border
- Stat labels in small muted gray text
- Stat values in large gold/bright gold text
- Tab-numeric font for numbers (consistent width)
- Responsive: Stacks vertically on small screens

---

#### **3.2 Moon Phase Info Box**
**Purpose:** Display current moon phase, name, effect, and large emoji.

**Layout:** Horizontal flex (stacks vertically on mobile)

**Components:**
- **Left Section:**
  - Phase Name: Bold gold text (e.g., "🌕 Full Moon")
  - Phase Effect: Cyan text with line-height for readability (e.g., "Dealer must hit until 17+")
  
- **Right Section:**
  - Large Moon Emoji (4em font size)
  - Animated glow effect (moonGlow animation, 3s duration)
  - Filter drop-shadow that pulses

**Visual:**
- Gradient background: purple (20% opacity) + cyan (10% opacity)
- 2px purple border
- Backdrop-filter blur for glass effect
- Animation: moonGlow pulses between drop-shadow(20px) and drop-shadow(40px)

**Animation Code:**
```css
@keyframes moonGlow {
    0%, 100% { filter: drop-shadow(0 0 20px rgba(255, 215, 0, 0.6)); }
    50% { filter: drop-shadow(0 0 40px rgba(255, 215, 0, 1)); }
}
```

---

#### **3.3 The Blackjack Table**
**Purpose:** Serve as the main game playing surface with professional casino aesthetics.

**Visual Design:**
- **Shape:** Rounded top (50px), curved bottom (200px) - classic blackjack table curve
- **Background:** Linear gradient from table green (#1a472a) to darker green (#0d2e1a)
- **Border:** 4px solid gold with large shadows:
  - Outer shadow: 20px offset, 60px blur, 0.4 opacity gold
  - Inset shadow: 0 0 40px dark with 0.5 opacity
  - Creates 3D depth effect
- **Felt Texture:** Pseudo-element (::before) with radial gradient overlay for subtle texture

**Layout:** Flex column with space-between, min-height 500px

---

#### **3.4 Dealer Area (Top of Table)**
**Components:**
- **Dealer Label:** "DEALER" in uppercase, gold text, 1.2em, bold, letter-spacing 1px
- **Dealer Cards Container:**
  - Flex row centered with 20px gap
  - Min-height 180px to accommodate cards
  - First card visible, second card hidden during deal (shows as mystery card)
  
- **Dealer Total:** 
  - "Total: [number]" in gold
  - 1.3em, bold font
  - Updated in real-time

**Card Styling (Dealer Cards):**
- Standard playing cards (see section 3.6)
- Hidden dealer card has special styling (see below)

---

#### **3.5 Betting Area on Table (Center)**
**Purpose:** Display placed chips visually on the felt during gameplay.

**Layout:** Flex center, 30px margin top/bottom

**Components:**
- **Chips-on-Table Container:**
  - Flex row with 15px gap, centered, flex-wrap
  - Min-height 100px, vertically centered
  - Displays stacked chips representing current bet

**Chip Styling (On-Table):**
- **Dimensions:** 70x70px circles
- **Border:** 4px rgba(white, 0.3) for raised effect
- **Shadow:** Triple shadow for 3D effect
  - Outer: 6px 20px, 0.6 opacity black (depth)
  - Inset -2px 5px: Shadow effect (bottom indent)
  - Inset 2px 5px: Highlight effect (top shine)
- **Shine Effect:** ::before pseudo-element with radial gradient
- **Animation:** chipPlace animation (400ms)
  - Starts at 1.3x scale, -30px translateY, 0 opacity
  - Ends at 1x scale, 0px translateY, 1 opacity
  - Staggered via animation-delay in JavaScript (50ms between chips)

**Chip Color Variants (On-Table):**
| Chip Value | Color | Styling |
|-----------|-------|---------|
| $10 | Red gradient (#ff6b6b → #ff5252) | White text |
| $25 | Gold gradient (var(--gold) → #ffb300) | Dark text |
| $50 | Green gradient (var(--green) → #00cc44) | White text |
| $100 | Cyan gradient (var(--cyan) → #00b8e6) | Dark text |

**Chip Breakdown Example:**
- Bet $185 displays as: 1×$100 + 1×$50 + 1×$25 + 1×$10
- Each chip staggered entrance animation
- Chips stack horizontally with flex wrapping

---

#### **3.6 Playing Cards (Standard)**
**Dimensions:** 110px wide × 160px tall

**Styling:**
- Background: var(--card-white) (#f5f1e8) for natural card look
- Border: 3px solid dark space color
- Border-radius: 12px for card corners
- Box-shadow: Triple shadow
  - Main: 10px 30px, 0.7 opacity black (depth)
  - Outer glow: 0 0 0 1px rgba(gold, 0.3)
- Flex centered content

**Card Content:**
- **Card Suit:** 2.5em emoji (♠ ♥ ♦ ♣)
- **Card Rank:** 1.8em bold text (A, 2-10, J, Q, K)
- Colored rank text matching suit color

**Card Animation (cardAppear):**
```css
@keyframes cardAppear {
    from {
        opacity: 0;
        transform: scale(0.8) rotateY(90deg);
    }
    to {
        opacity: 1;
        transform: scale(1) rotateY(0);
    }
}
```
- Duration: 500ms ease-out
- Creates flip effect on draw

**Special Card States:**

| State | Visual |
|-------|--------|
| **Hidden Card** (Dealer's hole card) | Purple-green gradient bg, gold border, "?" emoji, 3.5em font, 20px shadow |
| **Bust Hand** | Red border, red glow box-shadow (30px spread) |
| **Win Hand** | Green border, green glow box-shadow (30px spread) |

---

#### **3.7 Player Area (Bottom of Table)**
**Layout:** Mirror of dealer area

**Components:**
- **Player Label:** "YOUR HAND" in uppercase, cyan text, 1.2em, bold
- **Player Cards Container:**
  - Flex row centered with 20px gap
  - Min-height 180px, flex-wrap for multiple cards
  - All cards visible (no hidden cards for player)
  
- **Player Total:**
  - "Total: [number]" in cyan
  - 1.3em, bold font

---

### **Page 3, Section 2: Control & Betting Panel**

#### **3.8 Betting Section**
**Purpose:** Allow players to enter bet amounts using input or click-to-bet chips.

**Layout:** Flex row, centered, wrap on mobile

**Components:**
1. **Bet Input Group:**
   - Label "Bet:" in cyan, bold, 1.1em
   - Input field (120px width)
     - Gold 2px border
     - Semi-transparent dark background
     - Light text
     - Focus state: cyan border, cyan glow, brighter background

2. **Quick-Bet Chips:**
   - 4 clickable chip buttons: $10, $25, $50, $100
   - Dimensions: 60px circles
   - Hover effect: scale(1.1), shadow grows
   - Each color-coded (red, gold, green, cyan)
   - Cursor pointer on hover

3. **Place Bet Button:**
   - Gold gradient button
   - Text: "PLACE BET"
   - Hover: translateY(-3px), large shadow glow
   - Active: translateY(0)

**Visual:**
- Dark semi-transparent background container
- 2px gold border, 15px border-radius
- 25px padding, flex gap 20px
- Backdrop blur effect

---

#### **3.9 Action Buttons**
**Purpose:** Player controls during gameplay (Hit, Stand, Double Down).

**Layout:** Flex row, centered, gap 15px, wrap on mobile

**Buttons:**
| Button | Style | Function |
|--------|-------|----------|
| HIT | Cyan secondary | Draw another card |
| STAND | Cyan secondary | Let dealer play |
| DOUBLE DOWN | Red secondary | Double bet, 1 more card |

**Button Styling:**
- Padding: 14px 35px
- Font-size: 1.05em, bold, uppercase
- Letter-spacing: 1.5px
- Transition: 300ms ease
- Hover: translateY(-3px), large glow shadow
- Active: translateY(0)
- Disabled: opacity 0.5, no cursor

**Hover Effect:**
- ::before pseudo-element slides left to right (background sweep)
- 400ms transition

---

#### **3.10 Message Display Area**
**Purpose:** Show game status and results.

**Styling:**
- Text-align: center
- Font-size: 1.3em, bold
- Min-height: 40px
- Flex centered content
- Animation: messageSlide (500ms)
  - Enters: translateY(-20px), 0 opacity
  - Exits: normal position, 1 opacity

**Message States (Color-Coded):**
| State | Color | Glow | Example |
|-------|-------|------|---------|
| **Win** | Green (#2ed573) | Green text-shadow | "YOU WIN! +$50" |
| **Loss** | Red (#ff4757) | Red text-shadow | "BUST! You went over 21!" |
| **Neutral** | Cyan (#00d4ff) | Cyan text-shadow | "Hit or Stand?" |

---

#### **3.11 Navigation Buttons**
**Layout:** Flex row, centered

**Buttons:**
- **New Hand Button:** Gold, appears after each hand
  - Text: "NEW HAND"
  - Triggers next round with new moon phase
  
- **Menu Button:** Cyan, always visible
  - Text: "← MAIN MENU"
  - Returns to intro page

---

## Page Transitions & Animations

### **Page Entry Animation:**
```css
@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}
```
- 600ms ease-out
- Applied to all pages

### **Container Animations:**
- **Intro/Setup Containers:** slideUp (800ms)
  - From: opacity 0, translateY(30px)
  - To: opacity 1, translateY(0)

- **Stats Bar:** fadeIn (800ms with 200ms delay)
- **Moon Info Box:** fadeIn (800ms with 400ms delay)
- **Hand Containers:** fadeIn (800ms with 500ms delay)
- **Control Panel:** fadeIn (800ms with 600ms delay)

---

## Color Scheme & Design Tokens

```css
:root {
    /* Backgrounds */
    --dark-space: #0a0a15;           /* Primary background */
    --table-green: #1a472a;          /* Blackjack table color */
    
    /* Text & Cards */
    --card-white: #f5f1e8;           /* Card background */
    --text-light: #f0f4f8;           /* Primary text */
    --text-muted: #a8b5c8;           /* Secondary text */
    
    /* Accents - Primary */
    --gold: #d4af37;                 /* Luxury accent */
    --gold-bright: #ffd700;          /* Bright gold */
    
    /* Accents - Secondary */
    --cyan: #00d4ff;                 /* Modern cyan */
    --purple: #b24bff;               /* Moon phase color */
    --neon-green: #00ff88;           /* Success accent */
    
    /* Feedback */
    --red: #ff4757;                  /* Loss/error */
    --green: #2ed573;                /* Win/success */
    
    /* Special */
    --silver: #c0c0c0;               /* Chip accent */
    --moon-white: #ffffff;           /* Moon white */
}
```

---

## Responsive Design Breakpoints

### **Desktop (1024px+)**
- Full 2-column table layout
- 4-column moon phases grid
- Standard spacing and padding

### **Tablet (768px - 1023px)**
- 2-column moon phases grid
- Slightly reduced card sizes
- Moon info box: flex-direction changes to column

### **Mobile (<768px)**
- 1-column layout
- 2-column moon phases grid
- Stacked containers
- Reduced font sizes
- Card size: 90px × 130px
- Buttons stack vertically
- Responsive padding and margins

---

## Key DOM Elements (Query Selectors)

```javascript
// Page navigation
const pages = document.querySelectorAll('.page');

// Page 1: Intro
// (no specific interactive elements)

// Page 2: Setup
const playerNameInput = document.getElementById('player-name');
const startingBalanceInput = document.getElementById('starting-balance');
const displayBalance = document.getElementById('display-balance');

// Page 3: Game
const moonNameDisplay = document.getElementById('moon-name');
const moonEmojiLarge = document.getElementById('moon-large');
const moonEffectDisplay = document.getElementById('moon-effect');

const dealerCards = document.getElementById('dealer-cards');
const playerCards = document.getElementById('player-cards');
const dealerTotal = document.getElementById('dealer-total');
const playerTotal = document.getElementById('player-total');

const chipsOnTable = document.getElementById('chips-on-table');

const playerNameDisplay = document.getElementById('player-name-display');
const balanceDisplay = document.getElementById('balance-display');
const betDisplay = document.getElementById('bet-display');
const handCount = document.getElementById('hand-count');

const betAmountInput = document.getElementById('bet-amount');
const placeBetBtn = document.getElementById('place-bet-btn');
const bettingSection = document.getElementById('betting-section');

const message = document.getElementById('message');
const actionButtons = document.getElementById('action-buttons');
const newHandBtn = document.getElementById('new-hand-btn');
const menuBtn = document.getElementById('menu-btn');
```

---

## Key Update Functions

```javascript
// Page navigation
function goToPage(pageId)

// Setup page
function startGame(event)

// Game display
function displayCards(hand, containerId, hiddenCard)
function displayChipsOnTable(amount)
function updateDisplay()
function showMessage(text, type)

// Game actions
function placeBet(amount)
function placeBetAmount()
function dealInitialCards()
function playerHit()
function playerStand()
function doubleDown()
function dealerPlay()
function determineWinner()
function endHand(result)
function newHand()
```

---

## Accessibility Features

- Clear button labels with uppercase text
- Readable font sizes (min 16px base)
- High contrast colors (gold on dark, cyan on dark)
- Focus-visible states on all interactive elements
- Semantic HTML structure
- ARIA labels on important UI elements
- Keyboard navigation support

---

## Performance Considerations

- CSS-only animations (no JavaScript animation loops)
- Backdrop-filter blur with fallback
- Minimal DOM manipulation
- Event delegation where possible
- Staggered chip animations (50ms) instead of simultaneous
- Fixed starfield background (no reflow on scroll)
- Debounced rapid button clicks

---

## Browser Compatibility

- Modern browsers (Chrome, Firefox, Safari, Edge)
- CSS Grid and Flexbox support required
- CSS Animations support required
- Backdrop-filter support (with graceful fallback)
- JavaScript ES6+ (const, arrow functions, template literals)

