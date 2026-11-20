# Git hosting and agent identity

## Organization repositories: GitHub is the source

Since 2026-11-10, GitHub `subculture-collective` is the source for every repository in that organization, including copies whose Gitea home is under `PatrickFanella`.

- Push branches and open pull requests on `github.com/subculture-collective/<name>` with `gh`. Pull requests on the Gitea copies are turned off.
- A ready pull request merges itself once its required checks pass and it has no conflicts; a Kvant timer merges as the repository owner, so main-branch deploys run. To hold one back, open it as a draft or add the label `hold`.
- Issues live on GitHub too; Gitea issues are turned off. Add each new issue to the organization Project named after its repository. Public products also have Discussions.
- Do not push to these repositories on Gitea, and do not edit their `PatrickFanella/<name>` copies on GitHub. Kvant copies GitHub to Gitea every minute, fast-forward only, and nothing flows back.
- Deploys are moving to GitHub Actions, which reach the homelab over the tailnet; jobs not yet moved still run on Gitea Actions after the copy.
- Local checkouts use GitHub as `origin` and keep Gitea as the fetch-only remote `gitea`, which also selects the agent Git identity. A fresh GitHub clone has no `gitea` remote, so commit with `-c user.name='SUBCULT Agent' -c user.email='info@subcult.tv'`.

## Gitea-only repositories

Repositories that exist only on `https://git.subcult.tv` keep the Gitea workflow: push branches to Gitea and open pull requests and issues with `tea`, as `subcult-agent`.

- The default `tea` login and the Gitea HTTPS credential helper select `subcult-agent`. When a command accepts a login, pass `--login subcult-agent`; logins such as `open-pilot`, `subcult` and `patrickfanella` may authenticate as the human operator.
- Before a Gitea write, check that `tea api --login subcult-agent /user` returns `login: subcult-agent`.
- Push through the installed credential helper. Fleet Git URL rules route Gitea SSH remotes through HTTPS. On a permission failure, report the missing access; never fall back to a human SSH key or token.
- `subcult-agent` is a site administrator and its device tokens have Gitea's `all` scope. Creating a token still needs account authentication; a token alone gets `401 auth required` there. Report a missing scope instead of using a human token.

## Commit identity

Before committing agent work, check `git var GIT_AUTHOR_IDENT` and `git var GIT_COMMITTER_IDENT`. Repositories with a Gitea remote inherit `SUBCULT Agent <info@subcult.tv>` from a conditional Git include. If a worktree or environment overrides it, commit with `-c user.name='SUBCULT Agent' -c user.email='info@subcult.tv' -c commit.gpgsign=false`. Never sign agent commits with the human operator's key.

## Credentials

Device tokens live in private `tea` configuration. Never print them, put them in remote URLs or copy them into project files, and do not revoke human or service credentials during routine work. Receipts live in `~/.local/state/gitea-agent/`. Switchyard keeps its `open-pilot` runtime identity by the user's instruction; a fleet-wide identity change must leave it alone.
