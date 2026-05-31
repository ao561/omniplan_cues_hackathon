#  OmniPlan 🤖

A hyper-personalised group chat assistant powered by the deep context of the Model Context Protocol (MCP) to solve chaotic group planning.

---

### 💡 Inspiration

We've all been there: trying to plan a simple dinner with friends turns into a chaotic nightmare. Sarah is vegan, Tom has allergies, nobody remembers who can't do Tuesdays, and someone inevitably suggests a place 2 hours away during rush hour.

We realised the real problem isn't that we can't chat; it's that we are **making decisions blind**. AI should be able to help, but standard chatbots can't "read minds"—they lack context. They don't know where you are right now, what the weather is like, or that you hate spicy food.

We wanted to solve this by building **OmniPlan**: a tool that combines every user's calendar, location, preferences, and real-time dynamic context in one place.

---

### 🤖 What it Does

OmniPlan is a hyper-personalised group chat assistant that acts as a silent observer. It only chimes in when a user tags `@AI` for help.

When activated, OmniPlan doesn't just "guess" a recommendation. It spins up **5 distinct MCP servers** to gather live context from every user in the chat:

* **📅 Checks Calendars:** Finds the *true* free time for everyone.
* **🗣️ Sentiment Analysis:** Reads the chat history to understand the group's mood and preferences (e.g., "I'm tired of pizza").
* **👤 Profile Matching:** Updates and references individual dietary restrictions and budget constraints.
* **📍 Real-Time Location:** Checks everyone's location to find a central, convenient meeting point.
* **☀️ Live Weather:** Checks if it's raining to avoid suggesting outdoor seating.

The result is a single, smart suggestion that matches all known criteria, complete with **custom directions for every single person** in the chat.

---

### 📁 Folder Structure

```
OmniPlan/
├── main.py                    # FastAPI server & WebSocket entry point
├── active_ai_monitor.py       # Watches chat history and calls Claude with MCP tools
├── requirements.txt           # Python dependencies
├── README.md
├── .env                       # API keys (not committed)
├── service_account.json       # Google service account credentials (not committed)
│
├── servers/                   # MCP tool servers
│   ├── __init__.py
│   ├── calendar_server.py     # Google Calendar availability & location tools
│   ├── weather_server.py      # OpenWeatherMap current conditions
│   ├── location_server.py     # Google Places restaurant search & geocoding
│   ├── directions_server.py   # Google Maps directions for all group members
│   ├── sentiment_server.py    # Claude-powered food sentiment analysis
│   └── chat_monitor_mcp.py    # Chat history monitoring tools
│
├── static/
│   └── index.html             # Web chat frontend
│
├── config/
│   ├── persona_calendars.json # Maps person names to Google Calendar IDs
│   └── Json_amaan.json        # MCP client configuration (Windows reference)
│
└── data/
    ├── chat_history.txt       # Persisted chat messages (runtime)
    └── user_food_profiles.json # Per-user food preference profiles (runtime)
```

---

### 🚀 How to Run

This project runs as a single web server application which manages all the underlying MCP servers.

**1. Clone the Repository**

```sh
git clone https://github.com/ao561/omniplan_cues_hackathon.git
cd omniplan_cues_hackathon
```

**2. Create a Virtual Environment (Recommended)**

```sh
# For Mac/Linux
python3 -m venv venv
source venv/bin/activate

# For Windows
python -m venv venv
.\venv\Scripts\activate
```

**3. Install Dependencies**

```sh
pip install -r requirements.txt
```

**4. Set Up Configuration (API Keys)**

Create a `.env` file in the root of the project directory:

```sh
# For Mac/Linux
touch .env

# For Windows
echo. > .env
```

Add the following keys to `.env`:

```sh
# 1. Anthropic API Key (for Claude)
# Get this from the Anthropic Console: https://console.anthropic.com/
ANTHROPIC_API_KEY="sk-ant-..."

# 2. Google Maps API Key (for Directions & Location)
# Enable: Directions API, Geocoding API, Maps JavaScript API
GOOGLE_MAPS_API_KEY="AIzaSy..."

# 3. OpenWeatherMap API Key
# Get this from: https://openweathermap.org/api
OPENWEATHER_API_KEY="..."
```

> Never commit your `.env` file. Add it to `.gitignore`.

**5. Add Google Calendar Credentials**

Place your `service_account.json` (Google service account key file) in the project root. Update `config/persona_calendars.json` to map each group member's name to their Google Calendar ID.

**6. Run the Web Server**

```sh
python -m uvicorn main:app --host 0.0.0.0 --port 9000
```

Open your browser to `http://localhost:9000`. The server will also start the `active_ai_monitor.py` background process automatically.

> Using `0.0.0.0` makes the server accessible to other devices on your local network. Use `127.0.0.1` to restrict to your own machine only.

---

### ⚙️ How We Built It

We built OmniPlan as a modular agent leveraging the **Model Context Protocol (MCP)** to standardise how the AI accesses and orchestrates information.

* **The Brain:** We used **Claude** (running in `active_ai_monitor.py`) as the orchestration engine because of its ability to work effectively with live, hyper-personalised data streams.
* **The Architecture:** The `main:app` server, when run, orchestrates five separate MCP "tools" that provide live context:
    1.  `servers/calendar_server.py`
    2.  `servers/weather_server.py`
    3.  `servers/location_server.py`
    4.  `servers/directions_server.py`
    5.  `servers/sentiment_server.py`
* **Modular Design:** We used a modular MCP design so new context sources (like live event APIs or transit schedules) can be added instantly without re-engineering the core logic.
* **Privacy-First:** The bot is passive and only activates when called. The MCP workflow ensures only our defined tools can run, which helps prevent hallucinations and protects group chat privacy.

---

### 🚧 Challenges We Ran Into

* **The MCP Learning Curve:** Since MCP is a relatively new standard, simply getting the workflow to ensure only our defined tools ran (and nothing else) took significant effort.
* **Context Overload:** Balancing the amount of data fed to the context window was tricky. We had to fine-tune the sentiment analysis to understand both individual and group preferences without overwhelming the model.

---

### 🏆 Accomplishments We're Proud Of

* **True Modularity:** We successfully implemented a modular MCP design where new context sources can be added instantly.
* **Privacy-First Design:** We built a bot that respects group chat privacy by default, only responding when explicitly tagged.
* **Working Prototype:** We delivered a fully working, real-time contextual assistant that successfully uses multiple, simultaneous MCP servers to provide a genuinely useful recommendation.

---

### 🧠 What We Learned

* **The Power of Context:** AI needs more than just a prompt; it needs "deep context" (calendars, location, weather, preferences) to be truly useful and graduate from a toy to a tool.
* **Real-Time Constraints:** Working with live, streaming context required us to optimise how the model handles and prioritises hyper-personalised data from multiple sources at once.

---

### 🚀 What's Next for OmniPlan

We plan to evolve OmniPlan from a meeting scheduler into a fully-fledged travel and social companion.

* **Richer API Integrations:** Integrating Google Maps/Transit, restaurant availability (e.g., OpenTable), live events, and flight status.
* **Proactive Alerts:** Adding proactive alerts for weather shifts, transport delays, or nearby "hidden gem" food matches based on group preferences.
* **True Adaptability:** Making the assistant adapt to where you are and what is happening around you in real-time, becoming a true "co-pilot for your life."
