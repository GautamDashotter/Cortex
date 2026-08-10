# Job Application Auto-Fill Agent

Opens each job application link you give it, fills whatever fields it can
recognize from your profile data (name, email, phone, LinkedIn, resume
upload, work authorization, etc.), and then **pauses for you to review and
submit yourself.** It never clicks Submit.

## Setup

```bash
pip install -r requirements.txt
playwright install chromium
```

## Configure your data

1. Copy `profile.example.json` to `profile.json` and fill in your real info
   (use absolute paths for `resume_path` / `cover_letter_path`).
2. Copy `links.example.txt` to `links.txt` and paste in the job application
   URLs you want to process, one per line.

## Run it

```bash
python autofill_agent.py --profile profile.json --links links.txt
```

A real Chrome window will open. For each link, it will:

1. Load the page and wait for the form to render.
2. Fill every text field, dropdown, and resume/cover-letter upload it can
   confidently match to your profile.
3. Print a list of anything it couldn't confidently fill (custom essay
   questions, unusual fields, etc.) so you know what to check.
4. Pause — you review the form in the browser, fill in anything flagged,
   and click Submit yourself. Press Enter in the terminal to move to the
   next link.

## Notes & limits

- **It won't bypass logins or CAPTCHAs.** If a site (e.g. LinkedIn) needs
  you signed in, log in manually the first time — it's saved in
  `./browser_profile/` and reused automatically after that.
- **Custom/essay questions are always left for you.** The matcher only
  handles standard fields; anything site-specific gets flagged, not guessed.
- **Some ATS platforms (Workday especially) use multi-step wizards** or
  unusual widgets — the agent fills what it can on each step but you may
  need to click "Next" yourself between steps and re-trigger a pass (just
  rerun with the same link once you're on the right step, or extend
  `process_link` to loop until a "submit" button appears).
- **Terms of Service:** most ATS-hosted application forms (Greenhouse,
  Lever, Workday, Ashby, etc.) are fine to interact with normally, since
  you're just filling out a public application form as yourself, the same
  as clicking through it manually. Sites like LinkedIn explicitly prohibit
  automated activity in their ToS even for your own account, and can
  suspend accounts caught automating "Easy Apply" — this tool is built to
  stop short of that (auto-fill + manual submit, no scraping, no mass
  automated clicking), but you're using it at your own discretion on any
  given site.

## Extending field matching

Edit the rule lists near the top of `autofill_agent.py`:

- `TEXT_FIELD_RULES` — free-text fields (name, email, etc.)
- `YES_NO_FIELD_RULES` — dropdown/radio yes-no questions
- `FILE_FIELD_RULES` — file upload fields (resume, cover letter)

Each rule is `(profile_json_key, [keywords_to_match])`. Add keywords as you
run into forms that phrase things differently.
