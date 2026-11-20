---
name: sync-repo
description: >-
  Sync the current repository with trunk. Commit only the files that this
  session changed, push them, raise a PR to trunk, approve it, complete it with
  a merge commit, and pull trunk back down. Works in the main repository and in
  a worktree. Use only when Mr. Das asks for it.
---

# Sync the repository

Use this skill to put the work of this session on trunk in one step.

## Preconditions

- Mr. Das asked for the sync. The skill commits to trunk through a PR. This is
  permitted only by the exemption in guardrail 3. Do not run it for a different
  human or for unattended agent work.
- `az login` is done. The `azure-devops` extension of `az` is installed.
- The branch policy of trunk lets the PR author approve their own PR.

## Rules

- Commit only the files that this session changed. Other sessions can work in
  the same repository at the same time. Do not stage, stash, reset, or discard
  a file that this session did not change.
- Do not force push. Do not bypass hooks. Do not bypass branch policies.
- Do not switch branches. Do not create worktrees.
- Do not update the main repository when you run from a worktree.
- If a step fails, stop. Report the step, the command, and the error.
- Run each git command to `origin` with `-c credential.helper= -c "http.extraheader=AUTHORIZATION: bearer <token>"`, with the token from `az account get-access-token --resource 499b84ac-1321-427f-aa17-267ca6975798`.

## Step 1: Find the context

Run these commands in the current working directory:

```powershell
git rev-parse --show-toplevel
git branch --show-current
git symbolic-ref --short refs/remotes/origin/HEAD
git remote get-url origin
```

- Trunk is the output of the third command without `origin/`. If the command
  fails, use the fallback in `docs/design.md`.
- If the current branch is empty (detached HEAD), stop.
- Get the organization, project, and repository from the remote URL. The URL
  has the form `https://<user>@dev.azure.com/<org>/<project>/_git/<repo>`. Use
  `--org https://dev.azure.com/<org>` in all `az repos` commands.
- Set the source branch:
  - On trunk: `sync/<yyyyMMdd-HHmmss>`, with the local time.
  - On a different branch (for example `wi/<id>` in a worktree): the current
    branch.

## Step 2: Find the files of this session

1. List each file that this session created, edited, renamed, or deleted. Use
   your tool history. Include files that shell commands of this session
   changed (for example a move or a generated file).
2. If the session history was compacted, also query the local session store
   for the current session:

   ```sql
   SELECT DISTINCT file_path FROM session_files WHERE session_id = '<current session id>'
   ```

3. Run `git status --porcelain`. Keep only the session files that have a
   change. Record the other changed files as "not touched".
4. If a session file also has changes from a different session, you cannot
   split them. Commit the file and tell the human in the report.

If no session file has a change, go to step 4.

## Step 3: Commit

1. Stage only the session files. This also stages deletions:

   ```powershell
   git add -A -- <path1> <path2> ...
   ```

   Do not use `git add .`, `git add -A` without paths, or `git commit -a`.
2. Make sure that `git diff --cached --name-only` shows only the session files.
3. Commit. Write the message in Plain Language. Use a subject of 72 characters
   or fewer and a short body. Add the commit trailers of the current session.

## Step 4: Push

If nothing is ahead of trunk, there is nothing to merge. Go to step 7. To
find out, run `git rev-list --count origin/<trunk>..HEAD`. A result of 0 means
that nothing is ahead.

```powershell
git push origin HEAD:refs/heads/<source branch>
```

If the push is rejected because the remote branch moved, stop.

## Step 5: Raise the PR

Find an active PR from the source branch to trunk:

```powershell
az repos pr list --org <org url> --project <project> --repository <repo> `
  --source-branch <source branch> --target-branch <trunk> --status active `
  --query "[0].pullRequestId" -o tsv
```

If there is no PR, create one. Write the title and description in Plain
Language:

```powershell
az repos pr create --org <org url> --project <project> --repository <repo> `
  --source-branch <source branch> --target-branch <trunk> `
  --title "<title>" --description "<description>" --query pullRequestId -o tsv
```

## Step 6: Approve and complete the PR

```powershell
az repos pr set-vote --org <org url> --id <pr id> --vote approve
az repos pr update --org <org url> --id <pr id> --status completed --squash false `
  --delete-source-branch <true for sync/ branches, false for other branches> `
  --merge-commit-message "<PR title>"
```

- `--squash false` makes a merge commit (no fast-forward). A `wi/<id>` branch
  then stays in step with trunk. Do not delete a `wi/<id>` branch.
- Read the status until it is `completed`. Wait up to 5 minutes:

  ```powershell
  az repos pr show --org <org url> --id <pr id> --query "{status:status, merge:mergeStatus}"
  ```

- If a policy or a merge conflict blocks the completion, stop. Do not use
  `--bypass-policy`.

## Step 7: Pull trunk down

```powershell
git fetch origin --prune
git merge --ff-only origin/<trunk>
```

- The merge commit of the PR has the local commit as a parent. Thus a
  fast-forward is the expected result.
- If the fast-forward fails, stop. Do not stash, reset, or make a merge commit.
  A typical cause: trunk changes a file that has uncommitted changes of a
  different session.
- On a branch other than trunk, push the branch again so that the remote
  branch is equal to the local branch:

  ```powershell
  git push origin HEAD:refs/heads/<source branch>
  ```

## Step 8: Report

Tell the human:

- The PR, as a link with short text, for example `[PR #123](url)`.
- The merge commit on trunk.
- The files that you committed.
- The changed files that you did not touch.
- All warnings (for example, a file with changes from a different session).
