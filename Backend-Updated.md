# Moon Phase Blackjack - Backend Documentation

## Overview
The backend handles all game logic, state management, card mechanics, and rule enforcement for Moon Phase Blackjack. This includes deck management, hand evaluation, moon phase effects, betting system, and complete game flow control.

---

## Core Data Structures

### Game State Object
```javascript
const gameState = {
  playerName: 'Player',             // Player's entered name
  balance: 1000,                    // Current available chips
  currentBet: 0,                    // Current hand bet amount
  handCount: 0,                     // Number of hands played
  moonPhaseIndex: 0,                // Current moon phase (0-7)
  
  playerHand: [],                   // Array of card objects
  dealerHand: [],                   // Array of card objects
  
  playerTotal: 0,                   // Current hand value
  dealerTotal: 0,                   // Current hand value
  
  gameActive: false,                // Is a hand in progress?
  deck: []                          // Remaining cards in deck
};
```

### Card Object
```javascript
const card = {
  suit: '♠',                    // Suit symbol
  rank: 'A'                     // Rank: A, 2-10, J, Q, K
};
```

### Moon Phases Array
```javascript
const moonPhases = [
  { 
    name: '🌑 New Moon', 
    emoji: '🌑', 
    effect: 'One of your cards is hidden', 
    type: 'hidden-card' 
  },
  { 
    name: '🌒 Waxing Crescent', 
    emoji: '🌒', 
    effect: 'All cards worth +1', 
    type: 'card-boost' 
  },
  { 
    name: '🌓 First Quarter', 
    emoji: '🌓', 
    effect: 'Normal play', 
    type: 'normal' 
  },
  { 
    name: '🌕 Full Moon', 
    emoji: '🌕', 
    effect: 'Dealer weakness! (Hits until 17+)', 
    type: 'dealer-weak' 
  },
  { 
    name: '🌖 Waning Gibbous', 
    emoji: '🌖', 
    effect: 'Normal play', 
    type: 'normal' 
  },
  { 
    name: '🌗 Last Quarter', 
    emoji: '🌗', 
    effect: 'Wins pay 2x!', 
    type: 'bet-multiplier' 
  },
  { 
    name: '🌘 Waning Crescent', 
    emoji: '🌘', 
    effect: 'Card values shift +1', 
    type: 'card-shift' 
  }
];
```

---

## Core Functions

### 1. Deck Management

#### `createDeck()`
- **Returns:** Array of 52 card objects
- **Purpose:** Create a fresh standard playing deck
- **Process:**
  1. Define suits: ♠, ♥, ♦, ♣
  2. Define ranks: A, 2-10, J, Q, K
  3. Loop through all combinations
  4. Shuffle array randomly before returning

```javascript
function createDeck() {
    const suits = ['♠', '♥', '♦', '♣'];
    const ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
    const deck = [];
    
    for (let suit of suits) {
        for (let rank of ranks) {
            deck.push({ suit, rank });
        }
    }
    return deck.sort(() => Math.random() - 0.5);
}
```

#### `getCardValue(rank)`
- **Parameters:** rank (string: A, 2-10, J, Q, K)
- **Returns:** Number (card point value)
- **Logic:**
  - Ace (A) = 11
  - Face cards (J, Q, K) = 10
  - Number cards = face value (2-10)

```javascript
function getCardValue(rank) {
    if (rank === 'A') return 11;
    if (['J', 'Q', 'K'].includes(rank)) return 10;
    return parseInt(rank);
}
```

#### `drawCard()`
- **Parameters:** None
- **Returns:** Card object
- **Process:**
  1. Check if deck has fewer than 10 cards
  2. If yes, create new deck
  3. Pop and return last card from deck
  4. Update display via frontend

```javascript
function drawCard() {
    if (gameState.deck.length < 10) gameState.deck = createDeck();
    return gameState.deck.pop();
}
```

---

### 2. Hand Evaluation

#### `calculateTotal(hand)`
- **Parameters:** hand (array of card objects)
- **Returns:** Number (total points 0-21)
- **Process:**
  1. Sum all card values
  2. Count Aces (can be 1 or 11)
  3. Adjust Aces downward if total exceeds 21
  4. Apply moon phase modifiers if applicable

```javascript
function calculateTotal(hand) {
    let total = 0;
    let aces = 0;
    
    for (let card of hand) {
        total += getCardValue(card.rank);
        if (card.rank === 'A') aces++;
    }
    
    // Adjust for Aces (can be 1 or 11)
    while (total > 21 && aces > 0) {
        total -= 10;
        aces--;
    }
    
    return total;
}
```

#### `isBust(hand)`
- **Parameters:** hand (array of cards)
- **Returns:** Boolean
- **Logic:** return calculateTotal(hand) > 21

```javascript
function isBust(hand) {
    return calculateTotal(hand) > 21;
}
```

#### `isBlackjack(hand)`
- **Parameters:** hand (array)
- **Returns:** Boolean
- **Logic:** 
  - Must be exactly 2 cards
  - Must total exactly 21
  - Ace + 10-value card

```javascript
function isBlackjack(hand) {
    return hand.length === 2 && calculateTotal(hand) === 21;
}
```

---

### 3. Moon Phase Logic

#### `getCurrentMoonPhase()`
- **Returns:** Moon phase object
- **Accesses:** moonPhases[gameState.moonPhaseIndex]

```javascript
function getCurrentMoonPhase() {
    return moonPhases[gameState.moonPhaseIndex];
}
```

#### `advanceMoonPhase()`
- **Returns:** void
- **Process:** Cycles to next moon phase
- **Logic:** (current index + 1) % 8 to loop back to start

```javascript
function advanceMoonPhase() {
    gameState.moonPhaseIndex = (gameState.moonPhaseIndex + 1) % moonPhases.length;
}
```

#### `applyMoonPhaseEffect(type)`
- **Parameters:** type (string from moon phase object)
- **Returns:** void
- **Effects Applied:**

| Type | Effect |
|------|--------|
| `hidden-card` | One player card is not shown |
| `dealer-weak` | Dealer hits until 17+ instead of 16+ |
| `bet-multiplier` | Payouts doubled (2x) on this hand |
| `card-boost` | All cards worth +1 (applied in calculateTotal) |
| `card-shift` | Card values shift +1 (applied in calculateTotal) |
| `normal` | Standard blackjack rules |

---

### 4. Game Initialization

#### `startGame(playerName, startingBalance)`
- **Parameters:** 
  - playerName (string)
  - startingBalance (number)
- **Returns:** void
- **Process:**
  1. Store player name in gameState
  2. Set initial balance
  3. Create fresh deck
  4. Reset hands and totals
  5. Set moonPhaseIndex to 0
  6. Call frontend updateDisplay()

```javascript
function startGame(playerName, startingBalance) {
    gameState.playerName = playerName;
    gameState.balance = startingBalance;
    gameState.deck = createDeck();
    gameState.playerHand = [];
    gameState.dealerHand = [];
    gameState.moonPhaseIndex = 0;
    gameState.currentBet = 0;
    gameState.handCount = 0;
    updateDisplay();
}
```

---

### 5. Betting System

#### `placeBet(amount)`
- **Parameters:** amount (number)
- **Returns:** Boolean (success/failure)
- **Validation:**
  - amount > 0
  - amount <= gameState.balance
- **Process:**
  1. Validate bet amount
  2. Subtract from balance
  3. Set as currentBet
  4. Increment handCount
  5. Call displayChipsOnTable(amount)
  6. Call dealInitialCards()

```javascript
function placeBet(amount) {
    if (!amount || amount <= 0 || amount > gameState.balance) {
        showMessage('Invalid bet amount!', 'loss');
        return false;
    }
    
    gameState.currentBet = amount;
    gameState.balance -= amount;
    gameState.handCount++;
    
    displayChipsOnTable(amount);  // Frontend: show chips on table
    dealInitialCards();
    updateDisplay();
    return true;
}
```

#### `breakdownChips(amount)`
- **Parameters:** amount (number - bet amount)
- **Returns:** Array of chip values
- **Process:**
  1. Start with largest chip values [100, 50, 25, 10]
  2. Greedily use largest chips first
  3. Return array in order (staggered animation in frontend)

**Example:**
- Bet $185 → [100, 50, 25, 10]
- Bet $75 → [50, 25]
- Bet $10 → [10]

```javascript
function breakdownChips(amount) {
    const chipValues = [100, 50, 25, 10];
    const chipsToDisplay = [];
    let remaining = amount;
    
    for (let value of chipValues) {
        while (remaining >= value) {
            chipsToDisplay.push(value);
            remaining -= value;
        }
    }
    
    return chipsToDisplay;
}
```

---

### 6. Game Flow Control

#### `dealInitialCards()`
- **Returns:** void
- **Process:**
  1. Draw 2 cards for player
  2. Draw 2 cards for dealer
  3. Set dealerHidden flag to true
  4. Set gameActive to true
  5. Call frontend displayCards()
  6. Calculate initial totals
  7. Check for blackjacks

```javascript
function dealInitialCards() {
    gameState.playerHand = [drawCard(), drawCard()];
    gameState.dealerHand = [drawCard(), drawCard()];
    gameState.gameActive = true;
    
    displayCards(gameState.playerHand, 'player-cards');
    displayCards(gameState.dealerHand, 'dealer-cards', true);
    
    const playerTotal = calculateTotal(gameState.playerHand);
    document.getElementById('player-total').textContent = playerTotal;
    
    if (isBlackjack(gameState.playerHand)) {
        endHand('blackjack');
    } else if (isBlackjack(gameState.dealerHand)) {
        endHand('dealer-blackjack');
    }
}
```

---

### 7. Player Actions

#### `playerHit()`
- **Returns:** void
- **Process:**
  1. Draw card and add to playerHand
  2. Calculate new total
  3. Update display
  4. Check for bust
  5. If bust, end hand immediately

```javascript
function playerHit() {
    gameState.playerHand.push(drawCard());
    const total = calculateTotal(gameState.playerHand);
    
    displayCards(gameState.playerHand, 'player-cards');
    document.getElementById('player-total').textContent = total;
    
    if (isBust(gameState.playerHand)) {
        endHand('bust');
    }
}
```

#### `playerStand()`
- **Returns:** void
- **Process:**
  1. Set gameActive to false
  2. Hide action buttons
  3. Call dealerPlay()

```javascript
function playerStand() {
    gameState.gameActive = false;
    document.getElementById('action-buttons').style.display = 'none';
    dealerPlay();
}
```

#### `doubleDown()`
- **Returns:** Boolean (success/failure)
- **Validation:**
  - Must be first turn (playerHand.length === 2)
  - Must have enough balance for additional bet
- **Process:**
  1. Validate conditions
  2. Subtract additional bet from balance
  3. Double currentBet
  4. Draw 1 card
  5. Auto-stand and proceed to dealer play

```javascript
function doubleDown() {
    if (gameState.playerHand.length !== 2) return false;
    if (gameState.currentBet > gameState.balance) return false;
    
    gameState.balance -= gameState.currentBet;
    gameState.currentBet *= 2;
    
    gameState.playerHand.push(drawCard());
    playerStand();
    
    return true;
}
```

---

### 8. Dealer Logic

#### `dealerPlay()`
- **Returns:** void
- **Process:**
  1. Reveal dealer's hidden card
  2. Determine hit threshold based on moon phase
  3. Loop: dealer hits while total < threshold
  4. Call determineWinner()

**Moon Phase Effect:**
- Full Moon (dealer-weak): Hits until 17+ (normally 16+)
- Other phases: Hits until 16+

```javascript
function dealerPlay() {
    displayCards(gameState.dealerHand, 'dealer-cards');
    
    let dealerMustHitUntil = 16;
    const phase = getCurrentMoonPhase();
    
    if (phase.type === 'dealer-weak') {
        dealerMustHitUntil = 17;
    }
    
    while (calculateTotal(gameState.dealerHand) < dealerMustHitUntil) {
        gameState.dealerHand.push(drawCard());
    }
    
    setTimeout(() => determineWinner(), 1000);
}
```

---

### 9. Hand Resolution

#### `determineWinner()`
- **Returns:** void
- **Process:**
  1. Calculate both player and dealer totals
  2. Check for dealer bust
  3. Compare hand values
  4. Call endHand() with appropriate result

```javascript
function determineWinner() {
    const playerTotal = calculateTotal(gameState.playerHand);
    const dealerTotal = calculateTotal(gameState.dealerHand);
    
    if (isBust(gameState.dealerHand)) {
        endHand('dealer-bust');
    } else if (playerTotal > dealerTotal) {
        endHand('win');
    } else if (dealerTotal > playerTotal) {
        endHand('loss');
    } else {
        endHand('push');
    }
}
```

#### `endHand(result)`
- **Parameters:** result (string: bust, blackjack, win, loss, push, dealer-bust, dealer-blackjack)
- **Returns:** void
- **Process:**
  1. Calculate payout based on result
  2. Apply moon phase multiplier if applicable
  3. Add payout to balance
  4. Increment hand count
  5. Update display
  6. Show "New Hand" button
  7. Check for game over

**Payout Table:**

| Result | Base Payout | Last Quarter (2x) |
|--------|-------------|-------------------|
| Blackjack | 2.5x bet | 5x bet |
| Win | 2x bet | 4x bet |
| Push | 1x bet | 1x bet |
| Loss/Bust | 0x bet | 0x bet |

```javascript
function endHand(result) {
    let payout = 0;
    const phase = getCurrentMoonPhase();
    const multiplier = phase.type === 'bet-multiplier' ? 2 : 1;
    
    switch(result) {
        case 'blackjack':
            payout = gameState.currentBet * 2.5 * multiplier;
            showMessage(`BLACKJACK! +$${gameState.currentBet * 1.5 * multiplier}`, 'win');
            break;
        case 'win':
            payout = gameState.currentBet * 2 * multiplier;
            showMessage(`YOU WIN! +$${gameState.currentBet * multiplier}`, 'win');
            break;
        case 'push':
            payout = gameState.currentBet;
            showMessage('Push! Tie game.', 'neutral');
            break;
        case 'loss':
        case 'bust':
            showMessage('Dealer wins!', 'loss');
            break;
        case 'dealer-bust':
            payout = gameState.currentBet * 2 * multiplier;
            showMessage(`Dealer busts! You win! +$${gameState.currentBet * multiplier}`, 'win');
            break;
    }
    
    gameState.balance += payout;
    gameState.gameActive = false;
    
    document.getElementById('new-hand-btn').style.display = 'inline-block';
}
```

---

### 10. New Hand / Moon Phase Advancement

#### `newHand()`
- **Returns:** void
- **Process:**
  1. Advance moon phase index
  2. Clear cards from hands
  3. Reset betting UI
  4. Clear chips from table
  5. Reset currentBet to 0
  6. Update display with new moon phase

```javascript
function newHand() {
    gameState.moonPhaseIndex = (gameState.moonPhaseIndex + 1) % moonPhases.length;
    
    gameState.playerHand = [];
    gameState.dealerHand = [];
    gameState.currentBet = 0;
    
    // Clear DOM elements
    document.getElementById('player-cards').innerHTML = '';
    document.getElementById('dealer-cards').innerHTML = '';
    document.getElementById('chips-on-table').innerHTML = '';
    
    // Reset UI
    document.getElementById('place-bet-btn').style.display = 'inline-block';
    document.getElementById('betting-section').style.display = 'flex';
    document.getElementById('action-buttons').style.display = 'none';
    document.getElementById('new-hand-btn').style.display = 'none';
    
    showMessage('Place your bet!', 'neutral');
    updateDisplay();
}
```

---

## Helper Functions

```javascript
function isGameOver() {
    return gameState.balance <= 0;
}

function canDoubleDown() {
    return (gameState.playerHand.length === 2 && 
            gameState.currentBet <= gameState.balance &&
            gameState.gameActive);
}

function hasBlackjack() {
    return isBlackjack(gameState.playerHand) || isBlackjack(gameState.dealerHand);
}
```

---

## Game Loop Flow

```
1. Start Game (Setup Page) → startGame()
2. Place Bet → placeBet() → displayChipsOnTable()
3. Deal Cards → dealInitialCards()
4. Player Turn Loop:
   → Hit → playerHit()
   → Stand → playerStand()
   → Double Down → doubleDown()
   → Bust Check → endHand('bust')
5. Dealer Turn → dealerPlay()
6. Determine Winner → determineWinner()
7. End Hand → endHand(result)
8. Show New Hand Button
9. New Hand → newHand() → back to step 2
```

---

## Moon Phase Effects Summary

| Phase | Type | Gameplay Impact |
|-------|------|-----------------|
| New Moon 🌑 | hidden-card | One player card hidden from view |
| Waxing Crescent 🌒 | card-boost | Card values +1 (Ace=12, Face=11) |
| First Quarter 🌓 | normal | Standard blackjack |
| Full Moon 🌕 | dealer-weak | Dealer hits until 17+ (favorable) |
| Waning Gibbous 🌖 | normal | Standard blackjack |
| Last Quarter 🌗 | bet-multiplier | Winning payouts 2x |
| Waning Crescent 🌘 | card-shift | Card values +1 |

---

## Data Flow Diagram

```
Game State
    ↓
placeBet() → validateBet() → adjustBalance() → displayChipsOnTable()
    ↓
dealInitialCards() → drawCard() × 4 → displayCards() → checkBlackjack()
    ↓
playerHit() → drawCard() → calculateTotal() → isBust()? → endHand() : display
    ↓
playerStand() → dealerPlay() → dealer loop → determineWinner()
    ↓
endHand() → calculatePayout() → applyMoonPhase() → updateBalance()
    ↓
newHand() → advanceMoonPhase() → clearHands() → back to placeBet()
```

---

## Testing Checklist

- [ ] Deck creation returns 52 unique cards
- [ ] Shuffle randomizes card order
- [ ] Hand total calculation correct with Ace flexibility (11→1)
- [ ] Bust detection at > 21
- [ ] Blackjack detection (2 cards = 21)
- [ ] Dealer hits correctly on 16 and stands on 17
- [ ] Full Moon: Dealer hits until 17+
- [ ] Last Quarter: Payouts doubled
- [ ] Moon phase cycles correctly (0→7→0)
- [ ] Balance updates correctly
- [ ] Chips breakdown correctly (greedy algorithm)
- [ ] Game over when balance ≤ 0
- [ ] New hand advances moon phase

