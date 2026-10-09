# Config (example)

Copy this to `config.md` (or wherever your environment persists settings) and fill it in during first-run setup.

```yaml
owner:
  name: Alex
  timezone: America/New_York
leagues:
  - name: Office League
    platform: espn            # espn | yahoo | sleeper | other
    league_id: "123456"
    team: Alex's Team
    team_id: "4"              # platform's ID for the owner's team
    report: <artifact link or path to the Markdown report>
    notes: 12 teams, PPR, FAAB $100, waivers Wednesday. Human summary only; settings come from the league.
preapproved:                  # do-now without asking
  - lineup_swaps
  - ir_moves
  - free_agent_add_drop
  - waiver_claims
faab:
  autopilot: true
  max_bid_pct_of_remaining: 30
trash_talk:
  enabled: false
  prefix: "AlexAgent: "
```

Trades and league chat posts always need a one-word yes, whatever is listed here.
