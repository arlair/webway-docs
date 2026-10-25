# Dev Servers

Every site and app with a `dev-cockpit.json` runs under the Dev Cockpit
supervisor, which owns its process, port and log. Drive them with
`dev-cockpitctl` (on `PATH`), so the user's cockpit stays in step; a second
`pnpm dev` started by hand collides on the service's fixed port.

| Task | Command |
| --- | --- |
| What is running, and any problem | `dev-cockpitctl status` |
| Start and wait until it answers | `dev-cockpitctl up ID` |
| Apply a config or dependency change | `dev-cockpitctl restart ID` |
| Errors since the last start | `dev-cockpitctl logs ID --errors --since-start` |
| Run a package script, get its exit code | `dev-cockpitctl run ID SCRIPT` |

`ID` is the service id from `status` (usually the folder name). `wait`, `up`
and `restart` exit non-zero with the failure and the last output lines, so a
failed start is the error to act on. `dev-cockpitctl help` lists the rest.

A site's address is `http://ID.localhost:8000` as well as its own
`localhost` port; a request there starts a stopped service.
