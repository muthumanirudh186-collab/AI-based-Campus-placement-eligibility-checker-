# AI Placement Eligibility Checker — Full Stack

A full-stack rebuild of the original single-file HTML project, now with a real
Node.js/Express backend, MongoDB database, JWT login, an admin panel, a
resume analyzer, an AI mock interview, a 500-company dataset, and a
PYQ + syllabus library.

## Project structure

```
placement-checker/
  backend/
    server.js            Express app entry point
    config/db.js          MongoDB connection
    models/                Mongoose schemas (User, Company, PYQ)
    routes/
      auth.js              Register / login / me (JWT)
      companies.js         List / search / eligibility check
      admin.js             Admin-only company CRUD + student list
      resume.js            Resume upload + analysis (PDF or text)
      interview.js         AI mock interview questions + scoring
      content.js           PYQs and syllabus endpoints
    middleware/auth.js     JWT auth + admin guard
    data/
      companies.json       500-company dataset
      pyqs.json            300 previous-year questions
      syllabus.json         Branch-wise syllabus
      seed.js              Loads companies.json into MongoDB
  frontend/
    index.html            Student eligibility checker (calls backend API)
    login.html             Register / login
    resume.html             Resume analyzer UI
    interview.html          AI mock interview chat UI
    pyq.html                 PYQ + syllabus browser
    admin.html               Admin panel
    css/style.css
    js/api.js               Shared fetch helper + auth/session handling
```

## 1. Requirements

- Node.js 18+
- MongoDB running locally (`mongodb://127.0.0.1:27017`) or a MongoDB Atlas
  connection string

## 2. Backend setup

```bash
cd backend
npm install
cp .env.example .env      # then edit .env: set MONGO_URI and JWT_SECRET
npm run seed               # loads the 500 companies into MongoDB
npm run dev                 # or: npm start
```

The API runs at `http://localhost:5000/api`. Check `http://localhost:5000/api/health`.

## 3. Frontend

The frontend is plain HTML/CSS/JS, no build step required. Simplest option:

```bash
cd frontend
npx serve .        # or just open index.html directly in a browser
```

`js/api.js` points at `http://localhost:5000/api` — change `API_BASE` there
if your backend runs elsewhere.

## 4. Using it

1. Open `login.html`, create an account (tick "Register as admin" for one
   account so you have an admin login too).
2. `index.html` — enter your branch/CGPA/backlogs/skills and check eligibility
   against the live company dataset.
3. `resume.html` — upload a PDF/txt resume (or paste text) for a keyword and
   structure-based score with improvement tips.
4. `interview.html` — start a mock interview by branch/round; answers are
   scored by keyword overlap against a model answer by default.
5. `pyq.html` — browse previous-year questions by company/branch/round, and
   the core syllabus per branch.
6. `admin.html` (admin accounts only) — add/remove companies, view all
   registered students.

## 5. Optional: real AI scoring for the mock interview

By default, `/api/interview/evaluate` scores answers with simple keyword
overlap — no external API needed, works offline. If you set `ANTHROPIC_API_KEY`
in `backend/.env`, it will instead call the Claude API to score and give
richer feedback (see `routes/interview.js`). The same pattern can be extended
to the resume analyzer if you want LLM-based resume feedback instead of the
keyword-based one that ships here.

## Notes & limitations

- This is a learning/demo project, not production-hardened: add rate
  limiting, input validation, and email verification before deploying
  publicly.
- Admin self-registration (the checkbox on the register page) is for demo
  convenience only — in a real deployment, promote users to admin manually
  in the database instead.
- The resume analyzer and mock interview scoring are rule-based by default
  (keyword/skill matching) so the project runs with zero external API costs.
  Swap in a real LLM call any time using the pattern already shown in
  `routes/interview.js`.
