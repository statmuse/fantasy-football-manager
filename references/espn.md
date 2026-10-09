# ESPN Fantasy

Status: untested. Written from ESPN's site and its unofficial read API. Verify each step on the first run, and update this file with what you find.

ESPN has no official public API. Public leagues can be read without logging in. Private leagues need the owner's logged-in session, so do reads from a browser tab on fantasy.espn.com, where the owner's session comes along automatically. Never ask for, copy or store the owner's cookies or password.

## Find the owner's leagues

1. Ask for the league URL. The league ID is the `leagueId` in `https://fantasy.espn.com/football/league?leagueId=...`. The owner's team ID is the `teamId` on their team page.
2. Confirm the team name on the league page.

## Reads

From a fantasy.espn.com tab:

`https://lm-api-reads.fantasy.espn.com/apis/v3/games/ffl/seasons/{season}/segments/0/leagues/{leagueId}?view=mSettings&view=mTeam&view=mRoster&view=mMatchupScore`

- `mSettings`: scoring, lineup slots, waivers, trade deadline.
- `mTeam` / `mRoster`: teams, records, rosters and injury tags.
- `mMatchupScore`: this week's matchups and scores (`scoringPeriodId` is the week).
- Free agents and projections: the `kona_player_info` view with an `x-fantasy-filter` header filtering on free agents. If that's awkward, read the Players page in the browser instead.

If the API shape doesn't match, fall back to reading the pages: League, My Team, Players (filter Available), Scoreboard.

## Moves (browser, logged in)

- **Lineup:** My Team page. Move players between starter and bench slots, then save if the page asks.
- **Adds, drops, waivers:** Players page, filter Available, click the add button, pick the drop. Waiver players show a claim option instead.
- **Trades:** a team's page, then Propose Trade.

Verify every move by re-reading the page or the API.
