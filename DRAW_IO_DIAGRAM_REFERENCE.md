# 🎨 Draw.io Diagram Reference - Visual Guide
## Detailed Visual Layouts for All 5 Diagrams

---

## 📌 COLOR SCHEME (Use in Draw.io)

```
🟢 Green (#90EE90)      - Mobile App / User Interface
🔵 Blue (#87CEEB)       - API Routes / Request Handler
🟣 Purple (#DDA0DD)     - Claude AI / LLM Services
🟠 Orange (#FFB347)     - Database / Tables
🔴 Pink (#FFB6C6)       - Service Layer / Business Logic
🟦 Teal (#80CED7)       - Models / Validation
🟡 Yellow (#FFFFE0)     - Data Processing / Transformation
⚪ White (#FFFFFF)      - General/Neutral boxes
```

---

# 📊 DIAGRAM 1: SYSTEM ARCHITECTURE OVERVIEW
## (For Team Lead, BA, Project Manager)

```
╔════════════════════════════════════════════════════════════════════════════════╗
║                                                                                ║
║                            📱 MOBILE APP                                       ║
║                         (User Interface)                                       ║
║                          🟢 GREEN BOX                                          ║
║                                                                                ║
╚═════════════════════════════════╦════════════════════════════════════════════╝
                                  │
                                  │ HTTP POST Requests
                                  │
      ┌───────────────────────────┴───────────────────────────┐
      │                                                         │
      ▼                                                         ▼
╔════════════════════════════════════════════════════════════════════════════════╗
║                      🔵 FASTAPI ROUTES LAYER (BLUE)                           ║
║                                                                                ║
║  POST /beauty-overview  POST /cycle-overview  POST /athlete-overview          ║
║  POST /pregnancy-overview  POST /menopause-overview  POST /thriving-overview  ║
║                                                                                ║
╚════════════════════════════════════════════════════════════════════════════════╝
      │                    │                    │                    │
      │                    │                    │                    │
      ▼                    ▼                    ▼                    ▼
   ┌─────────┐         ┌─────────┐         ┌─────────┐         ┌─────────┐
   │  🔴PINK │         │  🔴PINK │         │  🔴PINK │         │  🔴PINK │
   │ beauty_ │         │ cycle_  │         │ athlete_│         │ pregnancy
   │service  │         │service  │         │service  │         │_service
   └────┬────┘         └────┬────┘         └────┬────┘         └────┬────┘
        │                   │                   │                   │
        ├─────┐             ├─────┐             ├─────┐             ├─────┐
        │     │             │     │             │     │             │     │
        ▼     ▼             ▼     ▼             ▼     ▼             ▼     ▼
     ┌────┐┌────┐       ┌────┐┌────┐       ┌────┐┌────┐       ┌────┐┌────┐
     │🟠DB││    │       │🟠DB││    │       │🟠DB││    │       │🟠DB││    │
     │ 1  ││ 2  │       │ 1  ││ 2  │       │ 1  ││ 2  │       │ 1  ││ 2  │
     └────┘└────┘       └────┘└────┘       └────┘└────┘       └────┘└────┘

        │  │                │  │                │  │                │  │
        └──┼────────────────┴──┼────────────────┴──┼────────────────┴──┘
           │                   │                   │
           └───────────────────┼───────────────────┘
                               │
                               ▼
╔════════════════════════════════════════════════════════════════════════════════╗
║                    🟣 CLAUDE AI API (PURPLE)                                   ║
║                    (Insights Generation)                                       ║
║                                                                                ║
║  • Analyze skin images (Claude Vision)                                        ║
║  • Generate cycle insights (Claude Opus)                                      ║
║  • Personalized recommendations                                              ║
║                                                                                ║
╚════════════════════════════════════════════════════════════════════════════════╝
                               │
                    ┌──────────┼──────────┐
                    │          │          │
                    ▼          ▼          ▼
                ┌────────┬────────┬────────┐
                │ 🔴PINK │ 🔴PINK │ 🔴PINK │
                │ Service│ Service│ Service│
                │   1    │   2    │   3    │
                └────┬───┴────┬───┴────┬───┘
                     │        │        │
                     ▼        ▼        ▼
                  ┌────────────────────────┐
                  │  🟦TEAL - PYDANTIC    │
                  │  VALIDATION MODELS    │
                  └────┬───────────────────┘
                       │
                       ▼ JSON Response
                  ┌────────────────┐
                  │  📱 MOBILE APP │
                  │  Display Data  │
                  └────────────────┘
```

### KEY ELEMENTS FOR DRAW.IO:

```
BOX STYLE:
- Mobile App: Size 200×80, Green, bold border, 14pt font
- Routes: Size 800×100, Blue, dashed border, 11pt font
- Services: Size 150×80, Pink, solid border, 11pt font
- Database: Size 120×80, Orange, solid border, 10pt font
- Claude AI: Size 800×120, Purple, bold border, 12pt font
- Pydantic: Size 600×80, Teal, solid border, 11pt font

ARROWS:
- Solid arrows: Normal flow
- Dashed arrows: Optional/conditional flow
- Arrow thickness: 2px
- Arrow labels: 10pt font, positioned above line

LAYOUT:
- Top to bottom flow
- Center alignment
- 40px spacing between elements
- Group related services (Beauty, Cycle, Athlete on left; Pregnancy, Menopause, Thriving on right)
```

---

# 📊 DIAGRAM 2: SINGLE ENDPOINT DETAIL FLOW
## (Example: Beauty & Radiance - For Backend Developers)

```
╔════════════════════════════════════════════════════════════════════════════════╗
║              REQUEST: POST /beauty-overview                                    ║
║              🟢 MOBILE APP sends request                                       ║
╚════════════════════════════════════════════════════════════════════════════════╝

INPUT PAYLOAD:
┌──────────────────────────────────────────────┐
│ 🟡 REQUEST BODY (Yellow Box)                 │
│ {                                            │
│   "user_id": "usr_2026_001",                 │
│   "days": 30,                                │
│   "include_correlations": true,              │
│   "timestamp": "2026-09-22T10:30:00Z"        │
│ }                                            │
└──────────────────────────────────────────────┘
                  │
                  ▼
        ┌─────────────────────────────────────┐
        │ 🔵 ROUTE VALIDATION (Blue Box)      │
        │ • Check user_id exists              │
        │ • Validate days (1-365)             │
        │ • Verify timestamp format           │
        └─────────────────────────────────────┘
                  │
        ✅ Validation OK
                  │
                  ▼
        ┌─────────────────────────────────────┐
        │ 🔴 SERVICE LAYER (Pink Box)         │
        │ beauty_service.get_overview()       │
        └─────────────────────────────────────┘
                  │
     ┌────────────┼────────────┐
     │            │            │
     ▼            ▼            ▼

┌─────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│ 🟠 QUERY DB #1  │  │ 🟠 QUERY DB #2   │  │ 🟠 QUERY DB #3   │
│ (Orange Box)    │  │ (Orange Box)     │  │ (Orange Box)     │
│                 │  │                  │  │                  │
│ skin_scans      │  │ terra_activity   │  │ menstrual_cycles │
│                 │  │ _data            │  │                  │
│ SELECT * FROM   │  │ SELECT JSON_     │  │ SELECT current   │
│ skin_scans      │  │ EXTRACT(payload..│  │ _phase, cycle    │
│ WHERE user_id=2 │  │ WHERE user_id=2  │  │ _day FROM...     │
│ LIMIT 30        │  │ TYPE='daily'     │  │ WHERE user_id=2  │
│                 │  │                  │  │                  │
│ RETURNS:        │  │ RETURNS:         │  │ RETURNS:         │
│ • score 0-100   │  │ • sleep hours    │  │ • phase: luteal  │
│ • status        │  │ • activity MET   │  │ • cycle_day: 21  │
│ • timestamp     │  │ • recovery pct   │  │ • estimated days │
│ • image_insights│  │                  │  │                  │
└─────────────────┘  └──────────────────┘  └──────────────────┘
     │                    │                    │
     └────────────────────┼────────────────────┘
                          │
                          ▼
        ┌─────────────────────────────────────┐
        │ 🟡 PREPARE AI CONTEXT (Yellow)      │
        │                                     │
        │ context = {                         │
        │   "today_score": 78,                │
        │   "today_status": "Good",           │
        │   "metrics": {...7 metrics...},     │
        │   "history_30d": [...],             │
        │   "sleep_hours": 7.5,               │
        │   "cycle_phase": "luteal",          │
        │   "cycle_day": 21                   │
        │ }                                   │
        └─────────────────────────────────────┘
                          │
                          ▼
        ┌──────────────────────────────────────────┐
        │ 🟣 CALL CLAUDE AI (Purple Box)           │
        │                                          │
        │ claude.generate(                         │
        │   model="claude-opus-4-7",               │
        │   system=BEAUTY_SYSTEM_PROMPT,           │
        │   prompt=f"""                            │
        │   Analyze this skin data:                │
        │   Score: {today_score}                   │
        │   Metrics: {metrics}                     │
        │   30-day history: {history}              │
        │   Sleep hours: {sleep}                   │
        │   Cycle phase: {phase}                   │
        │                                          │
        │   Generate:                              │
        │   1. Analysis of current state           │
        │   2. Sleep impact on skin               │
        │   3. Cycle phase recommendations        │
        │   4. Top 3 action items                 │
        │   """,                                   │
        │   max_tokens=1024                        │
        │ )                                        │
        └──────────────────────────────────────────┘
                          │
                          ▼
        ┌──────────────────────────────────────────┐
        │ 🟣 CLAUDE RESPONSE (Purple Result Box)   │
        │                                          │
        │ {                                        │
        │   "analysis": "Your skin shows good...  │
        │   "sleep_impact": "Excellent correlation│
        │   "recommendations": [                   │
        │     "Hydrate more before bed",           │
        │     "Use SPF during follicular phase",   │
        │     "Extra moisturizer in luteal phase"  │
        │   ],                                     │
        │   "focus_areas": ["hydration", "glow"]   │
        │ }                                        │
        └──────────────────────────────────────────┘
                          │
                          ▼
        ┌──────────────────────────────────────────┐
        │ 🟡 PARSE & VALIDATE (Yellow)             │
        │ • Extract text insights                  │
        │ • Validate recommendation count          │
        │ • Format timestamps                      │
        │ • Structure correlations                 │
        └──────────────────────────────────────────┘
                          │
                          ▼
        ┌──────────────────────────────────────────┐
        │ 🟦 PYDANTIC MODEL VALIDATION (Teal)      │
        │                                          │
        │ BeautyRadianceResponse(                  │
        │   user_id="usr_2026_001",                │
        │   timestamp="2026-09-22T10:30:00Z",      │
        │   today=BeautyToday(...),                │
        │   history=[...],                         │
        │   correlations=Correlations(...),        │
        │   ai_insights=AIInsights(...)            │
        │ )                                        │
        │ ✅ All fields validated                  │
        └──────────────────────────────────────────┘
                          │
                          ▼
╔══════════════════════════════════════════════════════════════════════════════╗
║ RESPONSE: HTTP 200 OK                                                        ║
║ Content-Type: application/json                                              ║
║                                                                              ║
║ {                                                                            ║
║   "success": true,                                                           ║
║   "data": {                                                                  ║
║     "user_id": "usr_2026_001",                                               ║
║     "today": {                                                               ║
║       "score": 78,                                                           ║
║       "status": "Good",                                                      ║
║       "metrics": {                                                           ║
║         "hydration": 75, "redness": 80, "texture": 78,                       ║
║         "glow": 76, "pore_health": 82, "elasticity": 74,                     ║
║         "overall": 78                                                        ║
║       },                                                                     ║
║       "insights": "Your skin shows good overall health..."                   ║
║     },                                                                       ║
║     "history": [                                                             ║
║       {"date": "2026-09-22", "score": 78, "status": "Good"},                 ║
║       {"date": "2026-09-21", "score": 76, "status": "Good"},                 ║
║       ... (30 records) ...                                                   ║
║     ],                                                                       ║
║     "correlations": {                                                        ║
║       "sleep": {                                                             ║
║         "correlation_strength": 0.85,                                        ║
║         "insight": "Higher sleep strongly improves hydration"                ║
║       },                                                                     ║
║       "cycle": {                                                             ║
║         "correlation_strength": 0.72,                                        ║
║         "insight": "Luteal phase: skin becomes drier"                        ║
║       }                                                                      ║
║     },                                                                       ║
║     "ai_insights": {                                                         ║
║       "analysis": "Long detailed analysis from Claude...",                   ║
║       "recommendations": [...],                                              ║
║       "focus_areas": ["hydration", "glow"]                                   ║
║     }                                                                        ║
║   },                                                                         ║
║   "error": null                                                              ║
║ }                                                                            ║
╚══════════════════════════════════════════════════════════════════════════════╝
                          │
                          ▼
                    📱 MOBILE APP
              (Display results to user)
```

### KEY ELEMENTS FOR DRAW.IO:

```
LAYOUT: Vertical flow, left-to-right for parallel database queries

COLORING BY STAGE:
┌─────────────────────────────────────────┐
│ Input Layer:     🟡 Yellow              │
├─────────────────────────────────────────┤
│ Validation:      🔵 Blue                │
├─────────────────────────────────────────┤
│ Business Logic:  🔴 Pink                │
├─────────────────────────────────────────┤
│ Database:        🟠 Orange (3 queries)  │
├─────────────────────────────────────────┤
│ Data Prep:       🟡 Yellow              │
├─────────────────────────────────────────┤
│ AI Processing:   🟣 Purple              │
├─────────────────────────────────────────┤
│ Parsing:         🟡 Yellow              │
├─────────────────────────────────────────┤
│ Validation:      🟦 Teal                │
├─────────────────────────────────────────┤
│ Response:        ⚪ White               │
└─────────────────────────────────────────┘

BOX DIMENSIONS:
- Input/Output: 400×100
- Small process: 300×80
- Database query: 300×150
- AI Call: 500×200
- Response: 600×400

CONNECTOR STYLE:
- Solid lines: Main flow
- Diamond for branching (e.g., multiple DB queries)
- Dashed for optional paths
```

---

# 📊 DIAGRAM 3: CYCLE & FERTILITY ENDPOINT
## (Complex Flow Example - For Backend Developers)

```
╔════════════════════════════════════════════════════════════════════════════════╗
║          REQUEST: POST /cycle-overview                                         ║
║          🟢 MOBILE APP - Cycle & Fertility Module                              ║
╚════════════════════════════════════════════════════════════════════════════════╝

INPUT OPTIONS (Two modes):
┌────────────────────────────────────────┐  ┌────────────────────────────────────┐
│ MODE 1: Tracking Fertility             │  │ MODE 2: Avoiding Pregnancy         │
├────────────────────────────────────────┤  ├────────────────────────────────────┤
│ {                                      │  │ {                                  │
│   "user_id": "usr_2026_001",           │  │   "user_id": "usr_2026_001",       │
│   "mode": "tracking_fertility",        │  │   "mode": "avoiding_pregnancy",    │
│   "include_bbt": true,                 │  │   "include_bbt": false,            │
│   "include_opk": true                  │  │   "include_ovulation": true        │
│ }                                      │  │ }                                  │
└────────────────────────────────────────┘  └────────────────────────────────────┘
                    │                                    │
                    └────────────┬───────────────────────┘
                                 │
                                 ▼
                  ┌──────────────────────────────┐
                  │ 🔴 cycle_service (Pink)      │
                  │ get_cycle_overview()         │
                  └──────────────────────────────┘
                                 │
          ┌──────────────────────┼──────────────────────┐
          │                      │                      │
          ▼                      ▼                      ▼
  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
  │ 🟠 DB QUERY #1   │  │ 🟠 DB QUERY #2   │  │ 🟠 DB QUERY #3   │
  │ (Orange)         │  │ (Orange)         │  │ (Orange)         │
  │                  │  │                  │  │                  │
  │ menstrual_cycles │  │ bbt_logs (if     │  │ terra_activity   │
  │                  │  │ mode=tracking)   │  │ _data (optional) │
  │ SELECT:          │  │                  │  │                  │
  │ • cycle_length   │  │ SELECT:          │  │ SELECT:          │
  │ • period_start   │  │ • temperature    │  │ • sleep hours    │
  │ • period_end     │  │ • log_date       │  │ • recovery data  │
  │ • current_day    │  │ • cycle_day      │  │ (correlations)   │
  │ • current_phase  │  │ • coverline      │  │                  │
  │ • ovulation_day  │  │ • excluded flags │  │                  │
  │ • confirmed_day  │  │                  │  │                  │
  └──────────────────┘  └──────────────────┘  └──────────────────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                                 ▼
          ┌──────────────────────────────────────┐
          │ 🟡 CALCULATE CYCLE METRICS (Yellow)  │
          │                                      │
          │ Current Status:                      │
          │ • cycle_day = today - period_start   │
          │ • current_phase (from tables)        │
          │ • days_until_next_period             │
          │                                      │
          │ Ovulation Prediction:                │
          │ • estimated_ovulation_date           │
          │ • fertile_window: -5 to +1 day       │
          │ • confidence_score: 0.0-1.0          │
          │                                      │
          │ BBT Analysis (if available):         │
          │ • thermal_shift_detected: yes/no     │
          │ • confirmed_ovulation_date           │
          │ • post_ovulation_temps               │
          └──────────────────────────────────────┘
                                 │
                                 ▼
          ┌──────────────────────────────────────┐
          │ 🟣 CLAUDE AI ANALYSIS (Purple)       │
          │                                      │
          │ Input:                               │
          │ • Cycle history (3+ months)          │
          │ • BBT data (if available)            │
          │ • Current phase & predictions        │
          │ • Sleep correlation data             │
          │ • Mode (fertility or avoid)          │
          │                                      │
          │ Generate:                            │
          │ • Personalized cycle insights        │
          │ • Optimal timing recommendations     │
          │ • Phase-specific advice              │
          │ • Accuracy assessment                │
          └──────────────────────────────────────┘
                                 │
                                 ▼
          ┌──────────────────────────────────────┐
          │ 🟡 PARSE AI RESPONSE (Yellow)        │
          │ • Extract insights text              │
          │ • Validate recommendations           │
          │ • Calculate confidence scores        │
          │ • Format predictions                 │
          └──────────────────────────────────────┘
                                 │
                                 ▼
          ┌──────────────────────────────────────┐
          │ 🟦 PYDANTIC VALIDATION (Teal)        │
          │ CycleOverviewResponse(               │
          │   user_id,                           │
          │   current_cycle,                     │
          │   fertile_window,                    │
          │   predictions,                       │
          │   bbt_analysis,                      │
          │   ai_insights                        │
          │ )                                    │
          └──────────────────────────────────────┘
                                 │
                                 ▼
╔════════════════════════════════════════════════════════════════════════════════╗
║ RESPONSE HTTP 200                                                              ║
║                                                                                ║
║ {                                                                              ║
║   "success": true,                                                             ║
║   "data": {                                                                    ║
║     "current_cycle": {                                                         ║
║       "cycle_day": 14,                                                         ║
║       "phase": "ovulatory",                                                    ║
║       "days_left_in_cycle": 14,                                                ║
║       "cycle_length": 28                                                       ║
║     },                                                                         ║
║     "fertile_window": {                                                        ║
║       "start_date": "2026-09-20",                                              ║
║       "end_date": "2026-09-24",                                                ║
║       "peak_fertility_date": "2026-09-22",                                      ║
║       "current_status": "highly_fertile",                                      ║
║       "confidence": 0.92                                                       ║
║     },                                                                         ║
║     "ovulation_prediction": {                                                  ║
║       "estimated_ovulation": "2026-09-22",                                     ║
║       "method": "calendar_based + BBT confirmed",                              ║
║       "confidence": 0.95                                                       ║
║     },                                                                         ║
║     "bbt_analysis": {                                                          ║
║       "thermal_shift_detected": true,                                          ║
║       "confirmed_ovulation_date": "2026-09-21",                                ║
║       "coverline": 98.1,                                                       ║
║       "post_ovulation_count": 1                                                ║
║     },                                                                         ║
║     "ai_insights": {                                                           ║
║       "current_analysis": "You're in your fertile window...",                  ║
║       "recommendations": [                                                     ║
║         "Optimal time for conception",                                         ║
║         "Track daily if possible",                                             ║
║         "Monitor temperature patterns"                                         ║
║       ],                                                                       ║
║       "phase_advice": "Luteal phase coming..."                                 ║
║     }                                                                          ║
║   }                                                                            ║
║ }                                                                              ║
╚════════════════════════════════════════════════════════════════════════════════╝
```

---

# 📊 DIAGRAM 4: DATABASE DEPENDENCIES MATRIX
## (For Database Team - Visual Table)

```
╔════════════════════════════════════════════════════════════════════════════════╗
║               DATABASE DEPENDENCIES - WHICH TABLE FEEDS WHAT                   ║
╚════════════════════════════════════════════════════════════════════════════════╝

TABLE HEADER ROW: 🟠 ORANGE BOX
┌──────────────────────────────────────────────────────────────────────────────┐
│  ENDPOINT          │  SKIN_SCANS │  MENSTRUAL  │  BBT_LOGS │  TERRA_DATA   │
│  (LEFT COLUMN)     │  (ORANGE)   │  _CYCLES    │  (ORANGE) │  (ORANGE)     │
│                    │             │  (ORANGE)   │           │               │
└──────────────────────────────────────────────────────────────────────────────┘

DATA ROWS (LIGHT GRAY BACKGROUND):

┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│  Beauty & Radiance   │      ✅     │      ✅      │         │      ✅       │
│  (Skin analysis)     │ (scores,    │ (for cycle   │         │ (sleep hours)│
│                      │  status)    │ correlation) │         │ (correlations)
│                      │             │              │         │              │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │              │         │              │
│  Cycle & Fertility   │             │      ✅      │   ✅    │      ✅       │
│  (Ovulation, fertile │             │ (dates,      │ (temp,  │ (optional    │
│   window)            │             │  phases)     │  shift) │ correlation) │
│                      │             │              │         │              │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │              │         │              │
│  Athlete Performance │             │      ✅      │         │      ✅       │
│  (Training by cycle) │             │ (phase info) │         │ (MET, HR)    │
│                      │             │              │         │              │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │              │         │              │
│  Pregnancy &         │             │      ✅      │         │      ✅       │
│  Postpartum          │             │ (LMP for     │         │ (recovery)   │
│  (Trimester, week)   │             │  calculations)        │              │
│                      │             │              │         │              │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │              │         │              │
│  Menopause & Vitality│             │      ✅      │         │      ✅       │
│  (Symptoms, phase)   │             │ (cycle info) │         │ (activity)   │
│                      │             │              │         │              │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │              │         │              │
│  Lifelong Thriving   │             │      ✅      │         │      ✅       │
│  (6-year history)    │             │ (long-term)  │         │ (long-term)  │
│                      │             │              │         │              │
└──────────────────────────────────────────────────────────────────────────────┘

ADDITIONAL TABLE COLUMNS:
┌──────────────────────────────────────────────────────────────────────────────┐
│  ENDPOINT          │ HEALTH_LOGS │  USER_      │ PREGNANCY_   │  NOTES     │
│                    │ (ORANGE)    │  PROFILES   │  STATUS (NEW)│            │
│                    │             │ (ORANGE)    │ (ORANGE)     │            │
└──────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                      │             │             │              │            │
│  Beauty & Radiance   │             │      ✅     │              │            │
│                      │             │ (user info) │              │            │
│                      │             │             │              │            │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │             │              │            │
│  Cycle & Fertility   │             │      ✅     │              │            │
│                      │             │ (user age)  │              │            │
│                      │             │             │              │            │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │             │              │            │
│  Athlete Performance │             │      ✅     │              │            │
│                      │             │             │              │            │
│                      │             │             │              │            │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │             │              │            │
│  Pregnancy &         │      ✅     │      ✅     │      ✅       │ ⚠️ NEW    │
│  Postpartum          │ (recovery)  │ (user age)  │ (required)   │  TABLE    │
│                      │             │             │              │            │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │             │              │            │
│  Menopause & Vitality│      ✅     │      ✅     │              │            │
│  (mood, energy)      │             │             │              │            │
│                      │             │             │              │            │
├──────────────────────────────────────────────────────────────────────────────┤
│                      │             │             │              │            │
│  Lifelong Thriving   │      ✅     │      ✅     │              │            │
│  (long-term history) │             │             │              │            │
│                      │             │             │              │            │
└──────────────────────────────────────────────────────────────────────────────┘

LEGEND:
├─ ✅ = Uses this table
├─ (blank) = Doesn't use
└─ ⚠️ = NEW TABLE - MUST BE CREATED

CRITICAL NOTES (Red box):
╔════════════════════════════════════════════════════════════════════════════════╗
║  ⚠️ PREGNANCY_STATUS TABLE MUST BE CREATED BEFORE IMPLEMENTATION              ║
║     Fields needed: user_id, pregnancy_start_date, due_date, trimester, etc.   ║
║                                                                                ║
║  ⚠️ TERRA_ACTIVITY_DATA: Sleep values currently NULL (but structure ready)    ║
║     Will be populated when user enables sleep tracking                        ║
╚════════════════════════════════════════════════════════════════════════════════╝
```

---

# 📊 DIAGRAM 5: ERROR HANDLING & EDGE CASES
## (For QA/Testing Team)

```
╔════════════════════════════════════════════════════════════════════════════════╗
║                 ERROR HANDLING & RESPONSE CODES                                ║
╚════════════════════════════════════════════════════════════════════════════════╝

REQUEST VALIDATION LAYER (🔵 BLUE):
┌──────────────────────────────────────────────────────────────────────────────┐
│ ❌ ERROR PATHS                        │  ✅ SUCCESS PATH                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ Missing user_id?                     │ user_id present?                      │
│ └─► HTTP 400 Bad Request             │ └─► Continue                          │
│     { "error": "user_id required" }  │                                       │
│                                      │                                       │
│ Invalid days (< 1 or > 365)?         │ days valid (1-365)?                   │
│ └─► HTTP 400 Bad Request             │ └─► Continue                          │
│     { "error": "days must be 1-365" }│                                       │
│                                      │                                       │
│ Invalid timestamp format?            │ timestamp valid?                      │
│ └─► HTTP 400 Bad Request             │ └─► Continue to DB                    │
│     { "error": "invalid timestamp" } │                                       │
└──────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
DATABASE QUERY LAYER (🟠 ORANGE):
┌──────────────────────────────────────────────────────────────────────────────┐
│ ❌ ERROR PATHS                        │  ✅ SUCCESS PATH                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ User not found in DB?                │ user_id exists?                       │
│ └─► HTTP 404 Not Found               │ └─► Continue                          │
│     { "error": "User not found" }    │                                       │
│                                      │                                       │
│ No data in skin_scans table?         │ Data found?                           │
│ └─► Return empty array but succeed   │ └─► Process data                      │
│     { "today": null, "history": [] } │                                       │
│                                      │                                       │
│ Database connection timeout?         │ Query completes < 5sec?               │
│ └─► HTTP 504 Gateway Timeout         │ └─► Continue to Claude                │
│     { "error": "DB connection lost" }│                                       │
│                                      │                                       │
│ SQL Injection attempt detected?      │ Query safe?                           │
│ └─► HTTP 400 Bad Request             │ └─► Execute safely                    │
│     { "error": "Invalid query params"}│    (parameterized queries)            │
└──────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
AI/CLAUDE LAYER (🟣 PURPLE):
┌──────────────────────────────────────────────────────────────────────────────┐
│ ❌ ERROR PATHS                        │  ✅ SUCCESS PATH                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ API Key invalid/expired?             │ API key valid?                        │
│ └─► HTTP 500 Internal Server Error   │ └─► Continue                          │
│     { "error": "AI Auth failed" }    │                                       │
│                                      │                                       │
│ Rate limit exceeded (429)?           │ Under rate limit?                     │
│ └─► HTTP 429 Too Many Requests       │ └─► Send request                      │
│     { "error": "Rate limit reached" }│                                       │
│     Retry-After: 60 seconds          │                                       │
│                                      │                                       │
│ API timeout (no response in 30s)?    │ Response received < 30s?              │
│ └─► HTTP 504 Gateway Timeout         │ └─► Parse response                    │
│     { "error": "Claude API timeout" }│                                       │
│                                      │                                       │
│ Invalid API response format?         │ Response JSON valid?                  │
│ └─► HTTP 500 Server Error            │ └─► Continue                          │
│     { "error": "Invalid response" }  │                                       │
│                                      │                                       │
│ Claude returns empty/null?           │ Response has content?                 │
│ └─► Use fallback response template   │ └─► Use Claude insights               │
│     { "insights": "Unable to analyze"}│                                       │
└──────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
VALIDATION LAYER (🟦 TEAL):
┌──────────────────────────────────────────────────────────────────────────────┐
│ ❌ ERROR PATHS                        │  ✅ SUCCESS PATH                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ Pydantic validation fails?           │ All fields valid?                     │
│ └─► HTTP 500 Server Error            │ └─► Serialize to JSON                 │
│     { "error": "Response validation" }│                                       │
│                                      │                                       │
│ Scores out of 0-100 range?           │ Scores 0-100?                         │
│ └─► HTTP 500 Server Error            │ └─► Clamp values                      │
│     { "error": "Invalid score range" }│    min(max(val, 0), 100)             │
│                                      │                                       │
│ Status not in enum list?             │ Status is [Low|Fair|Good|High]?       │
│ └─► HTTP 500 Server Error            │ └─► Continue                          │
│     { "error": "Invalid status" }    │                                       │
└──────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
╔════════════════════════════════════════════════════════════════════════════════╗
║ RESPONSE: HTTP 200 OK                                                          ║
║ { "success": true, "data": {...}, "error": null }                             ║
╚════════════════════════════════════════════════════════════════════════════════╝


HTTP STATUS CODE SUMMARY (Reference table):
┌──────────────────────────────────────────────────────────────────────────────┐
│ CODE │ MEANING              │ WHEN TO USE                                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ 200  │ OK                   │ Request successful, data returned              │
├──────────────────────────────────────────────────────────────────────────────┤
│ 400  │ Bad Request          │ Invalid input (missing/invalid params)         │
├──────────────────────────────────────────────────────────────────────────────┤
│ 401  │ Unauthorized         │ Invalid/missing authentication token           │
├──────────────────────────────────────────────────────────────────────────────┤
│ 403  │ Forbidden            │ Authenticated but not authorized for resource  │
├──────────────────────────────────────────────────────────────────────────────┤
│ 404  │ Not Found            │ Resource doesn't exist (user not found)        │
├──────────────────────────────────────────────────────────────────────────────┤
│ 429  │ Too Many Requests    │ Rate limit exceeded (from Claude API)          │
├──────────────────────────────────────────────────────────────────────────────┤
│ 500  │ Internal Server Error│ Backend error (validation, parsing, logic)     │
├──────────────────────────────────────────────────────────────────────────────┤
│ 503  │ Service Unavailable  │ Server temporarily down                        │
├──────────────────────────────────────────────────────────────────────────────┤
│ 504  │ Gateway Timeout      │ Database or AI service not responding          │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 📋 DRAW.IO QUICK REFERENCE

### Colors to Use:
```css
🟢 Mobile App:      #90EE90 (Light Green)
🔵 API Routes:      #87CEEB (Sky Blue)
🔴 Services:        #FFB6C6 (Light Pink)
🟠 Database:        #FFB347 (Peach Orange)
🟣 Claude AI:       #DDA0DD (Plum Purple)
🟦 Pydantic/Models: #80CED7 (Dark Turquoise)
🟡 Processing:      #FFFFE0 (Light Yellow)
⚪ Response:        #FFFFFF (White)
```

### Box Styles:
- **Rounded Rectangle**: For services, endpoints, main processes
- **Rectangle**: For data storage, simple operations
- **Diamond**: For decision points / branching
- **Parallelogram**: For input/output data
- **Cylinder**: For databases

### Font Sizes:
- **Headers**: 14pt Bold
- **Box Titles**: 12pt Bold
- **Box Content**: 10pt Regular
- **Labels on arrows**: 9pt Italic

### Line Styles:
- **Solid**: Primary flow
- **Dashed**: Optional/conditional flow
- **Arrow styles**: Open arrow (→) for data flow

---

## ✅ READY TO DRAW IN DRAW.IO!

You now have:
✅ All 5 diagrams with visual layout
✅ Color scheme
✅ Exact box positions
✅ Data flow arrows
✅ Text content for each element
✅ HTTP status codes
✅ Error paths

**Start with Diagram 1 (System Overview) - it's the foundation!**

