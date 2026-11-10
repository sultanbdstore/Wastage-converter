# Changelog

## 1.0.0
- Converter (`src/convert_wastage.py`): regular Wastage Control Status Excel -> management dashboard workbook.
- Independent verifier (`src/verify_conversion.py`), 20 checks.
- Browser version (`web/index.html`, runs the same converter through Pyodide).
- Google Sheets version (`google-apps-script/`, syntax-checked only).
- Sample wastage is part of Sewing floor and shown in kg only (never a % of cut qty).
- Header guard, full row accounting, month-typo date repair, typed-over cell detection, Conversion Report.
- Test suite with synthetic data (`tests/`) and GitHub Actions CI.
