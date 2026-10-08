# git-toolkit

Three agent skills that take a local project to a public GitHub repo —
**login → create → push** — learned the hard way so you don't have to.

## The trio

1. **git-login** — signs into GitHub in the agent's browser: checks session state,
   collects credentials through a secure vault (never in chat), handles 2FA/OTP,
   and verifies the logged-in user.
2. **git-create** — creates the repository *in the browser* and optionally registers
   a deploy key.
3. **git-upload** — scaffolds (README / LICENSE / .gitignore), commits, and pushes
   over git SSH through a repo-scoped deploy key.

Typical flow: `git-login` → `git-create` → `git-upload`.

## Why not just the API?

Measured 2026-10-09 against the Muse GitHub App:

- `create_repository` → **403** Resource not accessible by integration
- `create_or_update_file` → **403** (reads work fine)

So the honest stack is: **browser for login + creation, SSH deploy key for push**.
Each skill documents its fallback chain instead of pretending the API works.

## Install

```bash
npx skills add <this-repo>
```

or copy `skills/*/` into your skills directory (`~/.agents/skills/`,
`.claude/skills/`, or your Antigravity/Gemini plugin skills dir):

```bash
./install.sh              # → ~/.agents/skills/
./install.sh /target/dir  # → custom directory
```

## License

MIT — see [LICENSE](LICENSE).
