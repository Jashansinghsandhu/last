# New Features Implementation Guide

This document explains all the new features that have been implemented in the bot.

## 📋 Table of Contents
1. [Deposit System with 12-Word Recovery Phrase](#1-deposit-system-with-12-word-recovery-phrase)
2. [Gift Code Wager Conditions](#2-gift-code-wager-conditions)
3. [Admin Panel Game Limits](#3-admin-panel-game-limits)
4. [Multi-Language Support](#4-multi-language-support)
5. [Single Emoji Games](#5-single-emoji-games)
6. [Group PvP Challenge System](#6-group-pvp-challenge-system)

---

## 1. Deposit System with 12-Word Recovery Phrase

### What Changed
Previously, the bot used individual secret keys for each cryptocurrency. Now, you can use a single 12-word recovery phrase to generate addresses for all supported currencies.

### Configuration
1. Locate the `MASTER_WALLET_RECOVERY_PHRASE` variable in `bot.py` (around line 86)
2. Enter your 12-word recovery phrase (keep it blank if you want to continue using individual keys)
3. The bot will use this phrase to generate addresses for all currencies

```python
MASTER_WALLET_RECOVERY_PHRASE = ""  # Enter your 12 words here
```

### Note
- The old individual private keys are kept for backward compatibility
- If you set the recovery phrase, it will be used; otherwise, the bot falls back to individual keys

---

## 2. Gift Code Wager Conditions

### What's New
Admins can now set a wager requirement when creating gift codes. Users must have wagered a certain amount in the casino before they can claim the code.

### How to Use
1. In admin panel, go to "Gift Code Management"
2. Create a new gift code
3. Follow the 4 steps:
   - **Step 1:** Enter the amount (e.g., `5.50`)
   - **Step 2:** Enter max number of claims (e.g., `100`)
   - **Step 3:** Enter wager requirement (e.g., `100` for $100, or `0` for no requirement)
   - **Step 4:** Code is generated!

### Example
```
Code: GIFT-ABC123XY
Amount: $10.00
Claims: 50/100 left (Wager: $100)
```

This means users need to have wagered at least $100 in the casino to claim this code.

### User Experience
When a user tries to claim with `/claim GIFT-ABC123XY`:
- If they've wagered enough: ✅ Code claimed!
- If not: ❌ Error message showing how much more they need to wager

---

## 3. Admin Panel Game Limits

### What's New
All games are now available in the min/max bet limits system, including:
- Keno
- Limbo
- HighLow
- All 5 new single emoji games

### How to Use
1. Go to admin panel
2. Select "Set Game Limits"
3. Choose "Minimum" or "Maximum"
4. Select the game from the list (now shows 19 games)
5. Enter the limit amount

### Complete Game List
- Blackjack, Coin Flip, Roulette, Dice Roll, Slots
- Predict, Tower, Mines, **Keno**, **Limbo**, **HighLow**
- PvP Dice, PvP Darts, PvP Goal, PvP Bowl
- **Emoji Darts**, **Emoji Soccer**, **Emoji Basket**, **Emoji Bowling**, **Emoji Slot**

---

## 4. Multi-Language Support

### Languages Added
1. 🇫🇷 **French** (fr)
2. 🇷🇺 **Russian** (ru)
3. 🇮🇳 **Hindi** (hi)
4. 🇪🇸 **Spanish** (es)
5. 🇨🇳 **Mandarin Chinese** (zh)
6. 🇬🇧 **English** (en) - default

### How It Works
- Each user can set their preferred language in bot settings
- The language preference is saved to their profile
- Bot will respond to them in their selected language (for translated strings)

### Translation System
Located in the `LANGUAGES` dictionary (around line 249):
```python
LANGUAGES = {
    "en": {...},
    "es": {...},
    "fr": {...},
    ...
}
```

### Current Status
- Language system is fully set up
- Key strings are translated (welcome, daily bonus, achievements, etc.)
- Future work: Apply translations to all bot messages

### User Commands
Users will see language options in bot settings menu.

---

## 5. Single Emoji Games

### What's New
A new section in Emoji Games with 5 quick-play games that use Telegram's native dice animations.

### Menu Structure
```
Emoji Games
├── 🎮 Regular Games (existing dice, darts, football, bowling)
└── 🎯 Single Emoji Games (NEW!)
    ├── 🎯 Darts (1.15x)
    ├── ⚽ Soccer (1.53x)
    ├── 🏀 Basket (2.25x)
    ├── 🎳 Bowling (5.00x)
    └── 🎰 Slot (14.5x)
```

### Game Details

#### 🎯 Single Dart
- **Win when:** Dart hits the table (values 3-6)
- **Multiplier:** 1.15x
- **Win chance:** 83%

#### ⚽ Single Soccer
- **Win when:** Goal scored (values 3, 4, 5)
- **Multiplier:** 1.53x
- **Win chance:** 60%

#### 🏀 Single Basket
- **Win when:** Ball goes in basket (values 4, 5)
- **Multiplier:** 2.25x
- **Win chance:** 40%

#### 🎳 Single Bowling
- **Win when:** Strike! (value 6)
- **Multiplier:** 5.00x
- **Win chance:** 16%

#### 🎰 Slot
- **Win when:** All same symbols (values 1, 22, 43, 64)
- **Multiplier:** 14.5x
- **Win chance:** 6.25%

### How to Play
1. Navigate to Games → Emoji Games → Single Emoji Games
2. Select a game
3. Tap "Play Game"
4. Enter bet amount
5. Watch the emoji animation!
6. Instant result

---

## 6. Group PvP Challenge System

### What's New
Users can now create public challenges in group chats that others can accept!

### Supported Games
- `/dice [amount]` - Dice challenge
- `/darts [amount]` - Darts challenge
- `/goal [amount]` - Football/Soccer challenge
- `/bowl [amount]` - Bowling challenge

### How It Works

#### Step 1: Create Challenge (Group Only)
```
User: /dice 5
Bot: Shows mode selection (Normal/Crazy)
User: Selects Normal
Bot: Shows rolls selection (1/2/3)
User: Selects 2
Bot: Creates and pins challenge message
```

#### Step 2: Challenge Message
```
🎲 GROUP CHALLENGE! 🎲

🎮 Game: DICE
👤 Host: @username
💰 Bet: $5.00
🎯 Mode: Normal (Highest wins)
🔢 Rolls: 2
🆔 Match ID: GC-250121123456-ABC123

Tap a button below to join!

[✅ Accept Challenge] [🤖 Play with Bot (Host Only)]
```

#### Step 3: Challenge Accepted
- **Any user** with sufficient balance can tap "Accept Challenge"
- **Only the host** can tap "Play with Bot"
- Both players' bets are deducted
- Game starts automatically

#### Step 4: Game Execution
1. Bot sends dice animations for each roll
2. Scores are calculated
3. Winner is determined based on mode:
   - **Normal:** Highest score wins
   - **Crazy:** Lowest score wins
4. Prize pool (2x bet) goes to winner
5. Stats are updated for both players

### Example Match Flow
```
Host rolls: 🎲 4, 🎲 6 = 10 total
Opponent rolls: 🎲 3, 🎲 5 = 8 total

Result (Normal Mode):
🎉 @host WINS!
Host: 10 | Opponent: 8
Prize: $10.00
```

### Important Features
- ✅ **Backward Compatible:** Old command formats still work
- ✅ **Pinned Messages:** Challenge is pinned (if bot has permissions)
- ✅ **Balance Checks:** Ensures both players can afford the bet
- ✅ **Stats Tracking:** Wins/losses recorded for both players
- ✅ **Tie Handling:** Bets returned if scores are equal

### Usage in Groups
```
/dice 5          → Create $5 dice challenge
/darts 10        → Create $10 darts challenge
/goal all        → Create challenge with all balance
/bowl 2.50       → Create $2.50 bowling challenge
```

### Usage in Private Chat (Still Works!)
```
/dice            → Shows Play vs Bot / Play vs Player options
/darts           → Shows Play vs Bot / Play vs Player options
```

---

## 🔧 Technical Notes

### Code Structure
All changes are in `bot.py`:
- Lines 86-94: Recovery phrase configuration
- Lines 247-320: Language system
- Lines 443-492: Single emoji games config
- Lines 333-339: Conversation states (added ADMIN_GIFT_CODE_WAGER)
- Lines 3351-3850: Group challenge system
- Lines 10159-10165: Callback handler registrations

### Database Updates
New fields added to user stats:
- `claimed_gift_codes`: List of claimed codes
- `language`: User's preferred language (default: "en")

### Conversation Handlers
- Gift code creation now has 4 steps instead of 3
- Single emoji games use SELECT_BET_AMOUNT state
- Group challenges use callback-based flow (no conversation state needed)

---

## 🐛 Testing Checklist

### Gift Codes
- [ ] Create code with wager requirement 0
- [ ] Create code with wager requirement 100
- [ ] Try claiming with insufficient wager
- [ ] Claim successfully after meeting wager

### Single Emoji Games
- [ ] Test all 5 games
- [ ] Verify win conditions work correctly
- [ ] Check balance updates
- [ ] Confirm stats tracking

### Group Challenges
- [ ] Create challenge in group
- [ ] Accept challenge from different user
- [ ] Play with bot as host
- [ ] Test normal and crazy modes
- [ ] Test 1, 2, and 3 rolls
- [ ] Verify old command format still works

### Admin Panel
- [ ] Check all 19 games appear in limits
- [ ] Set min/max for new games
- [ ] View gift codes with wager requirements

### Languages
- [ ] Change language in settings
- [ ] Verify preference is saved
- [ ] Check translated strings appear

---

## 📞 Support

If you encounter any issues or have questions about the new features:
1. Check this guide first
2. Test in a safe environment before production
3. Review the code comments in bot.py
4. Contact the developer if needed

---

**Last Updated:** January 21, 2025
**Version:** 2.0 - Major Feature Update
