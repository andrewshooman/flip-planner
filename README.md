# GE Tycoon: RuneLite plugin

*Developed in [andrewshooman/osrs-flipper](https://github.com/andrewshooman/osrs-flipper) (`plugin/`), together with the GE Tycoon web app and the research that trained and tested the model. This repository is the plugin on its own, for the RuneLite Plugin Hub.*

GE Tycoon brings the model behind the GE Tycoon web app (https://andrewshooman.github.io/osrs-flipper/) into RuneLite:

- **Next action, one at a time.** A card at the top of the panel says the single thing to do now, in this order:
  - collect finished offers
  - cancel a buy that's gone stale or blocks a sell
  - sell what you hold
  - place the next buy
  - or wait

  **Skip** suggests a different item. **Pause** turns the suggestions and highlights off.
- **GE highlights.** The GE screen outlines what to click next:
  - *Collect*
  - an empty slot's *Buy* button, or the item to sell in your inventory
  - the suggested item in the search results
  - the price and quantity buttons, until they match the suggestion
  - then *Confirm*

  Each slot also shows how long its offer has been up: green while it's fine, amber when it's getting old, red when it should be cancelled, blue when it needs collecting.
- **Notifications** when a buy or sell completes, and optionally when a new hourly plan has buys for your free slots. Optional game-chat messages too, with the profit after tax when a sell finishes, and a *GP drop*: a floating "+12.3K gp" at the right of the game view, like an XP drop.
- **Stats and price graphs.**
  - The Stats tab shows profit for today, the last 7 days and all time, plus flips, win rate, ROI, tax paid, daily profit bars, best and worst items, and recent flips.
  - Profit and gp per hour for the current session, since you logged in.
  - Right-click a suggestion or a holding for a 7-day price graph with the suggested buy and sell prices marked.
  - In the game too: right-click any tradeable item in your inventory, bank or GE and pick **Price graph**. The option only opens the graph window; it does nothing in the game.
- **Item tooltips.** Hover an item you're flipping, in your inventory, bank, the GE or a GE slot, to see what you paid, the breakeven after tax, what it's worth now, and the current advice (list, hold, sell or cut). Items in the current buy plan show the planned order.
- **Your rules.**
  - *Sell-only mode* stops new buys.
  - *Never suggest* blocks items; right-click a suggestion to add it.
  - *Min profit per order* hides orders that aren't worth the clicks.
  - *Use detected stack* plans with the money the game shows: coins and platinum tokens in your inventory and bank, gp in buy offers, and the items you're flipping at cost. Open your bank once so it can count your coins. The Profit section shows the detected stack either way.
- **Dip alerts (optional, off by default).**
  - **What it does:** while you play, GE Tycoon checks the newest 5-minute prices every 2 minutes. When a liquid item suddenly trades well under its 24-hour average on heavy volume, you get a notification and a chat line, for example *"Rune platebody dipped 11% to 37,200. Buy up to 53 at 37,200, sell at 41,480 (+210K); sell anyway after 6h."*
  - **Where it shows:** the item also appears in the panel, in the `gt` search and in the price hint for 30 minutes.
  - **Testing:** in a replay of two months of real 5-minute trades, this made about +56k a day on the month it wasn't tuned on. The same trades at random moments lost money.
  - **Catch:** it only pays if you act within about 20 minutes, and a single trade can lose money. Turn it on under *Dip alerts* in the settings.
- **What to buy.** The side panel lists the model's buy orders for your stack: the item, price, quantity, where to sell, and the expected profit. Only orders worth the clicks are shown, so some check-ins have nothing to buy. That's fine.
- **What to do with what you hold.** For each item you hold, the panel says list at a price, hold (with the breakeven price), sell now, or cut.
- **Help in the GE:**
  - **Search:** type `gt` in the GE item search to list the suggested items.
  - **Price and quantity:** when the price or quantity box is open, a line in the chatbox shows the suggested number, for example *"GE Tycoon suggests: buy at 1,234 gp each"*. You type it in yourself. RuneLite doesn't allow plugins to type into the chatbox.
- **Trade tracking.** Every GE offer you make is recorded with its fills and average prices, including fills that happened while you were logged out. Your profit after tax shows in the panel.
- **Permanent history on your PC.** Trades are saved as you make them in RuneLite's data folder for the plugin: `.runelite/plugin-data/ge-tycoon/` in your home folder. **Export trades…** at the bottom of the panel saves a copy anywhere you choose (for example `D:\GE-Tycoon`). The web app's **Import RuneLite** button reads either file. If you tested the plugin under its earlier name, Flip Planner, move your files from `.runelite/plugin-data/flip-planner/` to `.runelite/plugin-data/ge-tycoon/`.

Nothing is automated: you place every offer and type every number. Prices come from the OSRS Wiki's public real-time prices API (https://prices.runescape.wiki), requested with a descriptive User-Agent. No player data is sent anywhere.

## Installing

Once it's accepted on the Plugin Hub: in RuneLite, open the wrench (Configuration) → **Plugin Hub** → search **GE Tycoon** → **Install**.

## What to try, and what to report back
- [ ] The side panel fills in within about 10 seconds of opening it: buy orders, then
      your items, then profit.
- [ ] Type `gt` in the GE item search. Do the suggested items show up as the results?
- [ ] Start a buy for a suggested item and click **Enter price**. Does a line
      *"GE Tycoon suggests: buy at … gp each"* appear in the chatbox?
- [ ] The same for **Enter quantity**.
- [ ] Place an offer, let it fill (or partly fill), and log out and back in. Is the trade
      in the panel's profit and in
      `%USERPROFILE%\.runelite\plugin-data\ge-tycoon\account-<number>.json`, counted once?

To build `ge-tycoon.jar` yourself, you need JDK 11 or newer. Run `./gradlew jar` here, and the jar is in `build/libs/`.

## Development

```
./gradlew build      # compile + unit tests
```

- `src/main/java/com/getycoon/engine/`: the model, a Java port of the web app's `js/` engine. Tests in `src/test/.../engine/` check it against fixtures generated from the JS (`plugin/tools/make-fixtures.js` in osrs-flipper).
- `tracking/`:
  - `OfferTracker`: GE offer updates in, offers and fills out. It handles the login burst, and offers that changed while you were logged out.
  - `TradeStore`: the trade file and the raw offer log.
- `OfferShortcuts`: the suggested price/quantity line in the chatbox and the GE search code.
- `GeOverlay`, `NextAction`: the next action and the GE highlights. `ItemTooltipOverlay`, `ItemTips`: the item tooltips.
- `ui/TycoonPanel`: the side panel.
