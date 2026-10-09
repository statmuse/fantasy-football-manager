# Sleeper

Status: tested in live weekly runs, daily checks and game-day checks during the 2026 season.

Sleeper has a public read API with no login, so reads are plain web requests. Moves (lineups, waivers, trades, chat) happen on sleeper.com in the browser, logged in as the owner.

## Find the owner's leagues

1. Ask for their Sleeper username.
2. `GET https://api.sleeper.app/v1/user/{username}` returns `user_id`.
3. `GET https://api.sleeper.app/v1/user/{user_id}/leagues/nfl/{season}` lists their leagues.
4. In each league's `/rosters`, their roster has `owner_id == user_id` (or lists them in `co_owners`). The team name is in `/users` (`metadata.team_name`, else `display_name`).

## Reads (once per run)

- Current week: `https://api.sleeper.app/v1/state/nfl`
- Per league: `https://api.sleeper.app/v1/league/{L}`, plus `/rosters`, `/users`, `/matchups/{week}`, `/transactions/{week}`. Settings live in `scoring_settings`, `roster_positions` and `settings` (`waiver_type`, `waiver_day_of_week`, `trade_deadline`).
- All players (large, fetch once and cache): `https://api.sleeper.app/v1/players/nfl`. Has `injury_status` and team.
- Projections: `https://api.sleeper.com/projections/nfl/{season}/{week}?season_type=regular&position[]=QB&position[]=RB&position[]=WR&position[]=TE&position[]=K&position[]=DEF`. Use the field that matches league scoring: `pts_half_ppr`, `pts_ppr` or `pts_std`.
- Weekly stats: `https://api.sleeper.com/stats/nfl/{season}/{week}?season_type=regular&...` (same position params). Use these for a defense's actual points in league scoring.
- Schedule: `https://api.sleeper.com/schedule/nfl/regular/{season}`. Teams missing from a week are on bye.
- Trending adds: `https://api.sleeper.app/v1/players/nfl/trending/add?lookback_hours=48&limit=60`
- Free agents: projected players not in any roster's `players` or `reserve` in that league.

Tips:

- The API can lag a few seconds after a move. Re-fetch with a cache-buster (`?t=<timestamp>`) after about 8 seconds, or check `/transactions/{week}`.
- Pending waiver claims don't appear in the public API. Check them in the UI (Team tab, waiver icon).
- From a browser tab on sleeper.com, the API allows cross-origin fetches, which is handy when the agent's only web access is the browser.

## Moves (browser, logged in)

- **Players page:** `https://sleeper.com/leagues/{L}/players`, with the Free agents filter on. A **+** next to a player means free agent: click it, pick the drop in the modal (scroll to reach bench rows), then Add Player. A **W** means he's on waivers: the same click opens a bid modal (set the FAAB amount, pick the drop, Make Waiver Bid).
- **Lineup:** Team tab. Use the week arrow to set a future week. Click a starter's position button, then the bench player to swap in.
- **Trades:** Trades tab. Pick the partner, select players on both sides, propose. Confirm it shows as pending.
- **Search box:** click it by coordinates. Clicking by element reference can leave focus in League Chat, so never type or press Enter there except to post an approved message.
- Sleeper puts new adds on the bench. Re-fill the slot afterward.
