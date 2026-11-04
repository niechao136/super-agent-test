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

There is no build step and no automated test suite, so there is nothing to run
locally. Changes are reviewed from the diff of the pull request that proposes them.

To test the agent itself, comment on an issue with `@niechao-superagent <request>`
and check that it replies in the same conversation and opens a pull request from
a branch named after its ticket.
