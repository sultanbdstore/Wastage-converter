# Wastage Converter

Turns the daily **Wastage Control Status** Excel (knit garments) into a management dashboard workbook, with no row skipped silently.

Output sheets: Dashboard, Daily Tracker, Monthly, Yearly, Drivers, Data, Notes, Conversion Report, Calc.

## Three ways to run it

| Route | Where | Needs |
|---|---|---|
| Python CLI (`src/`) | your PC | Python 3.9+, `pip install -r requirements.txt` |
| Browser page (`web/index.html`) | any browser | internet (loads Pyodide + SheetJS), nothing to install |
| Google Sheets (`google-apps-script/`) | Google account | see `google-apps-script/SETUP_GUIDE.txt` |

## Python quick start

```bash
pip install -r requirements.txt
python src/convert_wastage.py "My_Regular_File.xlsx" "Dashboard.xlsx"
# options:  --basis "With Non-fabric"   --target 0.22
```

Open the result in Excel and click **Enable Editing** so formulas calculate.

Optional check (needs stored formula values, so recalculate first; `tools/recalc.sh` uses LibreOffice):

```bash
tools/recalc.sh Dashboard.xlsx
python src/verify_conversion.py "My_Regular_File.xlsx" "Dashboard.xlsx"
```

## Business rules

- Total kg = Eff loss + Dia variation + Cut panel + End/Rej + Sewing machine + Rib + Others (only on the "With Non-fabric" basis) + Sample.
- Cutting = Eff + Dia. Panel = Cut panel + End/Rej. Sewing floor = Sewing machine + Rib (+ Others) + Sample.
- Total % = total kg / cut qty. Periods are weighted (sum / sum), never an average of daily %.
- **Sample is shown in kg only, inside the Sewing floor.** It is never expressed as a % of cut qty.
- Target 22% is a placeholder; change it with `--target` or the Target cell on the Dashboard.
- The report date auto-detects the newest dated row. Switch `IncPartial` controls whether partly filled days count.

## Safeguards

- Header guard: stops if the source headers or layout differ from the expected ones (it never guesses).
- Every source row is accounted for: scanned = data + blank + trailer + undated.
- Flags typed-over formula cells, text dates, and likely month-typo dates (repaired only when bounded by a following row; never at the end of the data).
- Recomputes the source's own total and % per row and compares with what is stored.
- Cross-checks against the other source sheet.
- Conversion Report shows PASS / REVIEW / FAIL; deterministic output.

## Layout

```
src/convert_wastage.py     converter
src/verify_conversion.py   independent 20-check verifier
tools/build_web.py         re-embed the converter into web/index.html
tools/recalc.sh            LibreOffice recalculation helper
web/icons/, manifest      logo set (any + maskable 192/512, favicon, apple-touch) - rebuild: python tools/make_icons.py
assets/                    logo source photo, 1024 master, GitHub social-preview.png (1280x640)
tests/                     pytest suite (synthetic data only) + run_checks.sh for your real file
.github/workflows/ci.yml   CI: web page in sync + tests on every push
index.html                 redirect so GitHub Pages opens the browser version
CHANGELOG.md               version history
web/index.html             browser version
google-apps-script/        Google Sheets version
docs/HOW_TO_USE.md         daily routine
```

After editing `src/convert_wastage.py`, run `python tools/build_web.py` so the browser page stays in sync.

## Tests and CI

```bash
pip install -r requirements.txt pytest
pytest -q                                   # 9 tests, synthetic data, ~1 min (needs LibreOffice for 2 of them)
SOURCE=your_file.xlsx RUNS=10 tests/run_checks.sh   # repeat convert + verify on your real file
```

GitHub Actions runs the same on every push. Enable **Settings > Pages > Deploy from branch > main / root** to host the browser version at `https://<you>.github.io/<repo>/`; the browser version runs entirely on the visitor's machine, so the file is not uploaded.

## Privacy

`.gitignore` excludes `*.xlsx`, so your factory data is not committed by accident.

## Status

The Python and browser versions were tested on real data. The Google Apps Script version was syntax-checked only, not run in a live Google account.

## License

MIT
