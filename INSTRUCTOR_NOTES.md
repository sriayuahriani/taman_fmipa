# Instructor Notes: Terminal-Only Git + GitHub + Pannellum

## Suggested duration: 90 minutes

- 00–10: Final demo, tour structure, key terminology.
- 10–25: Check installations, configure Git, authenticate with `gh`.
- 25–35: Navigate folders and edit HTML using `nano`.
- 35–55: Read Pannellum scene map; change hotspot label, understand `pitch`, `yaw`, `sceneId`.
- 55–65: Start `python3 -m http.server 8000` and test locally.
- 65–78: Initialize Git, commit, `gh repo create ... --push`.
- 78–87: Enable Pages from the terminal and inspect build status / URL.
- 87–90: Exit ticket and optional four-scene challenge.

## Pedagogy

1. Live-demo the provided three-scene tour first so students understand the goal.
2. Ask students to predict the command output before running commands.
3. Have students read the `index.html` config and identify the three scene IDs.
4. Pair students to trace an outgoing and incoming scene hotspot.
5. Explain `git` = local history; GitHub = remote hosting; GitHub Pages = public website.
6. Use `git status` before and after every `git add` and `git commit`.
7. Do not require users to upload secrets, passwords or access tokens.

## Clear limitations

- Signing up for a new GitHub account is outside the lesson. First-time `gh auth login --web` normally requires browser approval; *all repo, Git and Pages operations* are terminal based.
- Demo JPGs are generated schematic images that demonstrate spherical rendering. Real 360 tours need equirectangular camera output.
- The Pannellum Javascript and CSS are loaded from a public CDN; online access is required.
- Pages publishing is not instantaneous. Students can inspect it with `gh api .../pages` and `gh run list`.
- Bash syntax is used; use Linux/macOS/WSL/Git Bash to avoid PowerShell syntax differences.

## 5-question check with answers

1. **What does `git add .` do?** Stages files for the next commit; it does not upload them.
2. **What does `git push` do?** Uploads local commits to the linked remote branch.
3. **What does `sceneId` mean?** The unique target panorama ID from the `scenes` configuration.
4. **Difference between info and scene hotspot?** `info` displays a message; `scene` navigates to another panorama.
5. **What file must be at the published root?** `index.html` (as used in this activity).

## Rubric (10 points)

- Git/GitHub authentication and repo: 2.
- Three valid panorama scenes with working images: 3.
- Two or more functional navigation hotspots: 2.
- At least one informational hotspot: 1.
- GitHub Pages public URL working: 2.

Optional advanced task: Add `lab.jpg` and a `lab` scene with outgoing and return navigation.
