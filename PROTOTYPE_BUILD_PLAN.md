# 1-Month Prototype Build Plan
## Team Capybara - AI-Powered Financial Literacy & Guidance Platform

---

## PROTOTYPE SCOPE (What We Build vs What We Skip)

For a working prototype in 1 month with a 3-person team, we must be ruthless about scope.

### WE BUILD (Prototype):
- Web app only (Next.js) -- no mobile app yet
- User onboarding with financial profile questionnaire
- Personalized dashboard showing financial health snapshot
- AI conversational guide (chatbot) for financial Q&A
- 3 financial simulators (savings, loan EMI, investment comparison)
- Basic budgeting tool (manual income/expense entry + spending breakdown)
- 2-3 learning modules (Budgeting 101, Saving Strategies, Investment Basics)
- Firebase authentication (email/Google login)
- Deployed and accessible via URL

### WE SKIP (Post-Prototype / V2):
- Flutter mobile app (build after prototype is validated)
- Redis caching (not needed at prototype scale)
- Bank account linking / eSewa integration
- Gamification (badges, streaks, leaderboards)
- Custom AI fine-tuning (use base OpenAI/Claude with good prompts first)
- Advanced analytics and reporting
- Multi-language support (Nepali translation)
- Admin panel

---

## CONFIRMED TECH STACK (Prototype)

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | Next.js 14 + Tailwind CSS | Web app with server-side rendering |
| Backend | Python + FastAPI | REST API server |
| Database | PostgreSQL | User data, goals, transactions, learning progress |
| AI/ML | OpenAI API + LangChain | Conversational financial guide |
| Charts | Chart.js (via react-chartjs-2) | Financial simulators and spending visualizations |
| Auth | Firebase Auth | User login (email + Google) |
| Deployment | Vercel (frontend) + Railway/Render (backend) | Free-tier hosting for prototype |

---

## TEAM ROLES

| Member | Primary Role | Secondary Role |
|---|---|---|
| **Prasikshya** (Team Lead) | Backend (FastAPI + Database + AI integration) | Project management, deployment |
| **Shija** | Frontend (Next.js + Tailwind + Chart.js) | UI/UX design |
| **Sanskriti** | Content + Learning Modules + Testing | Frontend support, user testing |

*Roles can be adjusted based on each member's actual strengths.*

---

## DATABASE SCHEMA (Design on Day 1)

```
users
├── id (UUID, primary key)
├── email (string, unique)
├── name (string)
├── age_group (enum: 18-21, 22-25, 26-30)
├── occupation (enum: student, employed_full, employed_part, entrepreneur, not_working)
├── monthly_income (integer, NPR)
├── financial_goals (array: study_abroad, save, invest, start_business, loan, emergency_fund)
├── risk_tolerance (enum: low, medium, high)
├── financial_comfort (enum: comfortable, managing, strained, very_strained)
├── created_at (timestamp)
└── updated_at (timestamp)

transactions
├── id (UUID, primary key)
├── user_id (foreign key -> users)
├── type (enum: income, expense)
├── category (enum: food, transport, rent, education, entertainment, savings, other)
├── amount (integer, NPR)
├── description (string, optional)
├── date (date)
└── created_at (timestamp)

savings_goals
├── id (UUID, primary key)
├── user_id (foreign key -> users)
├── goal_name (string, e.g., "Study Abroad Fund")
├── target_amount (integer, NPR)
├── current_amount (integer, NPR, default 0)
├── deadline (date)
└── created_at (timestamp)

chat_history
├── id (UUID, primary key)
├── user_id (foreign key -> users)
├── role (enum: user, assistant)
├── message (text)
├── created_at (timestamp)

learning_progress
├── id (UUID, primary key)
├── user_id (foreign key -> users)
├── module_id (string)
├── module_name (string)
├── completed_lessons (integer, default 0)
├── total_lessons (integer)
├── quiz_score (integer, nullable)
├── status (enum: not_started, in_progress, completed)
└── updated_at (timestamp)
```

---

## WEEK-BY-WEEK PLAN

---

## WEEK 1: FOUNDATION (Days 1-7)
**Goal:** Project setup, authentication, onboarding, database -- everything works end-to-end.

---

### Day 1 (Monday) -- Project Setup

**Prasikshya (Backend):**
- Initialize FastAPI project with folder structure:
  ```
  backend/
  ├── app/
  │   ├── main.py
  │   ├── config.py
  │   ├── models/
  │   ├── routes/
  │   ├── services/
  │   └── utils/
  ├── requirements.txt
  └── .env
  ```
- Set up PostgreSQL database (local + Railway for production)
- Create all database tables using SQLAlchemy ORM
- Create `.env` file with database URL, API keys
- Test database connection

**Shija (Frontend):**
- Initialize Next.js 14 project with App Router:
  ```
  frontend/
  ├── src/
  │   ├── app/
  │   │   ├── layout.tsx
  │   │   ├── page.tsx
  │   │   ├── login/
  │   │   ├── onboarding/
  │   │   ├── dashboard/
  │   │   ├── chat/
  │   │   ├── budget/
  │   │   ├── simulators/
  │   │   └── learn/
  │   ├── components/
  │   ├── lib/
  │   └── styles/
  ├── tailwind.config.ts
  └── package.json
  ```
- Install dependencies: tailwindcss, react-chartjs-2, firebase, axios
- Set up Tailwind CSS with a color theme (suggest: blues + greens for finance/trust)
- Create basic layout component (navbar, sidebar skeleton)

**Sanskriti (Content + Design):**
- Design wireframes for all 6 pages (hand-drawn or Figma):
  1. Landing/Login page
  2. Onboarding questionnaire (multi-step form)
  3. Dashboard
  4. AI Chat interface
  5. Budget tracker
  6. Simulators page
- Define the color palette, fonts, and overall look
- Start writing content for Learning Module 1: "Budgeting 101"

**End of Day 1 Deliverable:** Both projects run locally. Database has tables. Frontend shows a basic page with Tailwind styling.

---

### Day 2 (Tuesday) -- Authentication

**Prasikshya:**
- Set up Firebase Admin SDK in FastAPI
- Create auth middleware that verifies Firebase tokens
- Create API endpoints:
  - `POST /api/auth/register` -- create user in PostgreSQL after Firebase signup
  - `GET /api/auth/me` -- get current user profile
- Test with Postman/Thunder Client

**Shija:**
- Set up Firebase Auth in Next.js (client-side)
- Build Login page:
  - Email/password login
  - Google login button
  - Clean UI with Tailwind
- Build signup page
- Implement auth context (React Context or Zustand) to manage logged-in state
- Add protected route wrapper (redirect to login if not authenticated)

**Sanskriti:**
- Continue wireframes -- finalize dashboard layout
- Write content for Learning Module 2: "Saving Strategies for Students"
- Research Nepal-specific financial data to feed into AI prompts:
  - Current bank FD rates (NIC Asia, Nabil, Global IME, etc.)
  - Average savings account interest rates
  - Basic tax brackets for individuals
  - Current NRB base rate

**End of Day 2 Deliverable:** User can sign up, log in, and see a protected dashboard page (even if empty).

---

### Day 3-4 (Wednesday-Thursday) -- Onboarding Flow

**Prasikshya:**
- Create API endpoints:
  - `PUT /api/users/profile` -- save onboarding answers
  - `GET /api/users/profile` -- get user profile with onboarding data
- Add validation (age_group must be valid enum, income must be positive, etc.)
- Create a flag `onboarding_completed` in users table

**Shija:**
- Build multi-step onboarding form (4-5 steps):
  - Step 1: "What's your age group?" + "What's your current situation?" (student/employed/etc.)
  - Step 2: "What's your approximate monthly income?" (slider or input in NPR)
  - Step 3: "What are your financial goals?" (multi-select: save, invest, study abroad, start business, emergency fund)
  - Step 4: "How would you describe your risk tolerance?" (low/medium/high with descriptions)
  - Step 5: "How would you rate your financial comfort?" (comfortable/managing/strained)
- Add progress bar at top
- After completion, redirect to dashboard
- Style it clean and friendly -- not intimidating

**Sanskriti:**
- Write the actual text/microcopy for each onboarding step (friendly, conversational tone)
- Write content for Learning Module 3: "Investment Basics -- What Are Your Options in Nepal?"
- Create a document of Nepal-specific financial knowledge for AI system prompt

**End of Day 4 Deliverable:** Complete onboarding flow -- user signs up, completes profile, lands on dashboard.

---

### Day 5-6 (Friday-Saturday) -- Dashboard (Core)

**Prasikshya:**
- Create API endpoints:
  - `GET /api/dashboard` -- returns user's financial snapshot:
    - Profile summary (name, income, goals)
    - Total income vs expenses this month (from transactions)
    - Savings goals progress
    - Recommended next actions based on profile
  - `GET /api/transactions/summary` -- spending breakdown by category

**Shija:**
- Build Dashboard page with sections:
  - **Welcome card:** "Hi [Name], here's your financial snapshot"
  - **Financial health card:** Monthly income vs expenses (simple bar or number)
  - **Goals card:** Progress bars for each savings goal
  - **Quick actions:** Buttons to "Add Expense", "Chat with AI", "Try Simulator", "Learn"
  - **Spending breakdown:** Doughnut/pie chart using Chart.js (even if empty, show placeholder)
- Make it responsive (mobile-friendly even though web-only)

**Sanskriti:**
- Test the full flow so far (signup -> onboarding -> dashboard)
- Document any bugs or UX issues
- Finalize all 3 learning module content drafts
- Compile the Nepal financial data document (bank rates, tax info, investment options)

**End of Day 6 Deliverable:** Dashboard shows personalized content based on onboarding. Charts render (even with placeholder data).

---

### Day 7 (Sunday) -- Week 1 Review + Buffer

- Fix bugs from the week
- Code review across team
- Merge all branches to main
- Deploy current state to Vercel (frontend) + Railway (backend)
- **Milestone check:** Can a user sign up, complete onboarding, and see a personalized dashboard? If yes, Week 1 is done.

---

## WEEK 2: AI CHATBOT + BUDGET TOOL (Days 8-14)
**Goal:** The two most impactful features -- AI financial guide and basic budgeting.

---

### Day 8-10 (Monday-Wednesday) -- AI Chatbot

**Prasikshya:**
- Set up OpenAI API integration via LangChain
- Design the system prompt (CRITICAL -- this defines chatbot quality):
  ```
  You are a friendly financial guide for young adults in Nepal.
  You explain financial concepts in simple, conversational language.
  You use Nepal-specific context (NPR currency, local bank rates,
  NEPSE, Nepal tax rules). You never give direct investment advice
  -- you educate and help users understand their options.
  You know the user's profile: [inject user's onboarding data].
  Always be encouraging, never judgmental about spending habits.
  ```
- Create API endpoints:
  - `POST /api/chat` -- send message, get AI response
  - `GET /api/chat/history` -- get past conversation
- Implement conversation memory (store in chat_history table, send last 10 messages as context)
- Add the Nepal financial data (from Sanskriti's document) into the system prompt or as a knowledge base

**Shija:**
- Build Chat interface page:
  - Message bubbles (user on right, AI on left)
  - Text input at bottom with send button
  - Auto-scroll to latest message
  - Loading indicator while AI responds
  - Suggested starter questions:
    - "How should I start saving as a student?"
    - "Explain mutual funds in simple terms"
    - "How do I plan finances for studying abroad?"
    - "What is a credit score and why does it matter?"
  - Chat history persistence (load previous messages on page open)

**Sanskriti:**
- Write 15-20 "starter questions" covering key topics from the survey:
  - Budgeting basics
  - Saving strategies
  - Investment options in Nepal
  - Loans and interest
  - Credit scores
  - Study abroad financial planning
  - Startup funding basics
  - Tax basics (PAN, VAT)
- Test the chatbot extensively:
  - Ask questions a real user from the survey would ask
  - Note where it gives wrong/generic answers
  - Suggest prompt improvements
- Write better prompt instructions based on testing

**End of Day 10 Deliverable:** User can chat with AI about finances. It responds with Nepal-specific, personalized guidance. Conversation persists.

---

### Day 11-13 (Thursday-Saturday) -- Budget Tool

**Prasikshya:**
- Create API endpoints:
  - `POST /api/transactions` -- add income or expense
  - `GET /api/transactions` -- list transactions (with filters: date range, category, type)
  - `DELETE /api/transactions/{id}` -- delete a transaction
  - `GET /api/transactions/summary` -- spending breakdown by category for a given month
  - `GET /api/transactions/monthly` -- total income vs total expense per month

**Shija:**
- Build Budget page with 3 sections:

  **Section 1: Add Transaction**
  - Quick-add form: Type (income/expense), Amount, Category (dropdown), Date, Description
  - Category icons for visual appeal

  **Section 2: Transaction List**
  - Scrollable list showing recent transactions
  - Color coded: green for income, red for expense
  - Filter by month
  - Delete button on each item

  **Section 3: Spending Analysis**
  - Doughnut chart: spending by category (food, transport, rent, education, entertainment, other)
  - Bar chart: income vs expenses per month (last 3 months)
  - Simple text insight: "You spent 45% of your income on food this month"

**Sanskriti:**
- Add sample transaction data for demo purposes (realistic Nepal amounts):
  - Rent: Rs. 8,000-15,000
  - Food: Rs. 5,000-12,000
  - Transport: Rs. 2,000-4,000
  - Education: Rs. 3,000-10,000
  - Entertainment: Rs. 1,000-5,000
- Test the full budget tool flow
- Ensure Chart.js visualizations look clean and readable

**End of Day 13 Deliverable:** User can add income/expenses, see spending breakdown charts, and get basic spending insights.

---

### Day 14 (Sunday) -- Week 2 Review + Buffer

- Fix bugs from the week
- Deploy updated version
- **Milestone check:** Can a user chat with AI and track their budget? If yes, Week 2 is done.

---

## WEEK 3: SIMULATORS + LEARNING MODULES (Days 15-21)
**Goal:** Financial simulators and educational content.

---

### Day 15-17 (Monday-Wednesday) -- Financial Simulators

**Prasikshya:**
- Create API endpoints (or these can be frontend-only calculations):
  - `POST /api/simulators/savings` -- input: monthly amount, interest rate, years -> output: projected savings with compound interest
  - `POST /api/simulators/loan` -- input: loan amount, interest rate, tenure -> output: EMI, total interest paid, amortization schedule
  - `POST /api/simulators/compare` -- input: amount, duration -> output: side-by-side comparison of FD vs Mutual Fund vs Savings Account

**Shija:**
- Build Simulators page with 3 tabs:

  **Simulator 1: Savings Growth Calculator**
  - Inputs: Monthly savings amount (NPR), Annual interest rate (%), Duration (years)
  - Output: Line chart showing growth over time, Final amount, Total interest earned
  - Pre-filled with Nepal bank rates (e.g., 7% FD, 4% savings)
  - Slider inputs for interactive feel

  **Simulator 2: Loan EMI Calculator**
  - Inputs: Loan amount (NPR), Annual interest rate (%), Loan tenure (years)
  - Output: Monthly EMI amount, Total interest paid, Total amount paid
  - Pie chart: Principal vs Interest breakdown
  - Pre-filled with typical Nepal loan rates (e.g., 12% personal, 9% education)

  **Simulator 3: Investment Comparison Tool**
  - Input: Amount to invest (NPR), Duration (years)
  - Output: Side-by-side bar chart comparing:
    - Savings Account (~4% return)
    - Fixed Deposit (~7-9% return)
    - Mutual Fund (~12-15% estimated return, with risk note)
  - Include disclaimer: "This is a simulation for educational purposes, not investment advice"

**Sanskriti:**
- Research and verify current Nepal financial rates:
  - Top 5 bank FD rates
  - Top 5 bank savings account rates
  - Average mutual fund returns in Nepal (last 3 years)
  - Education loan rates
  - Personal loan rates
  - Home loan rates
- Write helpful tooltips/explanations for each simulator input
- Test simulators with realistic scenarios

**End of Day 17 Deliverable:** All 3 simulators working with interactive charts and Nepal-specific default rates.

---

### Day 18-20 (Thursday-Saturday) -- Learning Modules

**Prasikshya:**
- Create API endpoints:
  - `GET /api/learn/modules` -- list all modules with user's progress
  - `GET /api/learn/modules/{id}` -- get module content (lessons + quiz)
  - `POST /api/learn/modules/{id}/progress` -- update lesson completion
  - `POST /api/learn/modules/{id}/quiz` -- submit quiz answers, get score

**Shija:**
- Build Learn page:
  - Module cards showing: title, description, progress bar, lesson count
  - Module detail page: list of lessons (text + visuals), quiz at the end
  - Completed state: green checkmark, score display
- Design lesson layout:
  - Clean text with headers
  - Key concept boxes (highlighted)
  - Simple illustrations or icons
  - "Did you know?" callout boxes with Nepal-specific facts

**Sanskriti:**
- Finalize content for 3 modules:

  **Module 1: Budgeting 101** (4 lessons + quiz)
  - Lesson 1: Why Budget? (The 76% stat from our survey)
  - Lesson 2: The 50/30/20 Rule (adapted for Nepal incomes)
  - Lesson 3: Tracking Your Money (categories that matter)
  - Lesson 4: Building Your First Budget (step-by-step)
  - Quiz: 5 multiple-choice questions

  **Module 2: Saving Strategies** (4 lessons + quiz)
  - Lesson 1: Why Saving Feels Hard (the psychology)
  - Lesson 2: Pay Yourself First Method
  - Lesson 3: Emergency Fund -- How Much Do You Need?
  - Lesson 4: Best Saving Options in Nepal (FD, recurring deposit, savings accounts)
  - Quiz: 5 questions

  **Module 3: Investment Basics** (4 lessons + quiz)
  - Lesson 1: What is Investing? (Not just for rich people)
  - Lesson 2: Risk vs Return -- Simple Explanation
  - Lesson 3: Your Options in Nepal (FD, Mutual Funds, NEPSE, Gold/Silver)
  - Lesson 4: How to Start with Rs. 1,000/month
  - Quiz: 5 questions

**End of Day 20 Deliverable:** 3 learning modules with content, progress tracking, and quizzes.

---

### Day 21 (Sunday) -- Week 3 Review + Buffer

- Fix bugs
- Deploy updated version
- **Milestone check:** Simulators work? Learning modules display and track progress? If yes, Week 3 is done.

---

## WEEK 4: POLISH, CONNECT, TEST, DEPLOY (Days 22-30)
**Goal:** Make everything work together smoothly. Test. Deploy. Prepare for demo.

---

### Day 22-23 (Monday-Tuesday) -- Connect Everything

**All three members:**
- Dashboard now pulls REAL data:
  - Budget summary from actual transactions
  - Savings goal progress from actual data
  - Learning progress from actual module completion
  - "Recommended for you" based on profile (e.g., if goal is "study abroad," recommend savings simulator + Module 2)
- AI chatbot is now context-aware:
  - Knows user's spending pattern from budget data
  - Can reference their goals
  - Can suggest relevant simulators or learning modules in conversation
- Add navigation between features (e.g., "Try the Savings Simulator" link in chat response)

---

### Day 24-25 (Wednesday-Thursday) -- UI Polish

**Shija (Lead):**
- Consistent styling across all pages
- Loading states (skeleton screens or spinners)
- Empty states (what does dashboard look like with no transactions?)
- Error handling (what happens when API fails? show friendly message)
- Mobile responsiveness (test on phone-sized screen)
- Add the app name/logo to navbar
- Landing page for logged-out users (what is this platform? why use it?)

**Sanskriti:**
- Review ALL text content across the app for:
  - Spelling/grammar
  - Tone consistency (friendly, non-judgmental)
  - Nepal-specific accuracy
- Write the landing page copy

**Prasikshya:**
- API error handling (proper HTTP status codes, error messages)
- Input validation on all endpoints
- Make sure no API endpoint is exposed without auth middleware

---

### Day 26-27 (Friday-Saturday) -- Testing

**Sanskriti (Lead tester):**
- Test complete user journey:
  1. Land on site -> Sign up -> Onboard -> Dashboard
  2. Add 10+ transactions -> Check spending charts
  3. Chat with AI about 5 different topics -> Check relevance
  4. Use all 3 simulators with different inputs
  5. Complete 1 learning module + quiz
  6. Check dashboard reflects all activity
- Test edge cases:
  - Empty states (new user, no transactions)
  - Very large numbers in simulators
  - Very long chat messages
  - Wrong inputs (negative amounts, empty fields)
- Document all bugs in a shared list

**Prasikshya + Shija:**
- Fix bugs from testing
- Performance check (are pages loading fast enough?)

---

### Day 28 (Sunday) -- Final Deployment

**Prasikshya:**
- Deploy backend to Railway/Render (production)
  - Set environment variables (DB URL, API keys, Firebase config)
  - Enable HTTPS
  - Test all API endpoints on production URL
- Set up PostgreSQL on Railway (production database)

**Shija:**
- Deploy frontend to Vercel (production)
  - Connect to production backend URL
  - Set environment variables
  - Test full flow on production

**All:**
- Smoke test on production: sign up a new account, go through entire flow
- Fix any production-specific bugs

---

### Day 29-30 (Monday-Tuesday) -- Demo Preparation

**All three members:**
- Create a demo account pre-loaded with:
  - Realistic onboarding data (22-year-old student, Rs. 15,000/month income)
  - 30+ sample transactions across categories
  - 1 savings goal ("Study Abroad Fund -- Rs. 5,00,000 target")
  - Some chat history showing good AI responses
  - 1 completed learning module
- Practice the demo walkthrough (5-7 minutes):
  1. Show landing page and sign up (30 sec)
  2. Walk through onboarding (1 min)
  3. Show personalized dashboard (1 min)
  4. Demo AI chatbot -- ask it about saving for abroad (1.5 min)
  5. Show budget tool with spending breakdown (1 min)
  6. Demo savings simulator (1 min)
  7. Show learning module (30 sec)
- Prepare backup screenshots in case of live demo failure
- Write a 1-page "README" for the project

---

## DAILY STANDUP FORMAT

Every day, 15 minutes at a fixed time (suggest: 9 AM or 8 PM):
1. What did I complete yesterday?
2. What am I working on today?
3. Am I blocked on anything?

Use a WhatsApp group or Discord channel for async communication.

---

## RISK MANAGEMENT

| Risk | Likelihood | Mitigation |
|---|---|---|
| OpenAI API costs too high | Medium | Use `gpt-3.5-turbo` for prototype (cheap). Set max token limits. Budget ~$10-20 for the month. |
| Team member falls behind | Medium | Weekly buffer days (Day 7, 14, 21). Other members help catch up. |
| Database issues | Low | Use Railway's managed PostgreSQL. Keep local SQLite as backup option. |
| Deployment fails | Medium | Deploy early (end of Week 1). Don't wait until Day 28. |
| AI gives wrong financial info | High | Add disclaimers. Strong system prompt. Sanskriti reviews responses. Never claim to be financial advice. |
| Feature creep | High | Stick to this plan. Say "that's for V2" to any new ideas during the month. |

---

## COST ESTIMATE (Prototype Month)

| Item | Cost | Notes |
|---|---|---|
| OpenAI API (gpt-3.5-turbo) | ~$10-20 | ~500-1000 conversations during development + testing |
| Railway (backend hosting) | $0-5 | Free tier usually sufficient for prototype |
| Vercel (frontend hosting) | $0 | Free tier for hobby projects |
| Railway PostgreSQL | $0-5 | Free tier: 500 MB storage |
| Firebase Auth | $0 | Free for up to 10K auth/month |
| Domain name (optional) | $10-15 | Optional: buy a .com for demo credibility |
| **Total** | **~$20-45** | |

---

## SUCCESS CRITERIA (End of Month)

The prototype is "done" when a user can:
- [ ] Sign up and complete financial profile onboarding
- [ ] See a personalized dashboard with financial snapshot
- [ ] Chat with an AI that gives Nepal-specific financial guidance
- [ ] Add income/expenses and see spending breakdown charts
- [ ] Use savings calculator to project future savings
- [ ] Use loan EMI calculator to understand loan costs
- [ ] Compare investment options side-by-side
- [ ] Complete a learning module and take a quiz
- [ ] All of the above works on a live, deployed URL

---

## POST-PROTOTYPE ROADMAP (V2 -- After Month 1)

Once prototype is validated:
1. Flutter mobile app
2. Redis caching for performance
3. Gamification (badges, streaks, daily tips)
4. Nepali language support
5. Bank API integrations (if available)
6. Custom AI fine-tuning on Nepal financial corpus
7. More learning modules (10+)
8. Goal-based financial roadmaps (multi-month plans)
9. Community features (peer financial discussions)
10. Institutional partnerships (colleges, banks)
