# Flip Planner: RuneLite plugin

*Developed in [andrewshooman/osrs-flipper](https://github.com/andrewshooman/osrs-flipper) (`plugin/`), together with the GE Flipper web app and the research that trained and tested the model. This repository is the plugin on its own, for the RuneLite Plugin Hub.*

Flip Planner brings the model behind the GE Flipper web app (https://andrewshooman.github.io/osrs-flipper/) into RuneLite:

- **What to buy.** The side panel lists the model's buy orders for your stack: the item, price, quantity, where to sell, and the expected profit. Only orders worth the clicks are shown, so some check-ins have nothing to buy. That's fine.
- **What to do with what you hold.** For each item you hold, the panel says list at a price, hold (with the breakeven price), sell now, or cut.
- **Help in the GE:**
  - **Search:** type `fp` in the GE item search to list the suggested items.
  - **Price and quantity:** when the price or quantity box is open, a line in the chatbox shows the suggested number, for example *"Flip Planner suggests: buy at 1,234 gp each"*. You type it in yourself. RuneLite doesn't allow plugins to type into the chatbox.
- **Trade tracking.** Every GE offer you make is recorded with its fills and average prices, including fills that happened while you were logged out. Your profit after tax shows in the panel.
- **Permanent history on your PC.** Trades are saved as you make them in RuneLite's data folder for the plugin: `.runelite/plugin-data/flip-planner/` in your home folder. **Export trades…** at the bottom of the panel saves a copy anywhere you choose (for example `D:\Flip-Planner`). The web app's **Import RuneLite** button reads either file.

Nothing is automated: you place every offer and type every number. Prices come from the OSRS Wiki's public real-time prices API (https://prices.runescape.wiki), requested with a descriptive User-Agent. No player data is sent anywhere.

## Installing

Once it's accepted on the Plugin Hub: in RuneLite, open the wrench (Configuration) → **Plugin Hub** → search **Flip Planner** → **Install**.

## What to try, and what to report back
- [ ] The side panel fills in within about 10 seconds of opening it: buy orders, then
      your items, then profit.
- [ ] Type `fp` in the GE item search. Do the suggested items show up as the results?
- [ ] Start a buy for a suggested item and click **Enter price**. Does a line
      *"Flip Planner suggests: buy at … gp each"* appear in the chatbox?
- [ ] The same for **Enter quantity**.
- [ ] Place an offer, let it fill (or partly fill), and log out and back in. Is the trade
      in the panel's profit and in
      `%USERPROFILE%\.runelite\plugin-data\flip-planner\account-<number>.json`, counted once?

To build `flip-planner.jar` yourself, you need JDK 11 or newer. Run `./gradlew jar` here, and the jar is in `build/libs/`.

## Development

```
./gradlew build      # compile + unit tests
```

- `src/main/java/com/flipplanner/engine/`: the model, a Java port of the web app's `js/` engine. Tests in `src/test/.../engine/` check it against fixtures generated from the web app's JS (`plugin/tools/make-fixtures.js` in osrs-flipper).
- `tracking/`:
  - `OfferTracker`: GE offer updates in, offers and fills out. It handles the login burst, and offers that changed while you were logged out.
  - `TradeStore`: the trade file and the raw offer log.
- `OfferShortcuts`: the suggested price/quantity line in the chatbox and the GE search code.
- `ui/FlipperPanel`: the side panel.
