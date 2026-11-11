# Chris Casino 2.5 — Expanded Table Games

Private, play-money Flask casino for friends. This version keeps the existing player PINs, PostgreSQL persistence, leaderboard, transaction history, and admin balance controls while expanding the casino to six playable table games.

## Games

### Blackjack
- 6-deck Blackjack
- Dealer stands on 17
- Blackjack pays 3:2
- Double Down
- Perfect Pairs side bet
- 21+3 side bet

Perfect Pairs paytable in this build:
- Mixed Pair — 6:1
- Colored Pair — 12:1
- Perfect Pair — 25:1

21+3 paytable:
- Flush — 5:1
- Straight — 10:1
- Three of a Kind — 25:1
- Straight Flush — 40:1
- Suited Trips — 100:1

### Roulette
Single-zero European Roulette with:
- Red / Black
- Odd / Even
- 1–18 / 19–36
- Exact Number
- Zero
- Dozens
- Columns
- Basket 0-1-2-3
- Snake Bet

### Craps
- Pass Line
- Don't Pass
- Persistent point logic
- Field
- Any 7
- Any Craps
- Yo 11
- Snake Eyes
- Boxcars
- Hard 4 / 6 / 8 / 10

Hardway bets remain active until they win, seven-out, or the number rolls the easy way. The active Craps point and hardways are server-memory state, like an in-progress Blackjack hand. Balances are always written to the database.

### Baccarat
8-deck Punto Banco using standard third-card rules.

Main bets:
- Player — 1:1
- Banker — 0.95:1
- Tie — 8:1

Side bets:
- Player Pair — 11:1
- Banker Pair — 11:1
- Player / Banker Dragon Bonus

Balances use whole credits, so a Banker commission payout is rounded down to the nearest whole credit.

### DJ Wild
Five-card poker using one 53-card deck. All four deuces and the Joker are wild.

Required:
- Ante
- matching Blind
- Play wager is 2× Ante after seeing the hand

Side bets:
- Trips
- Two-Way Bad Beat

The Blind and side-bet paytables are modeled on commonly published DJ Wild tables.

### I Luv Suits
Seven cards each to player and dealer. The longest flush wins; same-length flushes compare card ranks. Dealer qualifies with at least a 3-card 9-high flush.

Play sizing:
- 2–4 card flush: up to 1× Ante
- 5-card flush: up to 2× Ante
- 6–7 card flush: up to 3× Ante

Side bets:
- Flush Rush
- Super Flush Rush

Flush Rush:
- 4-card — 1:1
- 5-card — 10:1
- 6-card — 100:1
- 7-card — 300:1

Super Flush Rush:
- 3-card straight flush — 7:1
- 4-card — 60:1
- 5-card — 100:1
- 6-card — 1000:1
- 7-card — 8000:1

## Game info buttons

Every game card now has an `ⓘ` button. It opens an in-app explanation covering:
- the premise of the game
- how a round works
- main wagers
- side bets
- payout schedule

## Default wager

The previous $100 default has been changed to **$10** for all main game wager inputs. Optional side bets default to $0.

## Persistence

With `DATABASE_URL` configured, PostgreSQL stores:
- player balances
- starting balances
- transaction ledger
- roulette history

An in-progress decision hand (Blackjack, DJ Wild, I Luv Suits) and the live Craps point/hardway state are held in server memory. If Render restarts in the middle of one of those rounds, the already-recorded wagers remain in the transaction ledger but the unfinished game state is lost.

## Local Windows setup

```powershell
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python app.py
```

Open:

```text
http://127.0.0.1:5000
```

Without `DATABASE_URL`, local mode automatically uses `casino.db`.

## Render environment variables

```text
DATABASE_URL=<PostgreSQL connection URL>
SECRET_KEY=<long random secret>
ADMIN_PIN=<private admin PIN>
COOKIE_SECURE=1
```

Build command:

```text
pip install -r requirements.txt
```

Start command:

```text
gunicorn app:app
```

## Admin panel

The correct admin PIN unlocks:
- exact balance editing for any player
- reset all players to configured starting balances

Both operations are protected server-side, not only hidden in the browser UI.

## Play-money only

This project is intended only for fictional credits among friends. It has no deposit, withdrawal, cash-out, prize-redemption, or payment system.
