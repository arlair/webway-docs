# PostgreSQL

- Route feature queries through `withSql`; do not create a `postgres()` client
  in a route or domain feature.
- The shared implementation is `@eldarlabs/core/db/sql.server`. Use the project-local
  wrapper when one exists, such as Travelwebway’s `src/domain/db/sql.ts`; it
  owns configuration and initialization.
- Define a DTO or other explicit result type for query results.
- Pass an existing SQL connection when the operation must participate in a
  caller-owned transaction.

The direct shared import below assumes server startup has called `initSql`.
Otherwise use the project wrapper or the existing explicit configuration path.

<!-- prettier-ignore -->
```typescript
import {withSql} from '@eldarlabs/core/db/sql.server'

interface ItemDTO {
  id: string
  name: string
}

const items = await withSql(async sql =>
  sql<ItemDTO[]>`SELECT id, name FROM items WHERE status = ${'active'}`
)
```
