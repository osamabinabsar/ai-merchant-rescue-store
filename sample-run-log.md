# Sample run log (how the free samples were generated)

This log records the exact commands used to produce the free sample artifacts in
this folder, plus the verification performed. Goal: prove the samples are
deterministic outputs of the bundled scripts on the bundled fixtures — not
hand-written or smoothed numbers.

Environment (this run):
- OS: Windows 11, shell: bash (git-bash/MSYS)
- Python: 3.11 (hermes runtime) — scripts use stdlib only
- Working extraction base: `C:/Users/Anagh/business-extract/`
- Product zips in: `C:/Users/Anagh/business/products/`
- Run date: 2026-10-28

---

## 1. Chargeback Defense Kit sample
Artifact: `free-chargeback-defense-kit-sample.md`

Unzip + set up (one-time):
```
unzip chargeback-defense-kit-v1.zip -d chargeback-defense-kit-v1
mkdir -p cb-case/raw
cp chargeback-defense-kit-v1/chargeback-defense-kit/tests/fixtures/sample-disputes.csv cb-case/disputes.csv
cp chargeback-defense-kit-v1/chargeback-defense-kit/tests/fixtures/sample-evidence.csv cb-case/evidence-index.csv
```

Generate (run from `cb-case/`):
```
python C:/Users/Anagh/business-extract/chargeback-defense-kit-v1/chargeback-defense-kit/scripts/ingest_dispute.py disputes.csv --source auto --out .
python C:/Users/Anagh/business-extract/chargeback-defense-kit-v1/chargeback-defense-kit/scripts/evidence_check.py --cases cases.csv --evidence evidence-index.csv --out out
python C:/Users/Anagh/business-extract/chargeback-defense-kit-v1/chargeback-defense-kit/scripts/assemble_packet.py --case dp_1001 --cases cases.csv --evidence evidence-index.csv --out out
```

Observed output:
- `Ingested 3 new dispute(s), skipped 0 duplicate(s). Report: ./out/ingest-report.md`
- `Total open money at stake: 458.50`
- `dp_1001: 92/100 [READY]`, `dp_1002: 57/100 [NEEDS EVIDENCE]`,
  `dp_1003: 38/100 [NEEDS EVIDENCE]`
- `Packet assembled: out\packet-dp_1001 (score 92/100, ready)`

Verification:
- `python scripts/selfcheck.py` -> `32 checks, 0 failed`
- `out/ingest-report.md`, `out/evidence-scorecard.md`, `out/missing.csv`,
  `out/packet-dp_1001/cover-letter.md`, `out/packet-dp_1001/evidence-index.md`,
  `out/packet-dp_1001/manifest.json` all generated; numbers in the sample
  copied verbatim from those files.

---

## 2. Bookkeeping Rescue sample
Artifact: `free-bookkeeping-rescue-sample.md`

Unzip + set up (one-time):
```
unzip bookkeeping-rescue-v1.zip -d bookkeeping-rescue-v1
mkdir -p books
cp bookkeeping-rescue-v1/bookkeeping-rescue/templates/rules.example.csv books/rules.csv
```

Generate (run from `C:/Users/Anagh/business-extract/`):
```
python C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/scripts/ingest.py C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/tests/fixtures/sample_bank.csv --account "Chase Checking" --out C:/Users/Anagh/business-extract/books --year 2026
python C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/scripts/ingest.py C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/tests/fixtures/sample_card.json --account "Chase Card" --out C:/Users/Anagh/business-extract/books --year 2026
python C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/scripts/ingest.py C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/tests/fixtures/sample.ofx --account "Wells Fargo Checking" --out C:/Users/Anagh/business-extract/books --year 2026

python C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/scripts/categorize.py --ledger C:/Users/Anagh/business-extract/books/ledger.csv --rules C:/Users/Anagh/business-extract/books/rules.csv --out C:/Users/Anagh/business-extract/books

python C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/scripts/reconcile.py --ledger C:/Users/Anagh/business-extract/books/ledger.csv --out C:/Users/Anagh/business-extract/books --closing "Chase Checking=2026-02-28:7848.95"

python C:/Users/Anagh/business-extract/bookkeeping-rescue-v1/bookkeeping-rescue/scripts/report.py --ledger C:/Users/Anagh/business-extract/books/ledger.csv --out C:/Users/Anagh/business-extract/books/out --year 2026
```

Observed output:
- `Added 16 rows to ...\books\ledger.csv`
- `Added 6 rows to ...\books\ledger.csv`
- `Added 3 rows to ...\books\ledger.csv`
- `Categorized 25 rows (2 need review)`
- `FAIL:0 WARN:5 -> ...\reconciliation-report.md`
- `Reports written to ...\books\out (pnl, deductions, cashflow, tax-packet)`

Verification:
- `python scripts/selfcheck.py` -> `25 checks, 0 failed`
- `out/ingest-report.md`, `out/categorize-report.md`, `out/review-queue.csv`,
  `out/reconciliation-report.md`, `out/pnl.md`, `out/pnl.csv`, `out/deductions.md`,
  `out/cashflow.md`, `out/tax-packet.md` all generated.
- Numbers in the sample copied verbatim from those files; no re-computation by hand.
- One caveat disclosed in the sample: the provided closing figure (7848.95) is
  the statement closing balance, which the script compares as net movement; the
  WARN names the exact 5000.00 gap rather than nudging numbers.

---

## 3. Merchant Fee Audit sample (existing, reference)
Artifact: `free-merchant-fee-audit-sample.md` (pre-existing)

Source run (per the sample header and product README):
```
python scripts/ingest_statement.py tests/fixtures/sample-statement.csv --out merchant-audit  (path naming varies by run)
python scripts/fee_audit.py --statement merchant-audit/statement.csv --out merchant-audit/out
```
Verification on this machine:
- `python scripts/selfcheck.py` -> `33 checks, 0 failed`
- Sample numbers (effective rate 3.21%, weighted benchmark 2.54%, annualized
  volume 383375.80, fees 12318.36, savings 1491.02 / 2982.03) left untouched;
  they trace to `out/audit-report.md` + `out/audit-data.json` from the
  bundled `tests/fixtures/sample-statement.csv`.

We did not regenerate this sample in this pass; it is recorded here as a
verification reference only, to avoid silently altering published figures.

---

## Determinism notes
- All three scripts are stdlib-only, deterministic: same input + same rules ->
  same output (re-running changes only `assembled_on`/run dates where the tool
  stamps them).
- No account, API key, or network access was used for any run.
- Fixture inputs are the bundled `tests/fixtures/*` files shipped inside each
  product zip, so a buyer can reproduce every number above end-to-end.
- Self-checks (chargeback 32/32, bookkeeping 25/25, merchant 33/33) passed
  with 0 failures on 2026-10-28.
