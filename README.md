# super-agent-test

A test repository for [dsh-superagent](https://github.com/deepseek-ai/dsh-superagent-prototype),
a proactive coding agent that works on this repository when someone allowed asks it to.

## How to ask for work

Open a new issue, or comment on an existing issue or pull request, and mention the
agent's GitHub account `@niechao-superagent` along with what you want done:

```
@niechao-superagent <what you want done>
```

Say which files should change and what should be different, for example `README.md`
or `docs/CONVENTIONS.md`. Begin a request by mentioning the agent. Once it is working
in a conversation, reply there to continue; another mention is not needed.

The agent acknowledges the request in that conversation, works in its own checkout,
and usually answers with a pull request from a branch named `agent/<ticket-name>`.
A person reviews and merges it; the agent does not merge its own work. The Testing
section below walks through what a healthy run looks like, under
[Checking the agent end to end](#checking-the-agent-end-to-end).

## Testing

There is no build step, no dependencies and no test runner here. `npm test` fails with
`ENOENT ... package.json` and `make test` fails with `No rule to make target 'test'`;
both are expected, because neither file exists.

What does exist is a short list of checks that run from the repository root with standard
tools: `git` and `grep`, plus `curl` for links and `gh` for the last step. Run them
before opening a pull request. Steps 1 and 2 print `ok` or `FAIL`; step 3 speaks up only
when a file is wrong; step 4 prints one line per link; step 5 shows GitHub's own checks.

### 1. No unresolved merge conflict markers

```bash
grep -rnE '^(<<<<<<<|>>>>>>>)' --include='*.md' . \
  && echo 'FAIL: resolve the conflict markers above' \
  || echo 'ok: no conflict markers'
```

### 2. No whitespace errors in the diff

```bash
git fetch -q origin master
git diff --check origin/master \
  && echo 'ok: no whitespace errors' \
  || echo 'FAIL: fix the whitespace errors above'
```

It reports trailing whitespace and leftover conflict markers with file and line number,
for example `README.md:53: trailing whitespace.`.

### 3. Every Markdown file ends with a newline

```bash
git ls-files '*.md' | while read -r f; do
  [ -z "$(tail -c 1 "$f")" ] || echo "FAIL: $f has no final newline"
done
```

Prints nothing when every file ends with a newline.

### 4. Every external link still answers

```bash
for url in $(grep -rhoE '\(https?://[^)]+\)' --include='*.md' . | tr -d '()' | sort -u); do
  curl -sS -fL -o /dev/null -w '%{http_code} %{url_effective}\n' --max-time 20 "$url" \
    || echo "FAIL: $url"
done
```

Prints one line per link and `FAIL` for any link that does not answer `200`. A `404`
means the link needs updating; `000` usually means no network access where you ran it.

### 5. The checks GitHub runs for you

```bash
gh pr checks <number> --repo niechao136/super-agent-test
```

GitGuardian is currently the only check configured. A pass there means no secret was
committed, not that the change is correct, so steps 1 to 4 still matter.

### Checking the change itself

The commands above cover the mechanical part. Reviewing the content is still manual:

1. Read the pull request description and confirm it says what changed, why, and what
   was checked.
2. Read the diff and confirm it touches only the files the description claims.
3. Check the rendered Markdown on the pull request's "Files changed" tab, links
   included.
4. Keep `README.md` and `docs/CONVENTIONS.md` consistent with each other: branches made
   by the agent start with `agent/`, commits use the `niechao-superagent` author, and the
   agent opens pull requests but does not merge them.

### Checking the agent end to end

1. Comment `@niechao-superagent <request>` on an issue.
2. Expect an acknowledgement in that same conversation, naming what the agent is about
   to change.
3. Expect a pull request from a branch named after the ticket, such as
   `agent/<ticket-name>`, authored by `niechao-superagent`.
4. Expect the pull request description to begin with the line "This is by an automated
   agent, not a human." and to list the checks that were actually run.
5. Merge the pull request yourself; the agent leaves the merge to a person in this
   repository.

If steps 2 to 4 do not happen, the agent run has failed; it is not a gap in the
documentation. Every check in this section is done from the issue and pull request
pages, with nothing to set up locally.
