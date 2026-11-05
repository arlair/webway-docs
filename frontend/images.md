# Images

Every image is one of two kinds. Decide which before adding it.

| Kind              | What                                                             | Where it lives                                                                 | How code uses it                                                       |
| ----------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| **Bundled image** | The project's own interface: logo, favicon, icons, UI decoration | Committed in the project, in its bundled-images folder                         | Imported, so the build fails if it's missing                           |
| **Content image** | Anything a reader sees as content: post heroes, products, scenes, portraits, characters | webway-admin's image library for the site, then R2 once published | Referenced by its library path, turned into a URL by the site's images base URL |

## Content images

1. Add the file to the site's library: `webway-admin image add <absolute file> --site <siteId> --dir <folder> --name <name>`. It's converted to WebP and stored locally in webway-admin's `images-storage/<siteId>/`.
2. Reference it by its library path, such as `/scenes/broken-sluice.webp`. Never commit the file or copy it into `static/` or `public/`.
3. Locally the image is served by webway-admin at `http://localhost:6100/api/images/serve/<siteId>/<path>`.
4. Publish it to R2 when it's ready for staging or production: `webway-admin image publish --site <siteId> <path…>`. It's then served from `https://images.<domain>/<path>`.

The sites and their buckets and hosts are listed in `sites/webway-admin/src/lib/config/site-configs.ts`.

## Bundled images

- Keep them small: WebP or SVG, sized for display (about 2× the CSS size).
- Projects ignore every image type in `.gitignore` except their bundled folder, so a misplaced image can't be committed by accident.

Projects record their bundled folder and URL helper in their own `AGENTS.md`. For example, selective-tales-app uses `src/domain/images/bundled/` and `contentImageUrl`.
