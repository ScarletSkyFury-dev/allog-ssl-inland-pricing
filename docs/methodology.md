# Methodology and Maintenance Rules

## Scope

The dataset compares standalone import inland / drayage costs for 40' HC containers offered by Maersk, MSC, and Hapag-Lloyd across U.S. lanes.

## Report field mapping

For the Allog volume report used to define the Top 10 lanes:

- `Destino Nome` = port / ocean gateway origin shown in the report
- `Destino Delivery Nome` = actual customer delivery location
- `Destino Final Nome`, when populated = inland rail ramp used before final delivery

A carrier quote may separately show a first POD that is not the final rail ramp. For example, Hapag may show Houston as first POD while the container continues inland by rail before the local truck delivery. Do not treat the displayed POD as the drayage ramp unless the carrier actually identifies it as such.

When the SSL does not expose the inland rail provider or final rail ramp, keep the local drayage amount pending until a comparable Rail-only quote or other evidence allows the truck portion to be isolated reliably.

## Standalone truck calculation

### Direct truck quote

Use the carrier's applicable inland components to obtain the final standalone drayage amount. Depending on the SSL, those components may include haulage / on-carriage / landfreight, fuel, emergency fuel, inland fuel, and chassis.

### Combined Rail + Door quote

When the carrier does not expose a standalone local truck amount, calculate:

`Isolated Truck Drayage = Combined Rail + Door Total - Rail Ramp Only Total`

The two sides of the subtraction must use equivalent equipment and comparable charge components.

## Allog provider quote normalization

Most Allog provider rates are quoted on an operational all-in basis rather than on the same stripped-down basis used for the SSL comparison. Only selected clients, such as the Verdi lane example, routinely request a 2-day-chassis-only provider rate.

Therefore, always preserve the actual Allog/provider quote exactly as received. When management needs an approximate apples-to-apples analytical comparison, a separate normalized estimate may be calculated, but it must never be presented as an actual provider quote.

Current standard reference values for analytical normalization:

- Chassis: `USD 45 per day`
- Storage: `USD 50 per day`
- Prepull: `USD 150`

Working normalization basis: **2 days chassis only**, unless a different comparison basis is explicitly required.

### Normal Allog All-in standard

Unless a lane is specifically documented otherwise, the normal Allog All-in package for this comparison is:

- 2 days chassis
- prepull
- 1 day storage

For that normal package:

`Normalized 2-day-chassis estimate = All-in quote - 1 storage day - prepull`

Using the current reference values:

`Normalized estimate = All-in quote - 50 - 150`

No chassis deduction is made because the actual package already includes the 2-day chassis comparison basis.

### Known exceptions

At present, **Hatfield, MA** and **Portland, ME** are the only documented exceptions to the normal All-in standard. Their special All-in package includes:

- 3 days chassis
- 2 days storage
- prepull

For those two lanes:

`Normalized 2-day-chassis estimate = All-in quote - 1 extra chassis day - 2 storage days - prepull`

Using the current reference values:

`Normalized estimate = All-in quote - 45 - 100 - 150`

This is an analytical approximation. Special All-in rates may be commercially bundled, so the deduction does not prove that the provider would actually quote the normalized amount as a standalone rate.

For reporting, show both where relevant:

1. **Actual Allog All-in rate** — the real commercial rate and service package.
2. **Normalized Allog estimate** — the analytical 2-day-chassis-only equivalent used only to improve comparability against SSL pricing.

## Average SSL rate

`Average SSL Rate = sum(valid numeric SSL rates) / count(valid numeric SSL rates)`

Exclude `No Service`, `No Pricing`, and `Pending` from both numerator and denominator.

If only one SSL has a valid rate, the average equals that rate.

## Winner

The current SSL winner is the lowest valid numeric SSL rate for the same lane / ramp basis.

Do not declare a true market winner where competing carrier quotes may be based on different unidentified ramps. In those cases, preserve each known ramp option separately and mark unresolved carriers as `Pending`.

## Quote dates and staleness

Each carrier observation should retain its quote date when known. Historical prices are never overwritten or deleted simply because a newer quote exists.

For the original 13-lane baseline, the user stated the rates were obtained during the week immediately before 2026-06-13. Because an exact day was not specified, the history file records the quote period as 2026-06-01 through 2026-06-07 and leaves `quote_date` blank.

Current working validity: `Q3 2026`, unless a lane-specific validity note is more precise.

Normal refresh cadence: approximately every three months, accelerated when U.S. diesel or SSL fuel / inland surcharge changes materially.

## Status definitions

- `Available`: numeric rate can be compared
- `No Service`: SSL does not serve the lane
- `No Pricing`: usable pricing was not returned
- `Pending`: additional quote or ramp confirmation is required

## Historical changes and snapshots

The repository is intended to support trend analysis over time, not just the latest price.

When a lane is re-quoted:

1. Preserve every previous carrier observation in `data/rates_history.csv`.
2. Add the new observation as a new dated record; do not erase or replace the old observation.
3. Quarterly / refresh-cycle working files such as `data/top10_refresh_2026_q3.csv` remain separate snapshots and are retained after the next cycle is created.
4. `data/master_current.csv` may be used as a convenience view of the latest completed comparison, but updating that view must never remove the underlying historical records or prior quarterly snapshots.
5. Recalculate the current average and winner only from the valid rates for the same dated / routing basis.
6. Keep market / trucker benchmarks in `data/market_benchmarks.csv`; they must not enter the SSL average.

This structure allows later comparisons of carrier pricing, Allog performance, fuel-driven changes, and lane competitiveness by quarter.
