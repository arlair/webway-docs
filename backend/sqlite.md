# SQLite

Use the target project's adapter or repository boundary for SQLite access.

- Confirm whether the SQLite file is local compiler input, test data or runtime
  authority before changing writes or migrations.
- Do not assume that local SQLite changes publish to D1 or another remote
  database.
- Keep schema and lifecycle decisions in the target project's local docs and
  ADRs.
