# StatMuse Fantasy Football Manager

An agent skill that helps run your fantasy football team with sports stats and information from StatMuse Intelligence. It researches every start, sit, add and drop, tells you which moves to make (or makes them, if you turn that on), and keeps a weekly report of each call and the stat behind it. You approve anything that reaches other people, like trades and league chat posts.

Works with your fantasy platform of choice, in any agent that supports MCP. It's a starting point: tweak it for your league, platform and agent as you go.

## What you need

- **A StatMuse+ membership with the StatMuse MCP connected.** The StatMuse MCP and API are in beta for StatMuse+ members: [developer.statmuse.com](https://developer.statmuse.com). New tools ship often, so start a fresh session each week to get the latest.
- **An agent that supports MCP,** like Claude, ChatGPT or Codex. Agents with a skills feature can install it, and any other agent can read `SKILL.md` as instructions.
- **Optional:** a browser tool, if you want the agent to make moves on your fantasy site itself. Some platforms' terms limit automated tools, so check yours first. Without it, you share your league info (a screenshot works) and the agent hands you a short list of moves to make.
- **Optional:** scheduled tasks for the daily and game-day checks.

## Install

**Agents with skills:** download `statmuse-fantasy-football-manager.zip` from the [latest release](../../releases/latest) and add it the way your agent installs skills (in the Claude apps, Customize > Skills), or clone the repo into your agent's skills folder (for Claude Code, `~/.claude/skills/`):

```bash
git clone https://github.com/statmuse/fantasy-football-manager statmuse-fantasy-football-manager
```

**Any other agent:** give it `SKILL.md` and the `references/` folder as instructions.

Then say "set up my fantasy leagues." The agent asks which platform each league is on, gets your team and roster, and asks whether you want it to make moves or just tell you what to do.

## What it does

- **Weekly run:** byes, injuries, waivers and FAAB, kicker and defense streams, start/sit ties, trade research.
- **Daily checks** (three a day): news, injuries and role changes, then lineup fixes and free agents that matter.
- **Game-day lock check:** right after inactives, so no starter who's out is left in your lineup, and a bench player whose role just grew gets the start.
- **Game-day recap:** grades every call and, if you want, drafts one league chat post for you to approve.
- **Weekly report:** every move and the stat behind it, including every call that went against your platform's projection and what it cost or gained.

## Ground rules

- Your platform's projection is the default. Every call against it is logged with its projected cost and the stat behind it.
- Trades and league chat posts always wait for your yes.
- The agent never asks for, copies or stores your passwords, tokens or cookies.
- Sports stats and information come from the StatMuse MCP. Moves stay off unless you turn them on, and then the agent makes only the ones you pre-approve.

## Files

| File | What it's for |
|---|---|
| `SKILL.md` | The skill itself: modes, setup, research steps, trade rules, the projection rule, game-day checks and the weekly report. |
| `config.example.md` | A sample config. Setup writes your real one to `config.md` (ignored by git). |
| `references/platform-guide.md` | How the agent gets your league info, and makes moves if you turn them on, on any platform. |

## Platforms

By default you share your league info (a screenshot works) and make the moves in your fantasy app. With moves on, the agent works through your fantasy site in the browser, following `references/platform-guide.md`, and saves notes on your platform as it learns it. Hit a snag? Send feedback (below) with what worked and what didn't, and we'll fold it into the guide.

## Feedback

The StatMuse MCP has a built-in feedback tool. Tell your agent "send feedback to StatMuse" and it goes straight to the team. You can also use the questions contact on your account page at developer.statmuse.com.

We don't take pull requests, so send fixes and ideas as feedback.

## License

Apache 2.0. See `LICENSE` and `NOTICE`. The license doesn't cover the StatMuse name or logo.
