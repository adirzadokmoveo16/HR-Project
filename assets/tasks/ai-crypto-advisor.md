# Moveo Coding Task - AI Crypto Advisor

## Concept
Build a web app that serves as a personalized crypto investor dashboard. The app gets to know the user through a short onboarding quiz and then shows daily AI-curated content tailored to their interests. Users can give feedback (thumbs up/down) to help improve future recommendations. A fun meme is also shown dynamically each time the dashboard updates. 

## Pages & Features

### 1. Login/Signup
- Register with email, name, and password.
- Login page with basic authentication (JWT or session-based).

### 2. Onboarding (After first login)
Ask a few short questions:
- What crypto assets are you interested in?
- What type of investor are you? (e.g., HODLer, Day Trader, NFT Collector)
- What kind of content would you like to see? (e.g., Market News, Charts, Social, Fun)
- Save answers in the database as user preferences.

### 3. Daily Dashboard
Display 4 sections based on user preferences:
- **Market News:** (CryptoPanic API or static fallback)
- **Coin Prices:** (CoinGecko API)
- **AI Insight of the Day:** (Free LLM like OpenRouter or Hugging Face)
- Fun Crypto Meme:
- Each section must include voting (thumbs up/down), stored in the DB for future model improvements.

## Technical Guidelines

- **Frontend:** React or Angular.
- **Backend:** Any language/framework.
- **Database:** SQLite, PostgreSQL, or MongoDB.
- **External Tools:** Use only free public APIs and free-tier AI tools.
- **Focus:** Clean UX, readable code, and solid architectural structure.

### Suggested Free APIs
- **Coin Data:** CoinGecko API
- **News:** CryptoPanic
- **AI Insights:** OpenRouter, Hugging Face Inference API
- **Memes:** Reddit scraping or static JSON

## Deployment
Your project must be deployed and publicly accessible. Suggested free services:
- **Frontend:** Vercel or Netlify
- **Backend + DB:** Render, Railway, or Glitch

## Deliverables
- **Public GitHub Repository:** Containing the full source code.
- **Deployed App URL:** Link to the live project.
- **AI Usage Summary:** If you use any AI tools (e.g., ChatGPT, Cursor, GitHub Copilot, etc.), include a summary of your interactions. This helps us understand your thinking process and how you collaborate with AI.
- **DB Access:** Instructions or access to verify data persistence.

---

## Bonus
Add suggestion about the process of training, related to this dashboard app, how feedback is stored for future model improvements (no need to implement) 

**Good luck!**