# TutorLoop

Turn a tutor's rough session notes into (1) a parent update, (2) a targeted practice set with answer key, and (3) a 60-minute next-session plan. Output in 8 languages.

## Deploy in ~10 minutes (Vercel, free tier)
1. Push this repo to GitHub.
2. In Vercel: **Add New > Project**, import the repo. No build settings needed.
3. Add environment variables (see `.env.example`): `ANTHROPIC_API_KEY`, `ACCESS_CODES` (comma-separated codes you hand to testers; leave empty for open access), optional `RATE_LIMIT_PER_HOUR`, `MODEL`.
4. Deploy. Share the URL plus one access code per tester.

Never commit your API key. Put a spend limit on the key in the Anthropic Console before sharing.

## Architecture
- `index.html`: static single page, no framework, no build step.
- `api/generate.js`: serverless function. Validates the access code, rate-limits per IP, builds the prompt server-side (so the endpoint can't be used as a free general-purpose proxy), calls the Claude API, returns JSON.
- No database. Nothing about students is stored. Keep it this way until demand is proven; it avoids most student-privacy obligations.
- Known limits: rate limiting is in-memory (resets on cold start), fine for beta but not production.

## Prompt design principles
1. Facts only from the notes; never invent scores or events.
2. Weakness framed with one concrete next step.
3. Practice problems must target the specific weakness named in the notes, ordered easy to hard, with worked answers.
4. Notes treated as data, not instructions (prompt-injection guard).
5. Structured JSON output, so the UI can render tabs and later export to PDF or email.

## Validation plan (week 1)
- **Target:** independent tutors and small centers (1 to 20 tutors).
- **Offer:** "Send one session's notes, get the parent update back in 30 seconds. Free while in beta."
- **Outreach script:** "Hi [name], I'm building a tool that turns your session notes into a parent update and homework in 30 seconds. Would you try it on one real session this week and tell me what's wrong with it?"
- **Metrics that matter:** tutors who run 3+ real sessions; % of outputs sent with little or no editing; number who say they'd pay; number who prepay.
- **Kill or continue:** fewer than 2 in 10 reach 3 real uses, or nobody will prepay after 20 active testers, means revisit the buyer or problem.

## Pricing hypothesis
Free: 3 generations. Solo tutor: $15 to $20/month. Center: $8 per tutor/month, 5-seat minimum. Test one price, not a menu.

## Roadmap (only after users ask)
1. Tutor voice: learn sign-off and style from one sample message.
2. Student history per tutor, which becomes the moat.
3. Parent-facing link or email send, PDF export.
4. Stripe billing and accounts.
5. Curriculum packs (IB, IGCSE, national exams).

## Risks
- Answer-key errors: keep "Review before sending" visible; add subject-expert spot checks.
- Low defensibility as a pure prompt wrapper: moat comes from history, parent layer, distribution.
- Minors' data: don't add accounts or storage before legal review (FERPA, GDPR-K, local law).

## Local run
`npm i -g vercel && vercel dev` (needs env vars in a local `.env`).
