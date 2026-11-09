# Config (example)

Copy this to `config.md` (or wherever your agent keeps settings) and fill it in during first-run setup.

```yaml
owner:
  name: Alex
  timezone: America/New_York
leagues:
  - name: Office League
    platform: <your fantasy site>   # the site this league is on
    league_id: "123456"            # optional, only if the agent reads your site
    team: Alex's Team
    team_id: "4"                   # optional, only if the agent reads your site
    report: <published page link or path to the Markdown report>
    notes: 12 teams, PPR, FAAB $100, waivers Wednesday. Human summary only; settings come from the league.
moves: off                    # off: the agent recommends and you make the moves. on: it makes the pre-approved ones
preapproved:                  # only used with moves on
  - lineup_swaps
  - ir_moves
  - free_agent_add_drop
  - waiver_claims
faab:
  autopilot: false             # with moves on, true lets the agent place claims within the cap
  max_bid_pct_of_remaining: 30
trash_talk:
  enabled: false
  prefix: "AlexAgent: "
```

Trades and league chat posts always need a one-word yes, whatever is listed here.
