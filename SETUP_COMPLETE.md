# 🎉 Submission & Leaderboard Setup Complete!

Your repository is now configured for fully automated scoring with a CSV‑based leaderboard and an interactive GitHub Pages view.

## ✅ What’s in Place

### Leaderboard System
- **`leaderboard/leaderboard.csv`** — Source of truth
- **`leaderboard.md`** — Auto-generated static view
- **`docs/leaderboard.html`** — Interactive leaderboard (GitHub Pages)

### Automation
- **`.github/workflows/score-submission.yml`** — CI scoring and leaderboard updates
- **`competition/validate_submission.py`** — Validates submission format
- **`update_leaderboard.py`** — Scores and updates CSV/Markdown/Docs

### Submission Format
Submissions must be:
```
submissions/inbox/<team>/<run_id>/predictions.csv.enc
submissions/inbox/<team>/<run_id>/metadata.json
```
Decrypted `predictions.csv` columns:
- `id`
- `y_pred` (probability or hard label)

## 🚀 How It Works (Automated)
1. Participant opens PR with `predictions.csv.enc` + `metadata.json`.
2. CI validates, scores, and comments on the PR.
3. On merge, CI appends results to `leaderboard/leaderboard.csv`.
4. `leaderboard.md` and `docs/leaderboard.csv` are regenerated.

## ✅ Final Steps for You
1. **Enable GitHub Actions** (Settings → Actions → General)
2. **Enable GitHub Pages** (Settings → Pages → main /docs)
3. **Add test labels as a secret** for CI scoring

## 📍 Leaderboard URL
Once Pages is enabled:
```
https://<org>.github.io/<repo>/leaderboard.html
```
