# Methodology and Maintenance Rules

## Scope

The dataset compares standalone import inland / drayage costs for 40' HC containers offered by Maersk, MSC, and Hapag-Lloyd across U.S. lanes.

## Standalone truck calculation

### Direct truck quote

Use the carrier's applicable inland components to obtain the final standalone drayage amount. Depending on the SSL, those components may include haulage / on-carriage / landfreight, fuel, emergency fuel, inland fuel, and chassis.

### Combined Rail + Door quote

When the carrier does not expose a standalone local truck amount, calculate:

`Isolated Truck Drayage = Combined Rail + Door Total - Rail Ramp Only Total`

The two sides of the subtraction must use equivalent equipment and comparable charge components.

## Average SSL rate

`Average SSL Rate = sum(valid numeric SSL rates) / count(valid numeric SSL rates)`

Exclude `No Service`, `No Pricing`, and `Pending` from both numerator and denominator.

If only one SSL has a valid rate, the average equals that rate.

## Winner

The current SSL winner is the lowest valid numeric SSL rate for the same lane / ramp basis.

Do not declare a true market winner where competing carrier quotes may be based on different unidentified ramps. In those cases, preserve each known ramp option separately and mark unresolved carriers as `Pending`.

## Quote dates and staleness

Each carrier observation should retain its quote date when known. Historical prices are never overwritten; a new observation supersedes the previous one only for the current-view file.

For the original 13-lane baseline, the user stated the rates were obtained during the week immediately before 2026-06-13. Because an exact day was not specified, the history file records the quote period as 2026-06-01 through 2026-06-07 and leaves `quote_date` blank.

Current working validity: `Q3 2026`, unless a lane-specific validity note is more precise.

Normal refresh cadence: approximately every three months, accelerated when U.S. diesel or SSL fuel / inland surcharge changes materially.

## Status definitions

- `Available`: numeric rate can be compared
- `No Service`: SSL does not serve the lane
- `No Pricing`: usable pricing was not returned
- `Pending`: additional quote or ramp confirmation is required

## Historical changes

When a lane is re-quoted:

1. Add the new carrier observations to `data/rates_history.csv`.
2. Keep the previous observations in history with `is_current=false`.
3. Update `data/master_current.csv` to the newest valid snapshot.
4. Recalculate the average and winner.
5. Keep market / trucker benchmarks in `data/market_benchmarks.csv`; they must not enter the SSL average.
