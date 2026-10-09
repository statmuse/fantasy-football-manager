---
name: statmuse-fantasy-football-manager
description: Fantasy football manager powered by the StatMuse Agent (sports stats and information from StatMuse AI). Sets and optimizes lineups, checks injuries, byes and inactives, works the waiver wire and FAAB, streams kickers and defenses, researches and drafts trades, runs daily and game-day checks, drafts league chat trash talk, and keeps a weekly report of every move and the stat behind it. Works with your fantasy platform of choice (ESPN, Yahoo, Sleeper and others). Use for "manage my fantasy team," "set my lineup," "who should I start," "waiver wire," "FAAB bid," "stream a defense," "fantasy trade," "daily fantasy check," "game-day check," "weekly fantasy report," or "set up my leagues."
---

# StatMuse Fantasy Football Manager

Runs one or more fantasy football teams with sports stats and information from StatMuse AI. Claude does the research, makes the calls on the owner's roster, and writes down the stat behind every call. The owner only approves things that reach other people: trades and league chat posts.

It works with whatever fantasy platform the owner plays on. Platform guides live in `references/`. Read the one for the owner's platform before touching their league.

## Read first

**The StatMuse Agent is in beta.** The StatMuse MCP and API are available to StatMuse+ members, and new tools ship often. Start a fresh session for each weekly run so the newest tools load. At the start of every run, look at the StatMuse tool list for anything new (a projections tool, for example) and prefer it over any workaround in this file.

**Where everything comes from:**

- **Sports stats and information:** StatMuse AI, through the StatMuse MCP. Don't browse or scrape statmuse.com. The MCP is how agents get the StatMuse experience.
- **League state and moves:** the owner's fantasy platform, through its public API where one exists, or the website in the browser.
- **Browser automation** is only for the fantasy platform: reading league pages and making lineup, waiver and trade moves while the owner is logged in.

**What you need:**

| Capability | Required? | Without it |
|---|---|---|
| StatMuse MCP connected | Required | Stop and ask the owner to connect it |
| Browser tool logged in to the fantasy site (Claude in Chrome) | For making moves | **Advisor mode:** do all the research and hand the owner a short list of moves to make |
| Scheduled tasks | Optional | Run checks when the owner asks |
| Artifacts (claude.ai) | Optional | Write the weekly report as a Markdown file |

## Modes

- **First-run setup:** find the owner's leagues and write the config.
- **Weekly run** (manual, usually Tuesday to Thursday): full checklist, waivers, trade research, new week section in the report.
- **Daily check** (scheduled, 3 times a day): news, injuries and role changes, then fix lineups and grab free agents that matter.
- **Game-day lock check** (scheduled, right after inactives): no starter who's Out or inactive is left in a lineup when his game locks.
- **Game-day recap + trash talk** (scheduled, opt-in): score the day, grade our calls, draft one league chat post.

## Config

Keep one config per owner. Store it wherever this environment persists things: a `config.md` next to this file if the skill folder is writable (Claude Code), the skill itself re-saved with the config filled in (Claude apps that support saving skills), or project memory. Start from `config.example.md`.

Scoring, lineup slots, bench and IR counts, waiver type and trade deadline always come from the league's own settings. Never hardcode them. The config's `notes` line is a human summary only.

## First-run setup

1. Ask which platform each league is on (ESPN, Yahoo, Sleeper, other) and load the matching file in `references/`. For any other platform, use the browser-only approach in `references/other-platforms.md`.
2. Follow that guide to find the owner's leagues, team and roster. Show the list and ask which leagues to manage (default: all).
3. Ask what's pre-approved. Default: lineup swaps, IR moves, free-agent adds and drops, and waiver or FAAB claims within a cap are do-now. Trades and league chat posts always need a one-word yes, every time.
4. Ask about trash talk: "Want me to draft a league chat post after each game day, roasting the other teams' bad days and bragging about our good calls? You approve each post with one word." If yes, pick a prefix (default `"<OwnerName>Agent: "`).
5. Set up the weekly report: one per league, as a published artifact if available, otherwise a Markdown file.
6. Write the config, and create the scheduled tasks at the end of this file if the environment supports them.

## Step 1: Connect the StatMuse Agent

1. Find the StatMuse tools. In Claude, MCP tools can be deferred and carry a server-ID prefix, so searching "statmuse" may find nothing. Run one tool search by name: `get_stats_guide get_stats search_team search_player get_games get_game_details get_player_injuries get_season_briefing get_standings get_team_roster get_player_detail get_news get_game_plays get_article`, and load every tool with that prefix, plus any new ones. Confirm in one line: "StatMuse Agent connected: <n> tools."
2. Call `get_stats_guide` (`league: "nfl"`) before the first `get_stats`, then `get_season_briefing` (nfl) to ground the week.
3. Resolve IDs in batches: `search_player` and `search_team` take arrays. Platform player IDs are not StatMuse IDs, so map by name and check the team matches. Watch suffixes ("Luther Burden" vs "Luther Burden III").
4. If no StatMuse tools load, stop and tell the owner the StatMuse connector isn't enabled in this session. In a scheduled run, report that and stop. Don't fall back to browsing statmuse.com.

### StatMuse query cookbook

- Always filter `seasonYear` (the calendar year the season starts) and `seasonType: RegularSeason`.
- **Fantasy points:** `Fantasy-HalfPointsPerReceptionPoints`, `Fantasy-FullPointsPerReceptionPoints` or `Fantasy-StandardPoints`, at player, game or opponent level. Match the league's scoring.
- **Defense vs position:** group `[{"type":"opponent"},{"type":"position"}]` with the fantasy points key, `Receiving-Yards`, `Rushing-Yards`, `Receiving-Targets`, filtered to the opponents you care about. One call covers what each defense gives up to QB, RB, WR, TE and K.
- **Usage and game logs:** group `[player, game]` with fantasy points, `Receiving-Targets`, `Rushing-Attempts`; filter many player IDs at once; sort chron ascending.
- **Backfield reality check:** group `player`, filter `team` and `position: RunningBack`, for carries and targets by back. Projections guess at roles; game logs show them. Example: a back projected for 7 points can have zero touches all season.
- **Defense streaming:** opponent offense via `[team, game]` with `Points`, `Turnovers`, `Passing-Sacks` (sacks allowed). Own defense via `team` with `Defensive-Sacks`, `OpponentTurnovers`, `OpponentPoints`. Team defense fantasy points: `Fantasy-StandardPoints` with a `team` grouping (`opponent` grouping for what each offense gives up to defenses). StatMuse's standard scoring can differ from a platform's defense scoring, so compare once against the platform's actual defense points before leaning on it.
- **Kicker streaming:** `opponent` plus `position: Kicker` for kicker points and field goal attempts allowed. A defense that gives up touchdowns (many extra points, few field goals) is worse for kickers than its points per game suggests.
- **Pregame lines:** `get_game_details` on scheduled games returns spread, opening line, moneyline, total, and venue roof and surface. Implied team total = total / 2 − spread / 2. Use it for stacks and for kicker and defense streams. Lines can drop off once a game is in progress.
- **Injuries:** `get_player_injuries` for the NFL needs `team_ids`. Pass every team in the union of managed rosters and opponents in one call (max 40). Read `game_status` and `practice_status`.
- **News:** `get_player_detail` (up to 10 IDs) returns recent news per player. It's the fastest way to catch "X will replace Y" and role changes.
- **Per-game opponent rates:** pull totals and divide by game count. Get the count from a `[team, game]` query, since some count columns can be dropped from a result.
- **Projections:** if a StatMuse projections tool is listed, use it as the second projection next to the platform's (see Projection rule), labeled "StatMuse." If the tool names an underlying provider, add it in parentheses: "StatMuse (provider name)."

## Step 2: Pull league state

Follow the platform guide. Every run needs, for each managed league:

- the current week, league settings (scoring, lineup slots, waivers, trade deadline), all rosters, this week's matchups, recent transactions and pending trades;
- the platform's projections in the league's scoring;
- free agents and players on waivers, with projections;
- the NFL schedule and byes (StatMuse `get_games` works on any platform; kickoff times are ET, so convert to the owner's timezone).

**Several leagues at once:** build one deduped player universe (every managed roster, each league's top free agents, each opponent's starters). Run the StatMuse injury, news and usage queries once on that universe, then decide league by league. The same player can be a start in one league and a sit in another.

## Step 3: Weekly checklist (per league)

1. Check byes, Out and Doubtful starters, and early kickoffs (international games, Thursday, Saturday).
2. Compare our projected total with the opponent's. As an underdog or in a coin flip, lean into correlated upside (stack the projected shootout) over a safe floor.
3. Hunt waivers for injury replacements, backups promoted by an injury, and rising rookies. Verify the role with StatMuse usage stats, not the projection alone.
4. Stream kicker and defense with StatMuse matchup stats and Vegas implied totals.
5. Break start/sit ties with defense-vs-position stats and target share.
6. Run trade research (Step 3b). Proposals stay drafts until the owner says yes.
7. Bench Doubtful players. If one is ruled Out, move him to IR if the league allows it and use the open spot.
8. Plan 2 to 3 weeks ahead for bye clusters.

## Step 3b: Trade research

Waivers fix this week. Trades win the league. Research every weekly run, but only propose when it clearly makes sense.

**Bar for proposing (all three):** it fixes a real hole for us (about +3 projected points a week in a starting slot, or covers a bye cluster or injury); it fixes a real hole for them; and it looks fair or slightly in their favor on the platform's season points, which is what the other manager sees.

**Limit:** at most 1 proposal per league per week, plus 1 fallback. "No trade this week" is a fine answer.

1. **Audit every roster:** season points, points per game, injury status and bye for each team's QB, RB, WR and TE. Mark each team's starters and holes, record and points scored. Teams that are losing or just lost a starter deal more.
2. **Find our needs and surplus** against the league's typical starter at each position. Factor in byes and IR returns.
3. **Match partners:** their hole lines up with our surplus, and their surplus with our need.
4. **Check value with StatMuse:** points per game in league scoring, usage trend over the last 2 to 3 games, defense-vs-position for the next 3 opponents. Aim for an offer that looks fair on the platform's numbers but wins for us on usage, schedule or roster fit.
5. **Structure it:** main offer plus fallback. Sell high after a touchdown-driven spike with flat usage, buy low after a quiet game with steady usage. Don't trade with this week's opponent until the matchup ends. Respect the trade deadline.
6. **Judge it:** likely, coin flip or long shot, with one line why. Don't propose long shots.
7. **Write it up** for the owner: Proposal (we send X, we get Y), Why for us (with the StatMuse stat), Why they'd say yes, Can it get done, Fallback. Then one question: "Send it?"
8. **After a yes, send it** in the platform and confirm it's pending. Make no other trades in that league until it resolves.
9. **Track it every run.** Accepted: reset the lineup and log it. Declined or expired: the fallback is a new proposal and needs a new yes. Countered or incoming: evaluate the same way and recommend take it or not.
   - **Fleece guard:** assume every incoming offer is trying to win the trade. Recommend declining anything that lowers our projected starting lineup for the rest of the season, trades a starter for bench depth, is built on a touchdown spike or a name returning from injury, or makes a top-3 rival clearly better. When in doubt, decline.
10. **Approval rule:** each send, accept, counter or decline needs its own yes in chat. Scheduled runs never send or accept trades. They queue the proposal for the next time the owner is in chat.

## Projection rule (always)

The platform's projection is the default. Any lineup or add/drop call that goes against it is logged in that week's **Calls against the projection** table:

- It counts as against when we start a lower-projected player over a higher-projected eligible one, or pick a lower-projected free agent for a starting slot.
- Each row: when, the call, the projected cost in points, and the StatMuse stat that justified it. Sum the week's cost under the table.
- Keep it rare. Override only when the StatMuse stats clearly point the other way: a usage reality check, a points-allowed tier, a role change the projection hasn't caught. Log near-ties too.
- When the projected margin moves, say why in one line (a projection refresh or our own move).
- If a StatMuse projections tool is available, show its number beside the platform's for every starter, labeled by source, flag slots where they disagree on who to start, and grade both after the week (Step 5b). The platform's projection stays the default.

## Step 4: Daily check (3 times a day, for example 7 AM, noon and 6 PM local)

1. **Statuses:** one `get_player_injuries` call for the whole player universe's teams. Flag status changes since the last log and any DNP practice.
2. **News:** `get_player_detail` on every rostered player, in batches of 10. Look for ruled out, role change, "X will replace Y", trade, suspension, benching or QB change. Scan `get_season_briefing` headlines too.
3. **Promoted backups:** for any NFL starter ruled Out, check whether his backup is available and worth a spot. Confirm with StatMuse usage stats.
4. **Act per league, within what's pre-approved:**
   - Starter Out or suspended: swap in the best eligible bench player whose game hasn't locked. Move him to IR if allowed.
   - Starter Doubtful: bench him if there's a reasonable replacement.
   - Clearly better free agent for a hole (bye, injury, kicker or defense stream): add and drop now. Never drop a player who's in a future week's plan without noting it.
   - First run after waivers clear: sweep the newly free players first.
   - Waiver or FAAB claims (if autopilot is on): about 1 to 5% of the budget for depth, up to 15% for a likely weekly starter, more for a true breakout or a handcuff after an injury. Never more than the config's cap. Log every bid.
   - Trades: check pending and incoming offers (Step 3b.9).
5. **Log it** in the current week's Daily log if anything changed, and republish. If nothing changed, don't.
6. **Summary:** one line per league, plus anything waiting on the owner. Nothing changed anywhere: "Daily check: no changes."

## Step 5: Game-day lock check (right after inactives, about 75 to 90 minutes before each kickoff window)

1. Get today's games and kickoff times from StatMuse `get_games`.
2. If no starter in any league kicks off in the next 3 hours, end with "No locks in window."
3. For each starter kicking off in the window, check the platform's injury tag, StatMuse `game_status` and the latest news. Out or inactive: swap in the best bench player whose game hasn't started, or add the best free agent whose game hasn't locked.
4. Leave active Questionable players in.
5. Verify every changed lineup, log the moves, and summarize in one line per league.

## Step 5b: Game-day recap + trash talk (after each game day)

1. **Score the day:** every team's starters vs projection, points left on benches, inactive starters. Use StatMuse box scores (`get_game_details`) for the colorful details. Only count players whose NFL game is final (`get_games` status `completed`). A zero from a player who hasn't played yet is not a bust.
2. **Grade our calls:** every call against the projection, waiver add, stream and trade, by how much it beat or missed. After the last game of the week, fill in the week's projection grade (each source's average miss on our starters, plus season to date).
   - **Lineup efficiency, every team:** after the last game of the week, compute each team's points scored divided by the best lineup it could have started from its own roster that week. Fill the single-position slots first (QB, K, DEF, TE, then RBs and WRs), then FLEX from the best remaining eligible player, using each player's eligible positions. Add the week to a "Lineup efficiency" table (each team's weekly %, season %, points left on the bench) and say where our team ranked against the human managers. Label any weeks before the agent took over.
3. **Draft one post** if trash talk is on, under about 400 characters, starting with the prefix:
   - Roast 1 to 3 of the other teams' worst calls of the day. Never our own team.
   - Brag about 1 to 2 of our calls that beat the humans, with the number.
   - Only players whose game is final. Re-check every name right before showing the draft.
   - Don't call a matchup won until it's final or mathematically clinched, and don't claim a league record until it can't be beaten.
   - Football only: lineup calls and stat lines. Nothing personal, nothing mean. Funny beats harsh. If our day was bad, go self-deprecating or skip it.
   - At most 1 post per game day. Don't reply to other people's messages.
4. **Get a one-word OK, then post.** In a scheduled run, put the draft in the summary and stop.

## Execution rules

- Make moves only on the fantasy platform, while the owner is logged in, and only moves the owner pre-approved. The platform guide has the click paths.
- Double-check the league in the URL before every move. Multi-league runs make it easy to act in the wrong league.
- After adding a player to fill a kicker or defense slot, put him in the starting slot. Most platforms put adds on the bench.
- Never type in a league chat box except to post an approved message.
- Verify the final lineup after every move, through the platform's API or by re-reading the lineup page.
- **Approval queue:** anything that reaches other people (trade sends, accepts, counters, declines, chat posts) goes into one bundled message with a recommendation per item. The owner's one reply ("yes to all," "yes to 1, skip 2") approves exactly what's listed.

## Feedback

- The StatMuse MCP's feedback tool is in alpha, so it isn't on every account yet. Check whether the StatMuse tools list it.
- If they do, report problems through it when you hit them, as its description says: a StatMuse tool that failed or was unclear, a missing capability, or a step in a platform guide here that no longer works (name `statmuse-fantasy-football-manager` as the tool for skill problems).
- When the owner says "send feedback to StatMuse," pass their words along through that tool as feedback from the user.
- Never include league members' names, team names or league IDs in feedback.
- No feedback tool listed: point the owner to the questions contact on their account page at developer.statmuse.com.

## Step 6: Weekly report

One report per league, updated in place, newest week on top. Never delete earlier weeks. Keep them below the newest one under an "Earlier weeks" divider. Each week gets:

- record, matchup, and projections slot by slot;
- a moves table: the move, why, and the StatMuse stat behind it;
- a StatMuse recheck table with a "vs projection" column;
- the Calls against the projection table with its total;
- the projection comparison and last week's projection grade (when a second projection exists);
- the lineup efficiency table (every team, every week, with our rank against the human managers);
- matchup intel cards, the final lineup, a daily log, open items;
- a trade desk (Proposed, Sent, Accepted, Declined);
- last week's result with a review of the calls.

Label every source: sports stats and information from StatMuse AI, league info from the platform. If a report is shared publicly, anonymize the other managers unless they agree to be named. Plain sentences, dates as `YYYY.MM.DD`.

## Scheduled tasks (if the environment supports them)

Use the owner's timezone. Scheduled runs only fire while the app is running.

- **Daily check:** `0 7,12,18 * * *`. "Run the statmuse-fantasy-football-manager skill in Daily check mode for every league in the config."
- **Game-day lock check:** a few minutes after inactives come out (90 minutes before kickoff) for each window. Pacific examples: `5 5 * * 0` (international morning games), `35 8 * * 0` (early games), `0 12 * * 0` (late afternoon), `55 15 * * 0,1,4` (night games). "Run the statmuse-fantasy-football-manager skill in Game-day lock check mode."
- **Game-day recap:** `0 21 * * 0,1,4` Pacific. "Run the statmuse-fantasy-football-manager skill in Game-day recap mode. Draft the post, don't send it."
