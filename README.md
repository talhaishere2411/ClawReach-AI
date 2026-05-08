# 🦞 ClawReach: Autonomous AI Influencer Scouting

**Clash of the Claws | PRISM Tech Management, SRI-B**
**Team:** Mohamed Talha Natherma, Mohd Sahil, Keshav Kunal, Ishita Srivastava (Ramaiah Institute of Technology)

---

## 🚨 The Problem
Influencer marketing is a $32.5B industry, yet creator discovery remains a massive bottleneck. Brand managers spend upwards of 40 hours per campaign manually searching YouTube, verifying if creators are actually active, and watching videos to identify hidden sponsorships. Existing SaaS tools rely on stale, indexed databases that cost $500+/month and lack contextual understanding.

## 💡 The Solution
ClawReach is an autonomous, zero-touch pipeline that shifts discovery from manual search to GenAI execution. 

We built a backend worker utilizing the **OpenClaw framework** and **Gemma 4 31b**. Triggered by a UI (Telegram/Dashboard), it runs a strict "Relay Race" of Python skills:
1. **Pulse Verification:** Scrapes YouTube to find leads and strictly verifies their recent upload activity (within 365 days).
2. **Concurrent Analysis:** Blasts the top 10 channels concurrently, using Gemma to "read" their video descriptions to extract business emails and identify existing sponsor conflicts.
3. **Real-Time Persistence:** Saves the enriched data to Supabase, triggering real-time WebSocket updates to the user interface.

What takes a human 4 hours takes ClawReach 60 seconds.

---

## ⚙️ Architecture & Source Code
This repository contains the **AI Worker Node** responsible for the heavy lifting.

* `heartbeat_bridge.py`: The Orchestrator. Polls the Supabase database for new campaigns and dynamically triggers the OpenClaw agent.
* `skills/discover_active_channels.py`: Skill 1 (YouTube Data API v3 & deduplication).
* `skills/analyze_descriptions.py`: Skill 2 (Concurrent LLM description parsing).
* `skills/save_to_database.py`: Skill 3 (Supabase upsert and real-time triggers).

---

## 🚀 Setup & Installation

**1. Clone the repository**
```bash
git clone [https://github.com/YOUR_USERNAME/claw-reach-worker.git](https://github.com/YOUR_USERNAME/claw-reach-worker.git)
cd claw-reach-worker
```

**2. Install Dependencies**
```bash
pip install -r requirements.txt
```

**3. Environment Variables**
Create a `.env` file in the root directory. You must add your own API keys. **Never commit this file.**
```env
YOUTUBE_API_KEY=your_youtube_v3_api_key
SUPABASE_URL=your_supabase_project_url
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_key
GOOGLE_API_KEY=your_gemma_api_key
```

---

## 💻 Usage & Instructions

To start the autonomous worker, simply run the bridge script:

```bash
python heartbeat_bridge.py
```

**Expected Behavior:**
1. The terminal will display: `📡 Bridge is listening for Supabase signals...`
2. Once a user inputs a campaign brief via our frontend (Telegram/Web), Supabase registers the campaign as `ready_for_openclaw`.
3. The heartbeat detects the row, locks the status to `processing`, and hands the brief to the OpenClaw agent.
4. OpenClaw executes the `skills.md` relay race autonomously.
5. Final data is pushed back to the database.

---

## 🛠️ Built With
* Python 3.x
* OpenClaw Framework
* Google Generative AI (Gemma 4 31b)
* Supabase
* YouTube Data API v3
