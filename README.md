# Allog SSL Inland Pricing

Central rate log for standalone import inland / drayage pricing offered by steamship lines (SSLs) for 40' HC containers in the United States.

## Purpose

This repository is the durable source of truth for:

- Maersk, MSC, and Hapag-Lloyd inland / drayage rates by lane
- historical rate snapshots instead of overwriting old values
- quote dates and validity periods so stale pricing can be identified
- ramp assumptions and ambiguous-ramp cases
- isolated drayage calculations when a carrier only provides a combined Rail + Door rate
- current SSL lane winners and average SSL rates
- separate non-SSL / market benchmarks where available

## Pricing methodology

When a carrier provides a clean truck / door rate, the applicable haulage, fuel, chassis, and other inland components are summed as required.

When a carrier provides a Combined Rail + Door rate rather than standalone drayage, the local truck portion is isolated as:

`Isolated Truck Drayage = Combined Rail + Door Total - Rail Ramp Only Total`

Only valid numeric SSL rates are included in the average. `No Service`, `No Pricing`, and `Pending` are excluded.

## Data files

- `data/master_current.csv` — current user-facing lane comparison
- `data/rates_history.csv` — carrier-level historical observations and calculation detail
- `data/market_benchmarks.csv` — non-SSL / market benchmark pricing kept separate from SSL averages
- `docs/methodology.md` — operating rules for maintaining the dataset

## Refresh policy

The working validity period for the current dataset is **Q3 2026** unless a lane-specific note states otherwise.

Rates should normally be refreshed approximately every three months, with earlier re-quoting when U.S. diesel / fuel movement or carrier surcharge changes materially affect inland pricing.

## Status values

- `Available` — valid numeric SSL rate logged
- `No Service` — carrier does not offer the lane
- `No Pricing` — no usable price was returned
- `Pending` — quote or ramp confirmation still required

## Ramp ambiguity

Some destinations can reasonably be served from more than one inland rail ramp. Those alternatives are stored as separate lane options until the actual SSL routing basis is confirmed. This prevents comparing rates derived from different ramps as though they were directly equivalent.
