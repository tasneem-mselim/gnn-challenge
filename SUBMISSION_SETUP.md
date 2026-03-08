# Submission & Leaderboard Setup

**This project uses automated scoring only.** Submissions are evaluated in CI against a hidden test set and the leaderboard updates on merge.

## Automated Scoring with GitHub Actions

### How It Works

1. **Participant** submits a PR with:
   - `submissions/inbox/<team>/<run_id>/predictions.csv.enc`
   - `submissions/inbox/<team>/<run_id>/metadata.json`
2. **GitHub Actions** automatically:
   - Rejects plaintext prediction files
   - Decrypts predictions using organizer private key
   - Validates the decrypted submission format
   - Scores predictions using hidden test labels
   - Posts results as a PR comment
   - Updates the leaderboard on merge

### Files Used

- `.github/workflows/score-submission.yml` — CI workflow
- `competition/validate_submission.py` — submission format validation
- `scoring_script.py` — scoring utility
- `update_leaderboard.py` — updates leaderboard CSV + markdown
- `leaderboard/leaderboard.csv` — source of truth
- `docs/leaderboard.html` — interactive leaderboard (GitHub Pages)

### Required GitHub Secrets

- `TEST_LABELS_CSV_B64` — base64-encoded hidden test labels (`data/test_labels.csv`)
- `SUBMISSION_PRIVATE_KEY_ASC` — ASCII-armored private key for decrypting `predictions.csv.enc`
- `SUBMISSION_PRIVATE_KEY_PASSPHRASE` — optional passphrase for private key (leave empty if key is not passphrase-protected)

### Organizer Key Setup

Generate a dedicated keypair locally (one-time):

```bash
gpg --full-generate-key
gpg --armor --export "<KEY_UID_OR_EMAIL>" > .github/keys/submission_public.asc
gpg --armor --export-secret-keys "<KEY_UID_OR_EMAIL>" > /tmp/submission_private.asc
```

Then:

1. Commit `.github/keys/submission_public.asc` to the repo.
2. Add contents of `/tmp/submission_private.asc` as secret `SUBMISSION_PRIVATE_KEY_ASC`.
3. If key has a passphrase, add it to `SUBMISSION_PRIVATE_KEY_PASSPHRASE`.

### Workflow Trigger

```yaml
on:
  pull_request_target:
    paths:
      - 'submissions/inbox/**'
```

### Leaderboard Architecture

- **Source of truth**: `leaderboard/leaderboard.csv`
- **Static view**: `leaderboard.md` (auto-generated)
- **Interactive view**: `docs/leaderboard.html` (GitHub Pages)

`update_leaderboard.py`:
1. Scores new submissions
2. Appends rows to `leaderboard/leaderboard.csv`
3. Recomputes ranks
4. Regenerates `leaderboard.md`
5. Copies CSV to `docs/leaderboard.csv`

Encrypted submission notes:

- Participant files are stored encrypted in-repo as `predictions.csv.enc`.
- Decryption only happens in CI runtime memory/temp storage.
- Decrypted files are removed during workflow cleanup.

### Enabling GitHub Pages (Interactive Leaderboard)

1. Settings → Pages
2. Source: `main` branch, `/docs` folder
3. Save

Your leaderboard will be available at:
```
https://<org>.github.io/<repo>/leaderboard.html
```

---

## ✅ Checklist

- [x] Create `leaderboard/leaderboard.csv`
- [x] Add `docs/leaderboard.html`, `docs/leaderboard.css`, `docs/leaderboard.js`
- [x] Set up `.github/workflows/score-submission.yml`
- [x] Create `competition/validate_submission.py`
- [ ] Enable GitHub Actions
- [ ] Enable GitHub Pages
- [ ] Test workflow with a sample PR
