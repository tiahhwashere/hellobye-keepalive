# hellobye-keepalive

Optional **external safety-net** that keeps the Render free-tier web service at
**https://hellobye.onrender.com** warm, so visitors never see Render's
"service waking up" cold-start page.

> The app itself already keeps itself awake with an in-app self-ping
> (`/healthz` every 10 min, see `server.js` in the `hellobye-chat` repo).
> This repo is an **extra** layer that can also wake the service from a fully
> cold state.

## Why

Render's **Free** compute plan spins a web service down after **15 minutes
without inbound traffic**. The next visitor then sees Render's "service waking
up" loading page while the instance boots (~1 minute).

## How to enable

GitHub blocks tokens with only the `repo` scope from creating files inside
`.github/workflows/`, so the workflow is stored here as
**`keepalive.workflow.yml`**. To activate it, move it into place:

```bash
mkdir -p .github/workflows
git mv keepalive.workflow.yml .github/workflows/keepalive.yml
git commit -m "Enable keepalive workflow"
git push
```

(Or create the file directly in the GitHub web UI at
`.github/workflows/keepalive.yml` and paste the contents of
`keepalive.workflow.yml`.)

Once enabled, the workflow pings `https://hellobye.onrender.com/healthz` every
8 minutes. This repo is **public** on purpose: public repositories get
unlimited GitHub Actions minutes, so the keep-alive costs nothing.

## Notes

- GitHub disables scheduled workflows after 60 days of repository inactivity,
  so the workflow makes a small weekly heartbeat commit to stay enabled.
- Keeping a free service awake around the clock consumes almost the entire
  workspace allowance of **750 free instance hours per month**. If you run
  other free services, they may be suspended once the pool is exhausted.
  Upgrading the Render service to a paid plan removes the spin-down limitation
  entirely.
