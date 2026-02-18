# Fork Workflow

Personal fork of [anomalyco/opencode](https://github.com/anomalyco/opencode).

## Remotes

| Remote     | Repo                    | Purpose         |
| ---------- | ----------------------- | --------------- |
| `origin`   | `kyleacmooney/opencode` | Your fork       |
| `upstream` | `anomalyco/opencode`    | Source of truth |

## Branches

| Branch     | Rule                                   | Purpose                      |
| ---------- | -------------------------------------- | ---------------------------- |
| `dev`      | **Never commit directly**              | Clean mirror of upstream     |
| `personal` | Force-push OK (`--force-with-lease`)   | All personal/custom changes  |
| `feat/*`   | Short-lived, branch off `upstream/dev` | Upstream contributions (PRs) |

## Daily Workflows

### Personal changes

```bash
git checkout personal
# work, commit as normal
git push origin personal
```

### Sync with upstream

Do this regularly to stay current.

```bash
git fetch upstream
git checkout dev && git reset --hard upstream/dev && git push origin dev
git checkout personal && git rebase dev && git push --force-with-lease origin personal
```

### Contribute upstream

Create a clean branch from upstream's `dev` -- never from `personal`.

```bash
git fetch upstream
git checkout -b feat/my-thing upstream/dev
# make changes, commit
git push -u origin feat/my-thing
gh pr create --repo anomalyco/opencode --base dev
```

After the PR merges, clean up:

```bash
git checkout personal
git branch -D feat/my-thing
git push origin --delete feat/my-thing
```

### Promote a personal change to an upstream PR

When something on `personal` turns out to be worth contributing:

```bash
git fetch upstream
git checkout -b feat/cool-thing upstream/dev
git cherry-pick <commit-hash>   # pick only the relevant commit(s)
git push -u origin feat/cool-thing
gh pr create --repo anomalyco/opencode --base dev
```

After the PR merges and you sync (see above), `git rebase dev` on `personal` will usually auto-deduplicate the cherry-picked commits.

## Tips

- **Rebase getting tedious?** If `personal` accumulates many commits, break it into topical branches (`personal/ui-tweaks`, `personal/custom-config`, etc.) to keep each rebase scope smaller.
- **Conflict during rebase?** Resolve file-by-file, then `git rebase --continue`. If it's a mess, `git rebase --abort` to start over.
- **Check what you'd be rebasing:** `git log --oneline dev..personal` shows your personal commits that sit on top of upstream.
