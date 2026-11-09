# League info and moves on any platform

## League info

By default the owner shares it: a screenshot or paste of the roster, this week's matchup, the league's scoring settings and any free agents they're eyeing. Ask for whatever's missing, and save what doesn't change (scoring, lineup slots, waiver rules) in the config so you only ask once.

If the owner wants the agent to read the league itself, and their agent has a browser tool:

1. Ask for the league URL. Open it and find: league settings (scoring, lineup slots, waivers, trade deadline), the owner's roster, this week's matchup, and the free agent list with projections.
2. Prefer reading page text over screenshots. Never ask for or store passwords, tokens or cookies.
3. Write what you learned about the platform into a new file here (`references/<platform>.md`) so the next run is faster.

Some platforms limit automated tools in their terms. Whether to let the agent read or act on the site is the owner's call.

## Moves (only with moves on)

On the first move of each kind (lineup swap, add/drop, waiver claim, trade), take it slowly: confirm the page, make the move, then re-read the page to verify it landed. Data can lag a few seconds after a move, so wait before calling it done.

With moves off, end each run with a short list of moves for the owner to make, each with the stat behind it.

## Tips that hold on most platforms

Use these in recommendations too, so the owner's moves land.

- Adds and winning waiver claims often land on the bench. Move each one into the slot it was meant for.
- Between weeks, unrostered players can sit on waivers until the waiver run, even if their team was on bye. Check before planning a free-agent add: if he's on waivers, it's a claim, so put it in before the run.
- Some platforms block lineup changes while a player on IR is no longer IR-eligible. Move him off IR first (it can take a drop if the roster is full), then set the lineup.
- Pending waiver claims may not show everywhere. Check the platform's own claims view.
