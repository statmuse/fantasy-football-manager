# StatMuse Fantasy Football Manager

An agent skill that helps run your fantasy football team with sports stats and information from StatMuse AI. It researches every start, sit, add and drop, makes the moves on your roster, and keeps a weekly report of each call and the stat behind it. You approve anything that reaches other people, like trades and league chat posts.

Works with your fantasy platform of choice: ESPN, Yahoo, Sleeper and others.

## What you need

- **A StatMuse+ membership with the StatMuse MCP connected.** The StatMuse MCP and API are in beta for StatMuse+ members: [developer.statmuse.com](https://developer.statmuse.com). New tools ship often, so start a fresh session each week to get the latest.
- **Claude, or your agent of choice.** Built and tested with Claude (the Claude apps and Claude Code). Custom skills in the Claude apps need code execution turned on (Settings > Capabilities). The skill is plain Markdown, so you can adapt it to any agent that supports MCP.
- **Optional:** Claude in Chrome, logged in to your fantasy site, so the agent can make moves itself. Without it, the agent does the research and hands you a short list of moves to make.
- **Optional:** scheduled tasks for the daily and game-day checks.

## Install

**Claude apps:** download `statmuse-fantasy-football-manager.zip` from the [latest release](../../releases/latest), then upload it under Customize > Skills (+, then Create skill, then Upload a skill).

**Claude Code:**

```bash
git clone https://github.com/statmuse/fantasy-football-manager ~/.claude/skills/statmuse-fantasy-football-manager
```

Then say "set up my fantasy leagues." The agent asks which platform each league is on, finds your team, and asks what it's allowed to do without checking with you first.

## What it does

- **Weekly run:** byes, injuries, waivers and FAAB, kicker and defense streams, start/sit ties, trade research.
- **Daily checks** (three a day): news, injuries and role changes, then lineup fixes and free agents that matter.
- **Game-day lock check:** right after inactives, so no starter who's out is left in your lineup.
- **Game-day recap:** grades every call and, if you want, drafts one league chat post for you to approve.
- **Weekly report:** every move and the stat behind it, including every call that went against your platform's projection and what it cost or gained.

## Ground rules

- Your platform's projection is the default. Every call against it is logged with its projected cost and the stat behind it.
- Trades and league chat posts always wait for your yes.
- The agent never asks for, copies or stores your passwords, tokens or cookies.
- Sports stats and information come from the StatMuse MCP. The browser is only used on your fantasy site.

## Files

| File | What it's for |
|---|---|
| `SKILL.md` | The skill itself: modes, setup, research steps, trade rules, the projection rule, game-day checks and the weekly report. |
| `config.example.md` | A sample config. Setup writes your real one to `config.md` (ignored by git). |
| `references/` | One guide per fantasy platform: how to find your league, read it and make moves. |

## Platform support

| Platform | Status |
|---|---|
| Sleeper | Tested |
| ESPN | Guide included, untested |
| Yahoo | Guide included, untested |
| Others | Browser-based, guided by `references/other-platforms.md` |

Tried it on another platform? Send feedback (below) with what worked and what didn't, and we'll fold it into the guides.

## Feedback

The StatMuse MCP has a built-in feedback tool in alpha. If it's in your StatMuse tools, tell your agent "send feedback to StatMuse" and it goes straight to the team. Otherwise, use the questions contact on your account page at developer.statmuse.com.

We don't take pull requests, so send fixes and ideas as feedback.

## License

Apache 2.0. See `LICENSE` and `NOTICE`. The license doesn't cover the StatMuse name or logo.
