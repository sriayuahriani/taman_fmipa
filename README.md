# Git + GitHub + Pannellum: Terminal-Only Student Tutorial

## Learning goal

Publish a 3-scene, 360-degree campus tour using **only the terminal** for file management, editing, Git, repository creation, and GitHub Pages configuration. View the resulting tour in a web browser. The only account-management exception is the browser approval normally required for initial `gh` login.

The starter has 3 **schematic, generated equirectangular demo images**. They work for learning, but they are **not photographs of real places**. Replace them with your own genuine 360-degree equirectangular images (normally 2:1 width:height). A normal photograph stretched to 2:1 does not become a true 360-degree panorama.

## Prerequisites

- A GitHub account that you have already created.
- A **Bash-like terminal**, such as Linux Terminal, macOS Terminal, WSL/Ubuntu, or Git Bash (Windows). Commands below use Bash syntax.
- Git, GitHub CLI (`gh`), Python 3 for local preview, and `nano` or another terminal editor.
- Internet access when signing in, loading Pannellum from its CDN, and pushing to GitHub.

Installation references: <https://git-scm.com/downloads>, <https://cli.github.com/>, <https://www.python.org/downloads/>. On Ubuntu, the packages may be installed using `sudo apt update && sudo apt install git gh python3 nano unzip`. On macOS with Homebrew, run `brew install git gh python` (macOS typically includes `nano`). Windows students can install Git and GitHub CLI, and use Git Bash or WSL; commands may vary by shell.

Verify the tools:

```bash
git --version
gh --version
python3 --version
nano --version
```

If `python3` is unavailable on Windows, try `python --version` / `python -m http.server 8000`.

## 1. Set up Git identity (do once)

```bash
git config --global user.name "Your Full Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global --list
```

Use the email linked to your GitHub account if you want commits attributed to it; GitHub also supports a private no-reply email.

## 2. Sign in to GitHub from the terminal (do once)

```bash
gh auth login --git-protocol https --web
gh auth status
gh auth setup-git
```

Select `GitHub.com` if asked. `gh` may provide a **one-time code** and ask you to approve sign-in in a browser. This browser confirmation is part of authentication; no repository editing on GitHub's website is needed. Never share authentication tokens or passwords. When using a remotely managed terminal without a browser, ask your instructor for an approved authentication method; a token can be supplied to `gh`, but do **not** type a token in course submissions or commit it into Git.

## 3. Unzip the provided starter (terminal only)

Put `pannellum_terminal_starter.zip` in your downloads directory. In a Bash terminal, navigate to the folder where you downloaded it, then:

```bash
unzip pannellum_terminal_starter.zip
cd pannellum_terminal_starter
pwd
ls -la
ls images
```

Expected structure:

```text
pannellum_terminal_starter/
├── index.html
├── images/
│   ├── entrance.jpg
│   ├── classroom.jpg
│   └── library.jpg
├── .nojekyll
└── README.md
```

If creating the project without the starter, use `mkdir pannellum-tour && cd pannellum-tour && mkdir images`, then create `index.html` with `nano index.html` based on the starter source code and copy 3 actual 360-degree photos into `images/`.

### Terminal editor basics

```bash
nano index.html
```

- Write or modify text inside the editor.
- Save: **Ctrl+O**, then **Enter**.
- Exit: **Ctrl+X**.
- Display file contents in terminal: `cat index.html`.

## 4. Understand the website

`index.html` loads Pannellum CSS and JavaScript from the 2.5.7 CDN:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pannellum@2.5.7/build/pannellum.css">
<script src="https://cdn.jsdelivr.net/npm/pannellum@2.5.7/build/pannellum.js"></script>
```

The viewer is displayed in `<div id="panorama"></div>` and started via:

```javascript
pannellum.viewer("panorama", {
  "default": { "firstScene": "entrance", "autoLoad": true },
  "scenes": {
    "entrance": {
      "type": "equirectangular",
      "panorama": "images/entrance.jpg"
    }
  }
});
```

The full starter includes three scenes: `entrance`, `classroom`, and `library`.

### How scene-to-scene hotspots work

Inside the `hotSpots` array of the **source scene**:

```javascript
{
  "pitch": -5,
  "yaw": 14,
  "type": "scene",
  "text": "Go to Classroom",
  "sceneId": "classroom"
}
```

- `pitch`: vertical position in degrees; 0 is approximately the horizon.
- `yaw`: horizontal position in degrees; tune this number to match the location in the photo.
- `type: "scene"`: navigation hotspot.
- `sceneId`: must EXACTLY match a name under `scenes`.

Information hotspot example:

```javascript
{ "pitch": 10, "yaw": -50, "type": "info", "text": "Welcome!" }
```

Information hotspots show text rather than changing scene. In the starter, every scene has navigation links and an information hotspot.

## 5. Preview locally

From the project directory:

```bash
python3 -m http.server 8000
```

Open `http://localhost:8000/` in any browser to **view** the tour. To stop the server, press **Ctrl+C** in the terminal. For Windows with only `python`, use `python -m http.server 8000`.

Students should test: (1) image rotates, (2) entrance -> classroom, (3) classroom -> library, (4) library -> entrance, (5) an information icon shows text.

Why not double-click the HTML file? Using the local web server avoids browser-specific restrictions of `file://` pages and more closely reproduces GitHub Pages.

## 6. Create a Git repository

Do this in the folder that contains `index.html`:

```bash
git init -b main
git status
git add .
git commit -m "Create 360 tour with three scenes and hotspots"
git log --oneline -1
```

If Git reports a missing name or email, return to Step 1. If the repository already exists, do not run `git init` again.

## 7. Create a PUBLIC GitHub repository and push, no GUI

```bash
gh repo create pannellum-tour --public --source=. --remote=origin --push
git remote -v
git status
gh repo view --json url --jq .url
```

This creates the remote named `pannellum-tour`, adds it as `origin`, and uploads your commit. Use a different repository name if `pannellum-tour` already exists on your account. Do not use `--add-readme`, because the local repository already contains files.

## 8. Enable GitHub Pages using the GitHub API, still no GUI

Run these commands **inside the project's Git repository**, after pushing `main`:

```bash
gh api -X POST 'repos/{owner}/{repo}/pages' \
  -f 'build_type=legacy' \
  -f 'source[branch]=main' \
  -f 'source[path]=/'
```

The API creates a Pages site published from the `main` branch, repository root. If GitHub reports that Pages is **already enabled** (for example HTTP 409 or 422), do not recreate it; inspect it using:

```bash
gh api 'repos/{owner}/{repo}/pages' --jq '{status, html_url, source}'
```

The `html_url` is the published website address, typically `https://YOUR-USERNAME.github.io/pannellum-tour/` for a personal repository. Publishing is asynchronous; `status` may initially indicate that a build is pending or in progress. You can also inspect workflow runs with `gh run list --limit 5`.

## 9. Edit, commit and push updates

Try changing the title, hotspot text or a scene image in `nano index.html`:

```bash
nano index.html
git diff
git status
git add index.html
git commit -m "Improve tour text and navigation"
git push origin main
```

After GitHub Pages completes the updated deployment, refresh the published URL.

## 10. Replace the demo images with your own photos

Examples (adapt file paths):

```bash
cp ~/Pictures/entrance-360.jpg images/entrance.jpg
cp ~/Pictures/classroom-360.jpg images/classroom.jpg
cp ~/Pictures/library-360.jpg images/library.jpg
ls -lh images/
git add images/
git commit -m "Use my own campus 360 photos"
git push origin main
```

The source image should be a 360-degree equirectangular panorama (normally 2:1). Keep filenames **exactly** consistent with the `panorama` paths in HTML; capitalization matters on GitHub Pages.

## 11. Add a FOURTH scene and its hotspot

1. Copy `~/Pictures/lab-360.jpg` to `images/lab.jpg` using `cp`.
2. Run `nano index.html`.
3. Add a `"lab": {...}` entry under `"scenes"` (with a comma between neighbouring scenes):

```javascript
"lab": {
  "title": "Computer Lab",
  "type": "equirectangular",
  "panorama": "images/lab.jpg",
  "hotSpots": [
    {"pitch": -5, "yaw": 35, "type": "scene", "text": "Back to Library", "sceneId": "library"}
  ]
}
```

4. Add a hotspot to the existing `library` scene:

```javascript
{"pitch": -5, "yaw": 40, "type": "scene", "text": "Visit Computer Lab", "sceneId": "lab"}
```

5. Save, check locally with the Python server, then use `git add .`, `git commit -m "Add computer lab scene"`, and `git push origin main`.

## Common problems

| What happened? | Check in terminal |
|---|---|
| `gh: command not found` | Install GitHub CLI and reopen terminal; verify `gh --version`. |
| `not logged into any GitHub hosts` | Run `gh auth login --git-protocol https --web` and `gh auth status`. |
| `Author identity unknown` | Set `git config --global user.name` and `user.email`. |
| Panorama is blank / missing | `ls -lh images/`; check the exact HTML filename and capitalization. |
| Hotspot click does nothing | Make sure `sceneId` exactly matches an entry in `scenes`. |
| `404` on GitHub Pages | Confirm `index.html` at repo root, branch `main`, Pages API status, and wait for deployment. |
| `Repository already exists` | Use another name in `gh repo create`, or use the already-created remote. |
| Viewer CSS/JS fails to load | The Pannellum CDN requires internet; verify the browser can access jsDelivr. |
| Viewer distorted | Use a genuine spherical/equirectangular 360-degree panorama, not an ordinary picture. |

## Student deliverable / checklist

- [ ] Git identity set and GitHub authentication works (`gh auth status`).
- [ ] `git log --oneline -1` shows a commit.
- [ ] `gh repo view --json url --jq .url` shows a public repository.
- [ ] Three 360 scenes load correctly and can be navigated with hotspots.
- [ ] At least one information hotspot is visible.
- [ ] The GitHub Pages URL loads on a device other than the student's local computer.
- [ ] (Challenge) Fourth scene, with a forward and return hotspot.

## References

- <https://pannellum.org/documentation/examples/tour/>
- <https://pannellum.org/documentation/reference/>
- <https://cli.github.com/manual/gh_auth_login>
- <https://cli.github.com/manual/gh_repo_create>
- <https://cli.github.com/manual/gh_api>
- <https://docs.github.com/en/rest/pages/pages>
- <https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site>
