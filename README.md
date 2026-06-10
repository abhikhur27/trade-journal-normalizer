# Trade Journal Normalizer

Python CLI that turns messy broker-export CSVs into one consistent trade-journal schema plus a per-symbol summary JSON.

## Why it exists

Different exports label the same fields differently: `Ticker` vs `Symbol`, `Qty` vs `Shares`, `Action` vs `Side`. This tool normalizes those variants so the resulting journal is easier to archive, diff, or feed into later analysis.

## Features

- Infers common trade columns from broker-style aliases.
- Normalizes actions into `BUY` / `SELL`.
- Computes signed quantity, gross notional, and net cash flow.
- Writes a portable normalized CSV.
- Writes a summary JSON with per-symbol totals and whole-journal totals.
- Optional exact-row dedupe for duplicated export lines.

## Normalized output schema

- `date`
- `symbol`
- `action`
- `quantity`
- `price`
- `fees`
- `signed_quantity`
- `gross_notional`
- `cash_flow`
- `note`
- `source_row`

## Usage

```bash
python trade_journal_normalizer.py path/to/trades.csv
```

With explicit outputs:

```bash
python trade_journal_normalizer.py examples/sample_trades.csv ^
  --output-csv out/normalized.csv ^
  --summary-json out/summary.json ^
  --drop-duplicates
```

## Accepted header aliases

Required fields are inferred from common variants:

- `date`: `Date`, `Trade Date`, `Filled At`, `Timestamp`
- `symbol`: `Symbol`, `Ticker`, `Instrument`
- `side`: `Side`, `Action`, `Type`, `Instruction`
- `quantity`: `Quantity`, `Qty`, `Shares`, `Filled Quantity`
- `price`: `Price`, `Avg Price`, `Average Price`, `Fill Price`

Optional fields:

- `fees`: `Fees`, `Commission`
- `note`: `Note`, `Description`, `Memo`

## Example workflow

```bash
python trade_journal_normalizer.py examples/sample_trades.csv
type examples/sample_trades.normalized.csv
type examples/sample_trades.summary.json
```

## Local verification

```bash
python -m py_compile trade_journal_normalizer.py
python trade_journal_normalizer.py examples/sample_trades.csv --drop-duplicates
```
