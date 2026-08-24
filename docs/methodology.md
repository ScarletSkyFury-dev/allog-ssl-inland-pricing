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

Most Allog provider rates are quoted on an operational All-in basis because this is how clients commonly request the service. Only selected lanes / clients routinely request a stripped-down 2-day-chassis-only provider rate.

Therefore, always preserve the actual Allog/provider quote exactly as received. When management needs an approximate apples-to-apples analytical comparison, show a separate **indicative 2-day equivalent range**. This range is an analytical estimate only and must never be presented as an actual provider quote.

Current standalone reference values remain useful for context:

- Chassis: `USD 45 per day`
- Storage: `USD 50 per day`
- Prepull: `USD 150`

However, All-in packages are commercially bundled. The difference between a provider's All-in rate and its 2-day-chassis-only rate is **not necessarily the arithmetic sum of standalone accessorials**. In practice the package differential can be lower — for example approximately USD 120, 150, 175, or 200 — depending on the provider and lane.

Working comparison basis: **2 days chassis only**, unless a different basis is explicitly required.

### Normal Allog All-in standard

Unless a lane is specifically documented otherwise, the normal Allog All-in package for this comparison is:

- 2 days chassis
- prepull
- 1 day storage

For management reporting, use an indicative bundled adjustment range of **USD 120 to USD 200** rather than automatically deducting USD 200.

`Indicative 2-day equivalent range = All-in quote - USD 200 through All-in quote - USD 120`

The lower end represents the larger assumed package adjustment; the upper end represents the more conservative adjustment.

### Known enhanced-package exception

**Hatfield, MA** is currently the relevant enhanced-package lane in the management comparison. Its special All-in package includes:

- 3 days chassis
- 2 days storage
- prepull

Relative to the normal All-in package, this adds one chassis day and one storage day. Using the current reference values, that adds USD 95 of nominal service value. For analytical reporting, use an estimated total adjustment range of **USD 215 to USD 295** from the Hatfield All-in rate when estimating a 2-day-chassis-only equivalent.

Portland, ME has the same enhanced package, but is currently excluded from the numerical SSL management comparison because no comparable current SSL routing is available.

### Actual 2-day rates

Where Allog already has an actual 2-day provider rate, no normalization is required. Use the actual rate directly.

### Reporting convention

Show both where relevant:

1. **Actual Allog commercial rate** — the real rate and service package offered to the client.
2. **Indicative 2-day equivalent range** — an analytical range used only to improve comparability against SSL pricing.

Savings based on the normalized range should also be expressed as a range, not as a single fixed number.

## Average SSL rate

`Average SSL Rate = sum(valid numeric SSL rates) / count(valid numeric SSL rates)`

Exclude `No Service`, `No Pricing`, `Pending`, and routing-incomparable options from both numerator and denominator.

If only one SSL has a valid comparable rate, the average equals that rate.

## Winner

The current SSL winner is the lowest valid numeric SSL rate for the same lane / ramp basis.

Do not declare a true market winner where competing carrier quotes may be based on different unidentified ramps. In those cases, preserve each known ramp option separately and mark unresolved carriers as `Pending` or exclude the lane from an apples-to-apples management comparison.

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
- `Excluded - different routing`: service exists but does not use a comparable routing / ramp basis for the management comparison

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
