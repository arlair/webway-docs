# Backend and database guidance

Read only the database guidance relevant to the target code.

| Task                                   | Read                                 |
| -------------------------------------- | ------------------------------------ |
| PostgreSQL query or `withSql` boundary | [`postgres.md`](./postgres.md)       |
| Cloudflare D1                          | [`d1.md`](./d1.md)                   |
| Local or embedded SQLite               | [`sqlite.md`](./sqlite.md)           |
| Schema, lifecycle or data authority    | Target project's local docs and ADRs |

Do not apply a PostgreSQL wrapper to a project whose adapter or repository is
the intended persistence boundary.
