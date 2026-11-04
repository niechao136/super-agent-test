# super-agent-test

A test repository for [dsh-superagent](https://github.com/deepseek-ai/dsh-superagent-prototype),
a proactive coding agent that works on this repository when someone allowed asks it to.

## How to ask for work

Comment on an issue with:

```
@niechao-superagent <what you want>
```

The agent opens a Ticket, works in its own checkout, and answers in the same conversation,
usually with a pull request.

## Testing

There is no build step, no dependencies and no automated test suite here, so there is
no test command to run. Testing is manual and covers two things: a proposed change and
the agent that produced it.

### Checking a proposed change

1. Read the pull request description and confirm it says what changed, why, and what
   was checked.
2. Read the diff and confirm it touches only the files the description claims.
3. Check the rendered Markdown on the pull request's "Files changed" tab, links
   included.
4. Look at the checks on the pull request. GitGuardian is currently the only one
   configured; a pass there means no secret was committed, not that the change is
   correct.

Keep `README.md` and `docs/CONVENTIONS.md` consistent with each other: branches made by
the agent start with `agent/`, commits use the `niechao-superagent` author, and the agent
opens pull requests but does not merge them.

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
documentation. There is nothing to set up, install or run to reproduce a result: every
check above is done from the issue and pull request pages.
