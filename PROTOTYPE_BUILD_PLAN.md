# 1-Month Prototype Build Plan
## Team Capybara - AI-Powered Financial Literacy & Recommendation Platform

---

# WHAT THIS PLATFORM ACTUALLY IS

This is NOT a calculator/simulator app. This is a **data-driven recommendation system** that:
1. Collects financial data from users (profile, income, expenses, goals, knowledge level)
2. Analyzes their financial behavior and patterns
3. Generates personalized financial recommendations based on that data
4. Educates them through an AI guide that knows their specific situation
5. Tracks their progress over time and adjusts recommendations

Think of it as: **a financial advisor that learns about you, not a calculator you punch numbers into.**

---

# DATA COLLECTION STRATEGY

This is the foundation of everything. Without data, there are no recommendations.

## A. What Data We Collect from Users

### 1. Profile Data (collected once during onboarding)

| Data Point | Why We Need It | How It Feeds Recommendations |
|---|---|---|
| Age group (18-21, 22-25, 26-30) | Different age = different financial stage | A 19-year-old student gets different advice than a 25-year-old worker |
| Occupation (student, employed, entrepreneur) | Determines income stability and type | Students get budgeting-first advice; entrepreneurs get cash-flow advice |
| Monthly income (NPR) | Core input for all budget recommendations | Can't recommend saving Rs. 10,000/month if they earn Rs. 15,000 |
| Income sources (salary, allowance, freelance, family) | Stability assessment | Irregular income needs different budgeting approach |
| Financial goals (multi-select: save, invest, study abroad, start business, emergency fund, buy asset) | Drives goal-specific recommendations | Study abroad goal triggers savings roadmap; startup goal triggers loan readiness path |
| Risk tolerance (low, medium, high) | Shapes investment recommendations | Low risk = FD/savings; High risk = mutual fund/NEPSE education |
| Financial comfort (comfortable, managing, strained, very strained) | Urgency assessment | "Strained" users get survival budgeting first, not investment advice |
| Current financial knowledge (5-question mini quiz) | Baseline literacy score | Low score = start with basics; High score = skip to advanced topics |

### 2. Financial Behavior Data (collected ongoing -- this is the core)

| Data Point | How User Enters It | What It Reveals |
|---|---|---|
| Income entries (amount, source, date) | Manual entry form | Total monthly income, income regularity |
| Expense entries (amount, category, date, description) | Manual entry form | Spending patterns, category breakdown, problem areas |
| Expense categories: food, transport, rent, education, entertainment, health, personal, subscriptions, other | Dropdown selection | Where money actually goes vs where they think it goes |
| Savings deposits (amount, where saved, date) | Manual entry | Are they actually saving? How much? Where? |
| Debt/loan entries (amount owed, interest rate, monthly payment) | Optional form | Debt-to-income ratio, urgency of debt recommendations |

### 3. Goal Tracking Data (collected when user sets goals)

| Data Point | Purpose |
|---|---|
| Goal name ("Study abroad fund", "Emergency fund") | Personalization |
| Target amount (NPR) | Calculate monthly savings needed |
| Deadline (date) | Calculate timeline and feasibility |
| Current saved amount | Track progress, adjust recommendations |

### 4. Knowledge & Engagement Data (collected passively)

| Data Point | How Collected | Purpose |
|---|---|---|
| Quiz scores per module | After each learning quiz | Identify knowledge gaps, recommend relevant modules |
| Modules completed / in-progress | Auto-tracked | Learning progress, what they know vs don't |
| Chat questions asked | From chat history | What topics confuse them, what they search for |
| Features used most | Page visit tracking | What they care about most |
| Weekly check-in responses | Weekly prompt | Emotional state, recent wins/struggles |

### 5. Weekly Financial Check-in (prompted once per week)

Short 3-question check-in:
1. "Did you save anything this week?" (Yes/No + amount)
2. "What was your biggest unexpected expense?" (dropdown + amount)
3. "How are you feeling about money this week?" (emoji scale: great / okay / stressed / overwhelmed)

This builds longitudinal data to track emotional and behavioral change over time.

## B. Nepal-Specific Financial Data We Need to Research and Compile

This is static reference data that powers recommendations and AI responses. Sanskriti compiles this during Week 1.

| Data Category | Specific Data Points | Source |
|---|---|---|
| **Bank Savings Rates** | Interest rates from top 10 Nepal banks (savings account) | Bank websites, NRB |
| **FD Rates** | Fixed deposit rates by duration (3mo, 6mo, 1yr, 2yr) for top banks | Bank websites |
| **Loan Rates** | Personal loan, education loan, home loan, business loan interest rates | Bank websites, NRB |
| **Mutual Funds** | List of available mutual funds in Nepal, historical returns, minimum investment | NEPSE, fund websites |
| **Tax Info** | Income tax brackets for individuals, PAN registration process | IRD Nepal |
| **Study Abroad Costs** | Estimated yearly costs for top destinations (Australia, US, UK, Japan, Korea) -- tuition + living | Consultancy data, public sources |
| **Average Living Costs** | Rent, food, transport averages in Kathmandu Valley for students and young workers | Public surveys, CBS |
| **NRB Policies** | Key financial inclusion policies, digital payment regulations | NRB website |
| **Insurance Basics** | Types of insurance available, basic premium ranges | Insurance company websites |

Store all this as structured JSON files in the codebase. Update manually as needed.

## C. How Data Flows into Recommendations

```
User signs up
    → Onboarding data collected
        → INITIAL RECOMMENDATIONS generated (based on profile alone)
            "Based on your profile, here's your starting plan..."

User adds income + expenses for 1-2 weeks
    → Spending pattern analyzed
        → BUDGET RECOMMENDATIONS generated
            "You're spending 55% on food. Here's how to bring it to 40%..."

User sets a financial goal
    → Goal + income + current savings analyzed
        → GOAL RECOMMENDATIONS generated
            "To save Rs. 5L in 2 years, you need Rs. 20,833/month.
             Currently you save Rs. 3,000/month. Here's a plan to close the gap..."

User completes quiz with low score on investments
    → Knowledge gap identified
        → LEARNING RECOMMENDATIONS generated
            "We noticed you're unsure about mutual funds.
             Module 3: Investment Basics would help."

User chats with AI
    → AI has access to ALL of the above data
        → CONTEXTUAL RESPONSES
            Not generic "here's what investing is" but
            "Given that you earn Rs. 20,000 and want to start investing,
             here's what makes sense for YOU..."
```

---

# THE RECOMMENDATION ENGINE (Rule-Based for Prototype)

We don't need machine learning for the prototype. We use **rule-based logic** -- if/then conditions based on user data.

## Recommendation Categories

### 1. Budget Recommendations

```
Rules:
- Calculate % of income spent per category
- Compare to Nepal-adapted 50/30/20 benchmark:
    50% needs (food, rent, transport, education)
    30% wants (entertainment, personal, subscriptions)
    20% savings/investments
- Flag any category > 30% of income
- Flag if total expenses > 90% of income (danger zone)
- Flag if savings < 10% of income

Example outputs:
- "You spent 52% of your income on food this month. The recommended max is 30%.
   Try meal prepping to save Rs. 3,000 next month."
- "Your total expenses are 95% of your income. You're in the danger zone.
   Let's find Rs. 2,000 to cut."
- "Great job! You saved 15% of your income this month. You're above average."
```

### 2. Savings Recommendations

```
Rules:
- IF user has no emergency fund → recommend building 3-month emergency fund first
- IF user has a goal with a deadline →
    calculate: (target - current_saved) / months_remaining = monthly_needed
    compare monthly_needed to actual monthly savings
    IF monthly_needed > 50% of income → flag goal as "ambitious, consider extending deadline"
    IF monthly_needed < current savings → "You're on track!"
    IF monthly_needed > current savings → "You need to save Rs. X more per month"
- Recommend specific saving instruments based on timeline:
    < 6 months: savings account
    6 months - 2 years: recurring deposit or short-term FD
    > 2 years: FD or mutual fund (based on risk tolerance)

Example outputs:
- "You don't have an emergency fund yet. Before investing, save Rs. 30,000
   (3 months of basic expenses) in a savings account."
- "To reach your Study Abroad goal of Rs. 5,00,000 by Jan 2028, you need to save
   Rs. 20,833/month. You're currently saving Rs. 5,000/month. Let's look at ways
   to close this Rs. 15,833 gap."
```

### 3. Investment Recommendations (Educational, Not Advice)

```
Rules:
- Only show investment recommendations IF:
    user has emergency fund OR savings > 3 months expenses
    user has expressed interest in investing (goal or chat)
- Based on risk tolerance + monthly surplus:
    LOW risk  → "Consider: 80% FD (currently ~8% at [Bank]) + 20% recurring deposit"
    MED risk  → "Consider: 50% FD + 30% mutual fund ([Fund name], avg return ~12%) + 20% savings"
    HIGH risk → "Consider: 40% mutual fund + 30% NEPSE (start with blue chips) + 30% FD as safety"
- Always include disclaimer: "This is educational guidance, not financial advice."
- Include Nepal-specific details: actual bank names, actual rates, actual fund names

Example outputs:
- "Based on your medium risk tolerance and Rs. 5,000 monthly surplus:
   Consider splitting: Rs. 2,500 into NIC Asia FD (8.5% for 1yr),
   Rs. 1,500 into Siddhartha Equity Fund (avg 12.4% return),
   Rs. 1,000 into savings for liquidity."
```

### 4. Learning Recommendations

```
Rules:
- IF onboarding quiz score < 3/5 → start with Module 1 (Budgeting 101)
- IF user struggles with specific topic (from quiz or chat) → recommend that module
- IF user goal = study abroad AND hasn't completed Module 4 → recommend it
- IF user goal = start business AND hasn't completed Module 5 → recommend it
- IF user completed all modules → recommend advanced chat discussions

Example outputs:
- "Your quiz showed you're unsure about how interest works.
   Module 2: Saving Strategies covers this -- takes about 10 minutes."
- "Since you're planning to study abroad, Module 4: Financial Planning for Abroad
   has a step-by-step preparation checklist."
```

### 5. Weekly Action Items (personalized to-do list)

```
Rules:
- Generate 3-5 specific actions based on current data:
    IF no expenses logged this week → "Log your expenses from this week (takes 5 min)"
    IF savings goal behind schedule → "Transfer Rs. X to savings this week"
    IF unfinished learning module → "Complete Lesson 3 of Budgeting 101"
    IF high food spending last week → "Try cooking at home 2 extra days this week"
    IF weekly check-in missed → "Do your weekly financial check-in"

Example output:
"Your actions for this week:
 1. Log your expenses (you haven't logged since Tuesday)
 2. Transfer Rs. 3,000 to your Study Abroad savings
 3. Finish Lesson 2 of Investment Basics
 4. Your food spending was Rs. 4,200 last week -- try Rs. 3,500 this week"
```

---

# 100% FREE TECH STACK

Every single tool here is free. No credit card required anywhere.

| Layer | Technology | Free Tier Limits | Why This One |
|---|---|---|---|
| **Frontend** | Next.js 14 + Tailwind CSS | Unlimited (open source) | Industry standard, fast, great developer experience |
| **Backend + Database + Auth** | Supabase | 500 MB database, 50K monthly active users, unlimited API requests, built-in auth | Replaces PostgreSQL + Firebase Auth + REST API in ONE free service |
| **AI Chatbot** | Google Gemini 1.5 Flash API | 15 requests/minute, 1 million tokens/day, completely free | Most generous free AI API available, no credit card needed |
| **Charts** | Chart.js (react-chartjs-2) | Unlimited (open source) | Simple, well-documented, good looking charts |
| **Hosting (Frontend)** | Vercel | 100 GB bandwidth/month, serverless functions included | Built for Next.js, automatic deployments from GitHub |
| **Hosting (Backend)** | Supabase Edge Functions OR Next.js API routes on Vercel | Included in Supabase free / Vercel free | No separate backend server needed |
| **Version Control** | GitHub | Unlimited (free for public/private repos) | Already using it |
| **Design** | Figma | Free for up to 3 projects | For wireframes and UI design |
| **AI Prompt Development** | Google AI Studio | Free | Test and refine Gemini prompts before coding |

### Why This Stack Saves Money AND Complexity

**Old plan had:** Next.js + FastAPI + PostgreSQL + Firebase Auth + OpenAI API + Railway + Vercel = 7 separate services, some paid.

**New plan has:** Next.js + Supabase + Gemini API + Vercel = 4 services, ALL free.

**Supabase alone replaces 3 things:**
- PostgreSQL database (was going to be Railway -- paid)
- Firebase Auth (was free but separate setup)
- REST API (auto-generated from database -- less FastAPI code needed)

**Gemini 1.5 Flash replaces OpenAI:**
- OpenAI free tier: barely exists (rate limited, needs credit card)
- Gemini free tier: 1 million tokens/day, no credit card. That's ~500+ conversations/day.

**Next.js API routes replace FastAPI for most things:**
- Recommendation engine logic can run as Next.js API routes (serverless functions on Vercel)
- Only complex backend logic (if any) goes to Supabase Edge Functions
- Result: no separate backend server to manage or deploy

### Total Cost: Rs. 0

| Item | Cost |
|---|---|
| Supabase (database + auth) | Free |
| Vercel (frontend + API routes) | Free |
| Google Gemini API | Free |
| Chart.js | Free (open source) |
| GitHub | Free |
| Domain name | Not needed for prototype (use .vercel.app URL) |
| **Total** | **Rs. 0** |

---

# SIMPLIFIED PROJECT STRUCTURE

Since we're using Next.js API routes + Supabase (no separate FastAPI backend), the project is ONE codebase:

```
project/
├── src/
│   ├── app/                          # Next.js pages
│   │   ├── layout.tsx                # Root layout (navbar, sidebar)
│   │   ├── page.tsx                  # Landing page
│   │   ├── login/page.tsx            # Login
│   │   ├── signup/page.tsx           # Signup
│   │   ├── onboarding/page.tsx       # Multi-step onboarding
│   │   ├── dashboard/page.tsx        # Main dashboard with recommendations
│   │   ├── budget/page.tsx           # Income/expense tracker
│   │   ├── goals/page.tsx            # Savings goals
│   │   ├── chat/page.tsx             # AI financial guide
│   │   ├── learn/page.tsx            # Learning modules list
│   │   ├── learn/[id]/page.tsx       # Individual module
│   │   └── checkin/page.tsx          # Weekly check-in
│   │
│   ├── app/api/                      # Next.js API routes (backend logic)
│   │   ├── recommendations/route.ts  # Recommendation engine
│   │   ├── chat/route.ts             # Gemini AI chat
│   │   ├── transactions/route.ts     # Budget CRUD
│   │   ├── goals/route.ts            # Goals CRUD
│   │   ├── checkin/route.ts          # Weekly check-in
│   │   └── quiz/route.ts             # Quiz scoring
│   │
│   ├── lib/
│   │   ├── supabase.ts               # Supabase client setup
│   │   ├── gemini.ts                 # Gemini API setup
│   │   ├── recommendations.ts        # Recommendation rules engine
│   │   └── nepal-data.ts             # Nepal financial reference data
│   │
│   ├── components/                   # Reusable UI components
│   │   ├── Navbar.tsx
│   │   ├── TransactionForm.tsx
│   │   ├── SpendingChart.tsx
│   │   ├── RecommendationCard.tsx
│   │   ├── GoalProgressBar.tsx
│   │   ├── ChatBubble.tsx
│   │   ├── QuizQuestion.tsx
│   │   └── WeeklyCheckin.tsx
│   │
│   └── data/
│       ├── nepal-banks.json           # Bank rates data
│       ├── nepal-investments.json     # Investment options data
│       ├── nepal-loans.json           # Loan rates data
│       ├── nepal-abroad-costs.json    # Study abroad cost data
│       ├── modules.json              # Learning module content
│       └── quiz-questions.json       # Quiz question bank
│
├── supabase/
│   └── migrations/                   # Database schema
│
├── tailwind.config.ts
├── package.json
├── .env.local                        # API keys (Supabase URL, Gemini key)
└── README.md
```

---

# DATABASE SCHEMA (Supabase / PostgreSQL)

```sql
-- Users table (extended by Supabase Auth automatically)
CREATE TABLE profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id),
    name TEXT,
    age_group TEXT CHECK (age_group IN ('18-21', '22-25', '26-30')),
    occupation TEXT CHECK (occupation IN ('student', 'employed_full', 'employed_part', 'entrepreneur', 'not_working')),
    monthly_income INTEGER DEFAULT 0,
    income_sources TEXT[],
    financial_goals TEXT[],
    risk_tolerance TEXT CHECK (risk_tolerance IN ('low', 'medium', 'high')),
    financial_comfort TEXT CHECK (financial_comfort IN ('comfortable', 'managing', 'strained', 'very_strained')),
    initial_quiz_score INTEGER DEFAULT 0,
    onboarding_completed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Income and expense tracking
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    type TEXT CHECK (type IN ('income', 'expense', 'savings')),
    category TEXT,
    amount INTEGER NOT NULL,
    description TEXT,
    date DATE NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Financial goals
CREATE TABLE goals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    name TEXT NOT NULL,
    target_amount INTEGER NOT NULL,
    current_amount INTEGER DEFAULT 0,
    deadline DATE,
    status TEXT DEFAULT 'active' CHECK (status IN ('active', 'achieved', 'paused')),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- AI chat history
CREATE TABLE chat_messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    role TEXT CHECK (role IN ('user', 'assistant')),
    content TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Learning progress
CREATE TABLE learning_progress (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    module_id TEXT NOT NULL,
    completed_lessons INTEGER DEFAULT 0,
    total_lessons INTEGER NOT NULL,
    quiz_score INTEGER,
    status TEXT DEFAULT 'not_started' CHECK (status IN ('not_started', 'in_progress', 'completed')),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE(user_id, module_id)
);

-- Weekly check-ins
CREATE TABLE weekly_checkins (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    saved_this_week BOOLEAN,
    amount_saved INTEGER DEFAULT 0,
    biggest_expense_category TEXT,
    biggest_expense_amount INTEGER DEFAULT 0,
    mood TEXT CHECK (mood IN ('great', 'okay', 'stressed', 'overwhelmed')),
    week_start DATE NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Generated recommendations (stored so we can track what was shown)
CREATE TABLE recommendations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    category TEXT CHECK (category IN ('budget', 'savings', 'investment', 'learning', 'action')),
    title TEXT NOT NULL,
    description TEXT NOT NULL,
    priority INTEGER DEFAULT 3,
    is_dismissed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

# TEAM ROLES (Updated)

| Member | Primary Role | What They Own |
|---|---|---|
| **Prasikshya** (Lead) | Full-stack development + AI integration | Supabase setup, API routes, recommendation engine logic, Gemini integration, deployment |
| **Shija** | Frontend development + UI/UX | All pages, components, charts, responsive design, user experience |
| **Sanskriti** | Data research + Content + Testing | Nepal financial data compilation, learning module content, AI prompt writing, QA testing |

---

# WEEK-BY-WEEK PLAN

---

## WEEK 1: FOUNDATION + DATA COLLECTION STARTS (Days 1-7)
**Goal:** Users can sign up, complete onboarding (first data collection), and see an empty dashboard. Nepal data compiled.

---

### Day 1 -- Project Setup

**Prasikshya:**
- Create Next.js 14 project with TypeScript + Tailwind CSS
- Create Supabase project (supabase.com -- sign up, create project)
- Set up Supabase client in Next.js (`lib/supabase.ts`)
- Create all database tables (run the SQL schema above in Supabase SQL editor)
- Set up `.env.local` with Supabase URL + anon key
- Get Google Gemini API key from Google AI Studio (ai.google.dev -- free, no credit card)
- Push initial project to GitHub

**Shija:**
- Set up Tailwind config with color theme:
  - Primary: blue (trust, finance)
  - Secondary: green (growth, money)
  - Accent: amber (alerts, attention)
- Create root layout with responsive navbar
- Create landing page (simple: headline, 3 value props, login button)
- Install dependencies: `react-chartjs-2`, `chart.js`, `@supabase/supabase-js`, `@google/generative-ai`

**Sanskriti:**
- Start compiling Nepal financial data:
  - Visit NIC Asia, Nabil, Global IME, Sanima, Siddhartha bank websites
  - Record current savings account rates, FD rates (3mo, 6mo, 1yr)
  - Record personal loan, education loan, home loan rates
- Start wireframes for: login, onboarding, dashboard (hand-drawn or Figma)

**End of Day 1:** Project runs locally at localhost:3000, Supabase database has all tables, API keys ready.

---

### Day 2 -- Authentication

**Prasikshya:**
- Set up Supabase Auth (email + Google OAuth)
- Configure Google OAuth in Supabase dashboard (create Google Cloud OAuth credentials -- free)
- Create auth helper functions: signUp, signIn, signOut, getUser
- Set up auth middleware for API routes (check session)

**Shija:**
- Build login page: email/password + Google login button
- Build signup page: name, email, password
- Create auth context provider (wrap app, provide user state)
- Add protected route logic: if not logged in, redirect to /login
- After signup, redirect to /onboarding

**Sanskriti:**
- Continue Nepal data: mutual fund options (names, minimum investments, historical returns)
- Research tax brackets, PAN registration process
- Research study abroad average costs (Australia, US, Japan, Korea)
- Start structuring data as JSON files

**End of Day 2:** Users can sign up and log in. Protected routes work.

---

### Day 3-4 -- Onboarding (First Data Collection)

**Prasikshya:**
- Create API route: `POST /api/onboarding` -- saves profile data to Supabase `profiles` table
- Create API route: `GET /api/profile` -- fetches user profile
- Create the 5-question mini financial literacy quiz:
  1. "If you put Rs. 10,000 in a savings account at 5% yearly interest, how much will you have after 1 year?" (Rs. 10,500 / Rs. 15,000 / Don't know)
  2. "What does 'inflation' mean for your money?" (It grows / It loses value / No effect / Don't know)
  3. "Which is generally riskier: a fixed deposit or a mutual fund?" (FD / Mutual fund / Same / Don't know)
  4. "What is an EMI?" (One-time payment / Monthly loan installment / A type of investment / Don't know)
  5. "If you have a loan at 12% interest and savings earning 5%, which should you focus on first?" (Savings / Loan repayment / Both equally / Don't know)
- Score quiz and store in `initial_quiz_score`

**Shija:**
- Build multi-step onboarding page (6 steps with progress bar):
  - Step 1: "Tell us about yourself" -- age group + occupation
  - Step 2: "Your income" -- monthly income (NPR input) + income sources (multi-select)
  - Step 3: "Your financial goals" -- multi-select cards (save, invest, study abroad, start business, emergency fund, buy asset)
  - Step 4: "Your risk comfort" -- low/medium/high with simple explanations:
    - Low: "I prefer safety. I don't want to lose money."
    - Medium: "I'm okay with some risk for better returns."
    - High: "I'm willing to take risks for higher potential growth."
  - Step 5: "How are you doing financially?" -- comfortable/managing/strained/very strained
  - Step 6: Quick quiz (5 questions above) -- fun, not intimidating ("Let's see where you stand!")
- Smooth animations between steps, mobile-friendly

**Sanskriti:**
- Write all onboarding microcopy (friendly, conversational, non-judgmental)
- Finalize `nepal-banks.json` with rates from 8-10 banks
- Finalize `nepal-loans.json` with loan rate data
- Start writing Learning Module 1 content: "Budgeting 101"

**End of Day 4:** Full onboarding flow works. Profile data + quiz score saved to database. This is our first real user data.

---

### Day 5-6 -- Dashboard + Initial Recommendations

**Prasikshya:**
- Create API route: `GET /api/recommendations` -- the recommendation engine:
  - Takes user profile from database
  - Applies rule-based logic to generate initial recommendations:
    - IF quiz_score < 3 → recommend "Start with Budgeting 101 module"
    - IF financial_comfort = strained → recommend "Let's build a basic budget first"
    - IF goal includes study_abroad → recommend "Study abroad planning: here's your savings target"
    - IF goal includes invest AND risk = low → recommend "Safest investment options in Nepal"
    - IF no transactions yet → recommend "Start tracking your expenses this week"
  - Returns array of recommendation objects: { category, title, description, priority }
- Create API route: `GET /api/dashboard` -- aggregates:
  - Profile summary
  - Transaction summary (empty for new users -- show prompts to add data)
  - Goal progress (empty for new users -- show prompt to set first goal)
  - Top 3-5 recommendations

**Shija:**
- Build dashboard page with sections:
  - **Welcome banner:** "Hi [Name], here's your financial overview"
  - **Recommendation cards** (most important section):
    - Cards with icon, title, description, action button
    - Color-coded by category (budget=blue, savings=green, investment=purple, learning=amber)
    - Example: "Start tracking expenses → Go to Budget Tool"
  - **Financial snapshot** (shows data once user adds transactions):
    - Income vs Expenses this month (bar chart or just numbers)
    - Spending breakdown (doughnut chart)
    - If no data yet: friendly empty state "Add your first expense to see your spending breakdown"
  - **Goals progress** (shows once user sets goals):
    - Progress bars for each goal
    - If no goals: "Set your first financial goal →"
  - **Quick actions row:** "Add Expense" | "Chat with AI" | "Set Goal" | "Learn"

**Sanskriti:**
- Write recommendation text for ALL initial rules (make them specific, actionable, friendly)
- Test full flow: signup → onboarding → dashboard with recommendations
- Continue writing Module 2: "Saving Strategies for Students"

**End of Day 6:** Dashboard shows personalized recommendations based on onboarding data. Empty states guide users to add more data.

---

### Day 7 -- Week 1 Buffer + First Deploy

- Fix all bugs
- Deploy to Vercel (connect GitHub repo, automatic deployment)
- Set environment variables on Vercel (Supabase URL, keys, Gemini key)
- Test on production URL
- **Milestone:** User can sign up → onboard → see personalized dashboard with recommendations

---

## WEEK 2: BUDGET TRACKER + AI CHAT (Days 8-14)
**Goal:** Core data collection (transactions) working. AI chat powered by user data.

---

### Day 8-9 -- Budget Tracker (Core Data Collection)

**Prasikshya:**
- Create API routes:
  - `POST /api/transactions` -- add income/expense/savings entry
  - `GET /api/transactions` -- list with filters (month, category, type)
  - `DELETE /api/transactions/[id]` -- delete entry
  - `GET /api/transactions/summary` -- returns:
    - Total income, total expenses, total savings for selected month
    - Breakdown by category: { food: 5200, transport: 1800, rent: 10000, ... }
    - Percentage of income per category
    - Month-over-month comparison (if data exists)

**Shija:**
- Build budget page:
  - **Quick-add form** at top:
    - Toggle: Income / Expense / Savings
    - Amount input (NPR)
    - Category dropdown (different options per type):
      - Income: salary, allowance, freelance, family support, other
      - Expense: food, transport, rent, education, entertainment, health, personal, subscriptions, other
      - Savings: bank savings, FD deposit, investment, other
    - Date picker (defaults to today)
    - Optional description
    - "Add" button
  - **Transaction list:**
    - Grouped by date
    - Color: green (income), red (expense), blue (savings)
    - Swipe/click to delete
    - Monthly filter at top
  - **Spending analysis section:**
    - Doughnut chart: expenses by category
    - Horizontal bar chart: each category as % of income
    - Summary text: "Total Income: Rs. XX | Total Expenses: Rs. XX | Saved: Rs. XX"

**Sanskriti:**
- Create sample transaction data set (for demo and testing):
  - 1 month of realistic data for a student earning Rs. 15,000/month
  - 1 month of realistic data for a part-time worker earning Rs. 25,000/month
- Test budget tracker thoroughly
- Write Module 3: "Investment Basics -- Your Options in Nepal"

**End of Day 9:** Users can log income and expenses. Charts show spending patterns. This is the CORE data that powers recommendations.

---

### Day 10 -- Upgrade Recommendations with Transaction Data

**Prasikshya:**
- Upgrade `/api/recommendations` to use transaction data:
  - Calculate spending ratios:
    ```
    food_pct = food_spending / total_income * 100
    wants_pct = (entertainment + personal + subscriptions) / total_income * 100
    savings_pct = total_savings / total_income * 100
    ```
  - New recommendation rules:
    - IF food_pct > 35% → "Your food spending is [X]% of income. Try meal planning to cut it by 10%."
    - IF wants_pct > 30% → "Entertainment + personal spending is [X]% of income. Consider the 50/30/20 rule."
    - IF savings_pct < 10% → "You're saving [X]% of income. Even Rs. 500 more per month adds up."
    - IF savings_pct > 20% → "You're saving [X]% -- that's better than 97% of people we surveyed."
    - IF total_expenses > total_income → "You spent more than you earned this month. Let's find where to cut."
    - IF no transactions in last 7 days → "Keep logging! Consistent tracking is key to financial clarity."

**Shija:**
- Update dashboard to show live data from budget:
  - Real spending chart (not placeholder)
  - Real recommendation cards based on actual spending
- Add "insight" text below charts: "You spent 45% on food -- the average in our users is 32%"

**Sanskriti:**
- Write all the new recommendation texts (specific, actionable, friendly)
- Test recommendations with both sample data sets
- Verify numbers and percentages are calculated correctly

**End of Day 10:** Recommendations are now DATA-DRIVEN -- they change based on actual spending behavior.

---

### Day 11-13 -- AI Chat (Powered by User Data)

**Prasikshya:**
- Set up Gemini API integration (`lib/gemini.ts`):
  ```typescript
  import { GoogleGenerativeAI } from "@google/generative-ai";
  const genAI = new GoogleGenerativeAI(process.env.GEMINI_API_KEY);
  const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });
  ```
- Create API route: `POST /api/chat`
  - Fetch user's profile, recent transactions summary, goals, quiz score from Supabase
  - Build system prompt that includes ALL user context:
    ```
    You are a friendly financial guide for young adults in Nepal.

    ABOUT THIS USER:
    - Age: [age_group], Occupation: [occupation]
    - Monthly income: Rs. [income]
    - Financial goals: [goals]
    - Risk tolerance: [risk_tolerance]
    - Financial comfort: [comfort]
    - Financial knowledge score: [quiz_score]/5
    - This month's spending: Food Rs.[X], Transport Rs.[X], Rent Rs.[X]...
    - Total saved this month: Rs. [X]
    - Active goals: [goal_name] -- Rs.[current]/Rs.[target] by [deadline]

    RULES:
    - Explain concepts in simple, conversational language
    - Use Nepal-specific context (NPR, local banks, NEPSE, Nepal tax rules)
    - Reference the user's actual data when relevant
    - Never give direct investment advice -- educate and explain options
    - Be encouraging, never judgmental
    - If asked about specific bank rates, use this data: [nepal-banks.json]
    - Keep responses concise (under 200 words unless user asks for detail)
    ```
  - Store messages in `chat_messages` table
  - Send last 15 messages as conversation history for context
- Create API route: `GET /api/chat/history` -- load past messages

**Shija:**
- Build chat page:
  - Clean chat interface (WhatsApp/iMessage style)
  - User messages on right (blue), AI messages on left (gray)
  - Text input with send button at bottom
  - Auto-scroll to latest message
  - Typing indicator while AI responds ("Thinking...")
  - Load previous messages on page open
  - Suggested starter questions as chips at top:
    - "How should I budget on Rs. 15,000/month?"
    - "What's the safest way to start investing?"
    - "Help me plan for studying abroad"
    - "Explain credit scores simply"
    - "How do I apply for a bank loan?"
    - "Where is my money going this month?" (triggers data-aware response)

**Sanskriti:**
- Test chatbot with 30+ different questions covering:
  - Budgeting help
  - Saving advice
  - Investment questions
  - Loan/credit questions
  - Study abroad planning
  - Startup funding
  - Tax basics
  - Nepal-specific questions ("Which bank has the best FD rate?")
- Document where AI gives bad/generic answers
- Refine system prompt based on testing
- Write better instructions for edge cases

**End of Day 13:** AI chat works with full user context. It knows their income, spending, goals, and knowledge level. Responses are Nepal-specific and personalized.

---

### Day 14 -- Week 2 Buffer + Deploy

- Fix bugs
- Deploy updated version
- Commit and push all changes
- **Milestone:** User can track expenses AND chat with AI that references their actual financial data AND see data-driven recommendations on dashboard.

---

## WEEK 3: GOALS + LEARNING + CHECK-INS (Days 15-21)
**Goal:** Goal tracking, learning modules, weekly check-ins -- complete the data collection loop.

---

### Day 15-16 -- Goal Setting & Tracking

**Prasikshya:**
- Create API routes:
  - `POST /api/goals` -- create goal (name, target amount, deadline)
  - `GET /api/goals` -- list user's goals with progress
  - `PUT /api/goals/[id]` -- update goal (add to current_amount, change deadline)
  - `DELETE /api/goals/[id]` -- delete goal
- Add goal-based recommendations to engine:
  - Calculate: months_remaining, monthly_needed, gap vs current savings
  - Generate specific recommendations per goal

**Shija:**
- Build goals page:
  - "Create Goal" form: name, target amount, deadline
  - Preset goal templates (quick-start):
    - "Emergency Fund (3 months expenses)" -- auto-calculates target
    - "Study Abroad Fund" -- lets user set target
    - "First Investment Fund" -- suggests Rs. 10,000-50,000
    - "Custom Goal"
  - Goal cards with:
    - Progress bar (current / target)
    - Time remaining
    - "You need Rs. X/month to reach this"
    - "Add Savings" button (logs a savings transaction linked to this goal)
  - Color: green (on track), amber (falling behind), red (significantly behind)

**Sanskriti:**
- Research realistic goal amounts for Nepal:
  - Emergency fund: 3x monthly expenses
  - Study abroad: Rs. 20-50 lakh (depending on country)
  - Startup seed: Rs. 1-5 lakh
- Test goal creation and tracking flow
- Finalize Module 3 content

**End of Day 16:** Users can set financial goals, track progress, and get goal-specific recommendations.

---

### Day 17-19 -- Learning Modules

**Prasikshya:**
- Create API routes:
  - `GET /api/learn` -- list modules with user's progress
  - `GET /api/learn/[id]` -- get module content (lessons + quiz questions)
  - `POST /api/learn/[id]/progress` -- mark lesson complete
  - `POST /api/learn/[id]/quiz` -- submit quiz, calculate score, save
- Store module content in JSON files (not database -- easier to update):
  - `data/modules.json` -- all module content
  - `data/quiz-questions.json` -- all quiz questions with correct answers

**Shija:**
- Build learn page:
  - Module cards: title, short description, progress (X/Y lessons), status badge
  - Module detail page:
    - Lesson list (checkmarks for completed)
    - Lesson content: clean text with highlighted key concepts
    - "Mark Complete" button per lesson
    - Quiz at the end (5 multiple-choice questions)
    - Score display after quiz
    - "Next Module" suggestion

**Sanskriti:**
- Finalize ALL module content:

  **Module 1: Budgeting 101** (4 lessons + 5 quiz questions)
  - Why budget? (76% of youth don't -- our survey data)
  - The 50/30/20 rule for Nepal incomes
  - Expense categories that matter
  - Building your first monthly budget

  **Module 2: Saving Strategies** (4 lessons + 5 quiz questions)
  - Why saving feels impossible on a small income
  - Pay Yourself First: save before you spend
  - Emergency fund: your financial safety net
  - Where to save in Nepal (savings account vs FD vs recurring deposit -- with actual bank rates)

  **Module 3: Investment Basics** (4 lessons + 5 quiz questions)
  - What is investing? (It's not gambling and not just for rich people)
  - Risk vs return: the fundamental tradeoff
  - Your options in Nepal: FD, mutual funds, NEPSE, gold/silver
  - How to start investing with Rs. 1,000/month

  **Module 4: Planning for Study Abroad** (3 lessons + 5 quiz questions)
  - True cost breakdown by country
  - Savings timeline: work backwards from your departure date
  - Loans for abroad: what banks look for, how to prepare

  **Module 5: Financial Planning for Entrepreneurs** (3 lessons + 5 quiz questions)
  - Startup costs: what to expect
  - Loan readiness: what banks look for
  - Separating personal and business finances

**End of Day 19:** All 5 learning modules working with progress tracking and quizzes. Quiz scores feed into recommendation engine.

---

### Day 20 -- Weekly Check-in Feature

**Prasikshya:**
- Create API routes:
  - `POST /api/checkin` -- save weekly check-in
  - `GET /api/checkin/history` -- get past check-ins
- Add check-in data to recommendation engine:
  - IF mood = overwhelmed for 2+ weeks → recommend "Let's simplify your budget to the essentials"
  - IF saved_this_week = true consistently → positive reinforcement recommendation
  - IF biggest_expense keeps being food → specific food-saving tips

**Shija:**
- Build check-in page (or modal that appears on dashboard weekly):
  - Friendly prompt: "Quick weekly check-in (30 seconds)"
  - Question 1: "Did you save anything this week?" toggle + amount input
  - Question 2: "Biggest unexpected expense?" category dropdown + amount
  - Question 3: "How are you feeling about money?" emoji scale (4 options)
  - Submit button → brief encouraging response
- Add check-in streak indicator on dashboard ("3-week streak!")

**Sanskriti:**
- Write encouraging response messages for each mood:
  - Great: "That's awesome! Keep it up."
  - Okay: "Steady progress. Small steps count."
  - Stressed: "That's normal. Let's look at what's causing stress."
  - Overwhelmed: "You're not alone -- 70% of young Nepalis feel this way. Let's break it down."
- Test the complete data collection flow end-to-end

**End of Day 20:** Weekly check-ins work, building longitudinal emotional + behavioral data.

---

### Day 21 -- Week 3 Buffer + Deploy

- Fix bugs, deploy
- **Milestone:** All data collection is working (profile + transactions + goals + quiz scores + check-ins). Recommendations use ALL data sources. AI chat is fully context-aware.

---

## WEEK 4: CONNECT EVERYTHING + POLISH + SHIP (Days 22-30)
**Goal:** Everything works together. Polish. Test. Deploy. Prepare demo.

---

### Day 22-23 -- Full Integration

**Prasikshya:**
- Final recommendation engine upgrade -- uses ALL data:
  - Profile data → initial recommendations
  - Transaction data → budget recommendations
  - Goal data → savings recommendations
  - Quiz scores → learning recommendations
  - Check-in data → emotional-aware recommendations
  - Chat history topics → interest-based recommendations
- Generate weekly action items (5 specific tasks for the user)
- Ensure AI chat prompt includes latest data from all sources

**Shija:**
- Dashboard shows EVERYTHING:
  - Top recommendations (data-driven, personalized)
  - Weekly action items ("Your 5 tasks this week")
  - Spending snapshot with charts
  - Goal progress bars
  - Learning progress
  - Last check-in mood + trend
  - "What's changed" section: "This month vs last month: you saved 15% more"
- Navigation flows naturally between features:
  - Recommendation card → links to relevant feature
  - Chat suggestion → links to relevant module or tool
  - Goal card → links to add savings transaction

**Sanskriti:**
- Full end-to-end test: create account, use app for "1 month" (simulated data)
- Verify all recommendations make sense for different user profiles
- Verify AI chat responses are accurate and Nepal-specific

---

### Day 24-25 -- UI Polish

**Shija:**
- Consistent design across all pages
- Loading states (skeleton loaders)
- Empty states (helpful prompts, not blank screens)
- Error states (friendly messages)
- Mobile responsive (test on phone browser)
- Landing page for logged-out users:
  - Headline: what the platform does
  - 3 key benefits
  - Screenshots/mockups
  - "Get Started Free" button
- Smooth transitions and hover effects

**Prasikshya:**
- API error handling (proper messages, status codes)
- Input validation on all routes
- Rate limiting on AI chat (prevent abuse of Gemini free tier)
- Security: ensure users can only access their own data

**Sanskriti:**
- Review all text: spelling, grammar, tone
- Write landing page copy
- Prepare demo script

---

### Day 26-27 -- Testing

**Sanskriti (lead):**
- Test complete journey as 3 different user personas:
  1. **Student, 19, Rs. 8,000/month, wants to save** -- strained finances
  2. **Part-time worker, 23, Rs. 22,000/month, wants to invest** -- medium comfort
  3. **Entrepreneur, 21, Rs. 35,000/month, planning abroad** -- goal-focused
- For each persona:
  - Complete onboarding
  - Add 2 weeks of transactions
  - Set 1-2 goals
  - Chat with AI about 5 topics
  - Complete 1 learning module
  - Do 2 weekly check-ins
  - Verify recommendations are relevant and different per persona
- Test edge cases: empty data, huge numbers, special characters, rapid clicks
- Document all bugs

**Prasikshya + Shija:** Fix bugs as they're reported.

---

### Day 28 -- Final Deployment

- Deploy final version to Vercel
- Set all environment variables
- Test production URL end-to-end
- Verify Supabase production database
- Smoke test: new signup → full flow

---

### Day 29-30 -- Demo Preparation

**All three:**
- Create demo account with pre-loaded realistic data:
  - 22-year-old student, Rs. 18,000/month
  - 40+ transactions over 2 months
  - 2 goals: "Emergency Fund" + "Study Abroad"
  - Chat history showing good AI conversations
  - 2 completed modules with quiz scores
  - 4 weekly check-ins
- Practice demo walkthrough (7-8 minutes):
  1. Landing page → "Here's what we built" (30 sec)
  2. Quick sign-up flow (30 sec)
  3. Onboarding → show how we collect data (1 min)
  4. Dashboard → "Based on this data, here are personalized recommendations" (1.5 min)
  5. Budget tool → "User logs expenses, we analyze patterns" (1 min)
  6. AI Chat → ask "Where is my money going?" and "How should I save for abroad?" (1.5 min)
  7. Goals → show progress tracking (30 sec)
  8. Learning module → quick walkthrough (30 sec)
  9. Weekly check-in → show how it works (30 sec)
- Prepare backup: screenshots of every screen in case live demo fails
- Write README for the project repo

---

# DAILY STANDUP

15 minutes, same time every day (WhatsApp or Discord):
1. What did I finish yesterday?
2. What am I doing today?
3. Am I stuck on anything?

---

# RISK MANAGEMENT

| Risk | Mitigation |
|---|---|
| Gemini API rate limit hit (15 req/min) | Add request queuing; cache common responses; limit to 1 request per 4 seconds |
| Supabase free tier runs out | 500MB is plenty for prototype; monitor in Supabase dashboard |
| Vercel build fails | Deploy every week (not just at the end); catch issues early |
| Team member falls behind | Buffer days (7, 14, 21); redistribute tasks; pair programming |
| AI gives inaccurate Nepal-specific info | Strong system prompt with verified data; Sanskriti reviews; add disclaimers |
| Recommendation logic has bugs | Test with 3 different user personas; edge case testing |
| Feature creep / new ideas | Write it down for V2. Do NOT add to current sprint. |

---

# SUCCESS CRITERIA

The prototype is done when:
- [ ] User can sign up and complete financial profile onboarding (data: profile + quiz score)
- [ ] Dashboard shows personalized, data-driven recommendations
- [ ] User can log income, expenses, and savings (data: transactions)
- [ ] Spending breakdown charts visualize where money goes
- [ ] User can set financial goals and track progress (data: goals)
- [ ] AI chat knows the user's financial situation and gives Nepal-specific guidance
- [ ] 5 learning modules with quizzes track knowledge progress (data: quiz scores)
- [ ] Weekly check-in captures emotional and behavioral data
- [ ] Recommendations update as more data is collected
- [ ] Everything works on a live, free URL
- [ ] Total cost: Rs. 0

---

# POST-PROTOTYPE ROADMAP (V2)

1. Flutter mobile app
2. Gamification (streaks, badges, levels)
3. Nepali language support
4. Bank API integration (auto-import transactions)
5. ML-based recommendation engine (replace rule-based)
6. Custom fine-tuned AI model on Nepal financial data
7. Community features (peer discussions, shared tips)
8. More learning modules (10+)
9. Institutional partnerships (colleges, banks, NRB)
10. Data analytics dashboard (aggregate insights for research)
