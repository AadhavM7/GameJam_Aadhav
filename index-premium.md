# Moon Phase Blackjack - Premium Reference

This markdown file captures the visual intent and UI structure from the original premium HTML. Use it alongside Frontend-Updated.md to build and refine the actual frontend HTML.

## Page 1: Introduction and Rules
- Hero title and subtitle centered in a glass panel
- Sections: The Game, Basic Rules, Moon Phase Effects, Payouts
- Moon phases showcase grid (4 columns desktop, 2 tablet, 1 mobile)
- Primary action: Play Now (gold)
- Secondary action: Close (cyan)

## Page 2: Player Setup
- Player name input (required)
- Starting balance input (100 to 10000, step 100)
- Live balance preview box
- Primary action: Begin Your Journey (gold)
- Secondary action: Back (cyan)

## Page 3: Game
- Header with title and four stats (player, balance, bet, hand)
- Moon phase info box with name, effect, and large emoji
- Blackjack table with dealer area, chips on table, and player area
- Message area with win, loss, and neutral states
- Betting panel with input, quick chips, and Place Bet button
- Action buttons: Hit, Stand, Double Down
- Navigation: New Hand and Main Menu

## Visual Language
- Deep space gradient background with a starfield overlay
- Gold, cyan, and purple accents for premium casino feel
- Glass panels with blur, gold borders, and soft glow
- Table felt gradient with curved bottom and subtle texture

## Animations
- Page fade in
- Intro and setup panels slide up
- Card flip in on draw
- Chip placement pop in with staggered delays
- Moon glow pulse

## Color Tokens
```css
:root {
  --dark-space: #0a0a15;
  --table-green: #1a472a;
  --card-white: #f5f1e8;
  --gold: #d4af37;
  --gold-bright: #ffd700;
  --silver: #c0c0c0;
  --text-light: #f0f4f8;
  --text-muted: #a8b5c8;
  --cyan: #00d4ff;
  --purple: #b24bff;
  --neon-green: #00ff88;
  --red: #ff4757;
  --green: #2ed573;
}
```

## Key Structure and IDs
- Pages: #intro-page, #setup-page, #game-page
- Intro panel: .intro-container
- Setup panel: .setup-container
- Game wrapper: .game-wrapper
- Dealer area: #dealer-cards, #dealer-total
- Player area: #player-cards, #player-total
- Table chips: #chips-on-table
- Betting controls: #bet-amount, #place-bet-btn, #betting-section
- Actions: #action-buttons, #hit-btn, #stand-btn, #double-down-btn
- Navigation: #new-hand-btn, #menu-btn
