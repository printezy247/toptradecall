# Top Trade Calls

An original Pine Script v6 indicator suite that scores buy/sell confluence on
a chart, so you can compare it against your own manual analysis before
taking a trade. This suite is original work written from scratch -- it is
**not** derived from, adapted from, or a modification of any third-party
commercial TradingView script.

## How the score works

Every script in this suite computes the same `-100..100` confluence score
from three components:

1. **Trend** -- the spread between a fast and slow EMA (defaults: 9 / 21),
   normalized by ATR(14).
2. **Momentum** -- how far RSI (default length 14) sits from the neutral 50
   level.
3. **Volume pressure** -- net directional volume (up-bars vs. down-bars,
   based on `ohlc4`) over a lookback window (default 8 bars).

The three components are combined as a weighted average
(`trend * 0.4 + momentum * 0.3 + volume * 0.3`) into the final score. The
score is classified as:

- **BUY** when the score is at or above the Buy Threshold (default `+20`)
- **SELL** when the score is at or below the Sell Threshold (default `-20`)
- **NEUTRAL** otherwise

This is a confluence tool, not a standalone trading system -- use it to
support your own read of the chart, not to replace it. Past behavior of any
score or threshold is no guarantee of future results.

## Files in this suite

| File | What it does |
|---|---|
| `Top Trade Calls.pine` | Base script. Computes the score on whatever timeframe the chart is currently set to. |
| `1m / 5m / 15m / 30m / 1H / 2H / 4H / 1D / 1W Top Trade Calls.pine` | Same scoring engine, but pinned (via `input.timeframe`, adjustable) to that specific timeframe regardless of the chart's own timeframe -- e.g. add "1H Top Trade Calls" to any chart to see the 1-hour bias. |
| `MTF Top Trade Calls.pine` | Multi-timeframe dashboard. Shows the score for 1W, 1D, 4H, 1H, 30m, 15m and 5m at once in an on-chart table, plus one more timeframe you pick via the "Highlighted Resolution" input. |

## Adding a script to TradingView

1. Open TradingView and open the **Pine Editor** tab at the bottom of the
   chart.
2. Click **Open** > **New blank script**, then delete the placeholder code.
3. Open the desired `.pine` file from this folder and paste its full
   contents into the editor.
4. Click **Save**, then **Add to Chart**.
5. Adjust the inputs (EMA lengths, RSI length, volume lookback, thresholds,
   label size, table position) from the indicator's settings (gear icon) to
   taste.

Repeat for each script you want on your chart -- there's no restriction on
how many you run at once, and no license file to manage since this is your
own code.

## Setting alerts

Each script defines `alertcondition()`s for BUY and SELL transitions
(crossing above the Buy Threshold or below the Sell Threshold). To use them:

1. Right-click the chart and choose **Add Alert**, or click the alarm-clock
   icon in the toolbar.
2. Under **Condition**, pick the script (e.g. "Top Trade Calls Buy (1H)").
3. Configure how you want to be notified, then click **Create**.

## Notes on the multi-timeframe scripts

The per-timeframe and MTF scripts use `request.security()` with
`gaps=barmerge.gaps_off` and `lookahead=barmerge.lookahead_off` -- the
standard non-repainting pattern for pulling higher-timeframe data into a
lower-timeframe chart. If you point a script's "Timeframe to Analyze" input
at a *lower* timeframe than your chart (e.g. running the "1H" script on a
daily chart with the input changed to "5"), TradingView may show a
repainting warning; this is expected Pine behavior, not a bug in the script.
