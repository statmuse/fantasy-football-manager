# Yahoo Fantasy

Status: untested. Verify each step on the first run, and update this file with what you find.

Yahoo has an official Fantasy Sports API, but it needs an OAuth app the owner would have to set up. Unless they already have one, do everything through the website in the browser while the owner is logged in. Never ask for or store the owner's password or tokens.

## Find the owner's leagues

Ask for the league URL. It looks like `https://football.fantasysports.yahoo.com/f1/{leagueId}`. The owner's team is `/f1/{leagueId}/{teamId}`.

## Reads (browser)

Read the page text (not screenshots) from:

- League home: standings, records, recent transactions.
- Matchup page: this week's opponent, projections and live scores.
- Team page: roster, lineup slots, injury tags and projections.
- Players page, filtered to free agents and waivers by position: projections and ownership.
- League settings: scoring, roster slots, waiver type, trade deadline.

## Moves (browser, logged in)

- **Lineup:** team page, Edit Lineup (or swap buttons), then save.
- **Adds, drops, claims:** Players page, the add button next to a player, pick the drop. Waiver players open a claim (with a FAAB amount in FAAB leagues).
- **Trades:** the other team's page, Propose Trade.

Verify every move by re-reading the team page.
