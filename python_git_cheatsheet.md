# Cheat sheet — Python, VS Code and GitHub on Windows

Windows 10/11, no GPU. Use **PowerShell** for everything.

---

## Part A — Setup

### 1 · Install (once)

```powershell
winget install -e --id Python.Python.3.13
winget install -e --id Git.Git
winget install -e --id Microsoft.VisualStudioCode
winget install -e --id GitHub.cli
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

Close PowerShell, open a new one.

No winget? Installers from python.org · git-scm.com · code.visualstudio.com · cli.github.com.
Python installer: tick **"Add python.exe to PATH"**, then click **"Disable path length limit"** at the end.

### 2 · Check

```powershell
python --version   # 3.10 – 3.13
git --version
gh --version
code --version
```

### 3 · GitHub (once)

1. Create a free account at github.com (use your HTW e-mail; optional: education.github.com for the free Student Pack).
2. Tell Git who you are and log in:

```powershell
git config --global user.name "Vorname Nachname"
git config --global user.email "you@htw-berlin.de"
gh auth login
# GitHub.com → HTTPS → Yes → browser
```

### 4 · Project (once)

```powershell
cd $HOME\Documents
gh repo clone <org>/<repo> emerging-tech
cd emerging-tech
python -m venv .venv
.venv\Scripts\Activate.ps1   # → (.venv)
python -m pip install -U pip
pip install numpy pandas matplotlib `
  requests scikit-learn torch torchvision `
  transformers datasets `
  sentence-transformers faiss-cpu
```

### 5 · VS Code (once)

| Step | Keys |
|---|---|
| Open project | `code .` in the project folder → Trust |
| Extensions: Python, Jupyter (Microsoft) | `Ctrl+Shift+X` |
| Python: Select Interpreter → `.venv` | `Ctrl+Shift+P` |

### 6 · Test

```powershell
python -c "import torch; print(torch.__version__)"
cd rover; python harness.py
# grid + baseline score = done
```

### Every day

```powershell
cd $HOME\Documents\emerging-tech
.venv\Scripts\Activate.ps1
# or open the folder in VS Code
```

### GitHub, daily

```powershell
git pull      # latest first
git status    # what changed?
git add .
git commit -m "Short message"
git push
```

VS Code does the same in the Source Control panel (`Ctrl+Shift+G`).

### When it breaks

| Symptom | Fix |
|---|---|
| `python` opens Microsoft Store | Start → Manage app execution aliases → turn off `python.exe`, `python3.exe` |
| `'python' is not recognized` | Use `py` instead, or re-run installer → Modify → add to PATH |
| running scripts is disabled | `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` |
| `No module named 'torch'` | No `(.venv)` in prompt, or VS Code on wrong interpreter → step 5 |
| `No matching distribution for torch` | Python too new or 32-bit → install 3.13 64-bit |
| Error about long file path | Python installer → Disable path length limit, or clone to `C:\et` |
| gh asks for a password / push rejected | `gh auth login` again; check `git config user.email` matches GitHub |
| Laptop too slow, Days 3–4 | Fine — Google Colab free tier |

---

## Part B — Git and GitHub

### 1 · Git and GitHub — two different things

| | Git | GitHub |
|---|---|---|
| What | A program on your laptop | A website |
| Does | Records snapshots (commits) of a folder | Stores a copy of that folder online, for the team |
| Works offline | Yes | No |

- **Repository (repo):** a project folder whose history Git tracks.
- **Commit:** one saved snapshot, with a message saying why.
- **Push / pull:** send your commits to GitHub / fetch everyone else's.

### 2 · The daily loop

```
edit files → git add → git commit → git push
     ↑                                  │
     └────────── git pull ←─────────────┘
```

```powershell
git pull
git status
git add .
git commit -m "Add CNN agent for Day 3"
git push
```

**Pull before you start, push when you stop.** Most team problems come from skipping the first `git pull`.

### 3 · Team setup — once per team

One person creates the repo and invites the others:

```powershell
gh repo create team-rover --private --clone
```

GitHub → repo → Settings → Collaborators → Add people → each team member's GitHub name.

Everyone else accepts the e-mail invite, then:

```powershell
gh repo clone <owner>/team-rover
```

### 4 · Branches and pull requests

Work on your own branch, so a half-finished change never breaks the team's code.

```powershell
git switch -c day3-cnn
# ... edit, add, commit as usual ...
git push -u origin day3-cnn
gh pr create --fill
```

On GitHub a teammate opens the pull request, reads the change, clicks **Merge**. Then everyone:

```powershell
git switch main
git pull
```

### 5 · Merge conflicts

Two people changed the same lines. `git pull` stops and the file contains:

```
<<<<<<< HEAD
lr = 0.01
=======
lr = 0.1
>>>>>>> main
```

In VS Code click **Accept Current**, **Accept Incoming** or **Accept Both** above the block, or edit by hand and delete the markers. Then:

```powershell
git add .
git commit -m "Resolve merge conflict"
git push
```

Not a disaster. Nothing is lost; both versions are still in the history.

### 6 · Never commit these

Create a file called `.gitignore` in the project folder, **before your first commit**:

```
.venv/
__pycache__/
.env
*.ipynb_checkpoints
data/
```

| Never commit | Why |
|---|---|
| `.venv/` | Hundreds of MB, only works on your laptop |
| API keys, `.env` | Bots scan GitHub for keys within minutes |
| Large datasets | GitHub rejects files over 100 MB |

Pushed a key by accident? **Revoke it on the provider's website immediately.** Deleting the file afterwards does not help; it stays in the history.

---

## PyCharm equivalents

| VS Code | PyCharm |
|---|---|
| Source Control panel (`Ctrl+Shift+G`) | Commit tool window (`Alt+0`) |
| Commit | `Ctrl+K` |
| Push | `Ctrl+Shift+K` |
| Pull | `Ctrl+T` (Update Project) |
| Select Interpreter → `.venv` | Settings → Project → Python Interpreter → Add → Existing → `.venv` |
| Accept Current / Incoming in conflicts | Git → Resolve Conflicts → Merge window |
