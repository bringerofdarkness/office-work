# 📊 Data Workflow Diagram - Pulse_E
## Figma-Ready Simple Layout

### 🎨 **BASIC FLOW DIAGRAM**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           📱 MOBILE APP                                      │
│                     (User Interface / Client)                                │
└────────────────────────────┬────────────────────────────────────────────────┘
                             │ HTTP POST /endpoint
                             ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                      🔌 FASTAPI ROUTES                                       │
│                   (Request Validation)                                       │
└────────────────────────────┬────────────────────────────────────────────────┘
                             │ Call Service Function
                    ┌────────┴────────┐
                    ▼                 ▼
        ┌──────────────────┐  ┌──────────────────┐
        │ 💾 MYSQL         │  │ ⚙️ SERVICE      │
        │ DATABASE         │  │ LAYER           │
        │ (AWS RDS)        │  │ (Business Logic)│
        │                  │  │                 │
        │ • skin_scans     │  │ • Fetch data    │
        │ • cycles         │  │ • Calculate     │
        │ • terra_activity │  │ • Call Claude   │
        │ • health_logs    │  │                 │
        │ • pregnancy_info │  │                 │
        └──────────────────┘  └────────┬────────┘
                    ▲                   │
                    └───────────────────┘
                            ▼
        ┌──────────────────────────────┐
        │  🤖 CLAUDE AI API            │
        │  (Insights Generation)       │
        │                              │
        │  • Claude Vision (images)    │
        │  • Claude Opus (insights)    │
        └──────────────────────────────┘
                     ▲
                     │ AI Response
                     │
        ┌────────────┴──────────────┐
        ▼                           ▼
┌──────────────────────┐  ┌─────────────────────────────┐
│  ✅ PYDANTIC        │  │   Validation & Parsing      │
│  MODELS             │  │   (Structured Output)       │
│                     │  │                             │
│  Response Format:   │  │  • Extract scores 0-100     │
│  • user_id         │  │  • Parse timestamps         │
│  • today           │  │  • Extract insights         │
│  • history         │  │  • Format statuses          │
│  • correlations    │  │                             │
│  • ai_insights     │  │                             │
└──────────────────────┘  └─────────────────────────────┘
        │
        └─────────────┬────────────────────┐
                      ▼                    ▼
            ┌──────────────────┐  ┌─────────────────────┐
            │  JSON Response   │  │  HTTP 200 + Data    │
            │  Serialization   │  │                     │
            └──────────────────┘  └─────────────────────┘
                      │
                      ▼
        ┌─────────────────────────────┐
        │  📱 MOBILE APP              │
        │  (Display to User)          │
        └─────────────────────────────┘
```

---

### 🎯 **6 LIFE JOURNEYS - ENDPOINT ARCHITECTURE**

```
                    📱 MOBILE APP
                         │
    ┌────────────────────┬────────────────────┐
    │                    │                    │
    ▼                    ▼                    ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Beauty &   │    │   Cycle &   │    │   Athlete   │
│  Radiance   │    │  Fertility   │    │ Performance │
└──────┬──────┘    └──────┬──────┘    └──────┬──────┘
       │                  │                   │
    POST /               POST /              POST /
  beauty-             cycle-              athlete-
  overview             overview           overview
       │                  │                   │
       ▼                  ▼                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  beauty_    │    │  cycle_     │    │  athlete_   │
│  service    │    │  service    │    │  service    │
└──────┬──────┘    └──────┬──────┘    └──────┬──────┘
       │                  │                   │
       ├─ skin_scans      ├─ cycles          ├─ terra_
       ├─ terra_data      ├─ bbt_logs        ├─ activity
       ├─ sleep corr.     └─ opk_logs        │  _data
       └─ cycle corr.                        └─ cycles


    ┌────────────────────┬────────────────────┐
    │                    │                    │
    ▼                    ▼                    ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ Pregnancy & │    │ Menopause & │    │  Lifelong   │
│ Postpartum  │    │  Vitality   │    │  Thriving   │
└──────┬──────┘    └──────┬──────┘    └──────┬──────┘
       │                  │                   │
    POST /              POST /              POST /
  pregnancy-          menopause-          thriving-
  overview             overview           overview
       │                  │                   │
       ▼                  ▼                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  pregnancy_ │    │ menopause_  │    │  thriving_  │
│  service    │    │  service    │    │  service    │
└──────┬──────┘    └──────┬──────┘    └──────┬──────┘
       │                  │                   │
       ├─ pregnancy      ├─ symptoms         ├─ 6-year
       ├─ health_logs    ├─ health_logs      │  history
       └─ terra_data     └─ terra_data       ├─ health_logs
                                             └─ vitality_data

                            ▼
                    🤖 CLAUDE AI API
                (Generates All Insights)
```

---

### 📊 **SINGLE REQUEST-RESPONSE CYCLE**

```
USER REQUEST                    SERVER PROCESSING                     USER RESPONSE
═════════════════════════════════════════════════════════════════════════════════

📱 User opens                   
"Beauty & Radiance"             
         │                       
         ├─► POST /beauty-overview
         │   {                  
         │     user_id: 123,   ──────────────────┐
         │     days: 30              │           │
         │   }                       │           ▼
         │                          ▼      ┌──────────────────┐
         │                   ⚙️ beauty_   │ Query Database:  │
         │                   service()   │ • skin_scans     │
         │                          │     │ • terra_data     │
         │                          │     │ • last 30 days   │
         │                          ▼     └──────────────────┘
         │                   🤖 Claude:
         │                   "Analyze skin data + sleep
         │                    + cycle correlation"
         │                          │
         │                          ▼
         │                   ✅ Parse Results:
         │                   • Score: 78/100
         │                   • Status: "Good"
         │                   • Insights: {...}
         │                          │
         │                          ▼
         │                   Return Pydantic Model
         │                   {
         │                     user_id: 123,
         │                     today: {...},
         │                     history: [...],
         │                     correlations: {...},
         │                     ai_insights: {...}
         │                   }
         │
         └──◄──────────────────────────────────────┐
             JSON Response                         │
             HTTP 200                              │
                                                   ▼
                                          📱 Display Results:
                                          • Today's Score
                                          • 30-Day History
                                          • Sleep Impact
                                          • Cycle Impact
                                          • AI Tips
```

---

## 📊 **Data Table Dependencies**

| Life Journey | Primary Table | Secondary Tables | AI Role |
|---|---|---|---|
| **Beauty & Radiance** | `skin_scans` | `terra_activity_data` (sleep) | Analyze image + correlate sleep |
| **Cycle & Fertility** | `menstrual_cycles` | `bbt_logs`, `opk_logs`, `terra_activity_data` | Generate cycle insights |
| **Athlete Performance** | `terra_activity_data` | `menstrual_cycles` | Training recommendations by cycle |
| **Pregnancy & Postpartum** | `pregnancy_info` | `health_logs`, `terra_activity_data` | Trimester-specific advice |
| **Menopause & Vitality** | `health_logs` | `menstrual_cycles`, `terra_activity_data` | Symptom analysis + insights |
| **Lifelong Thriving** | `health_logs` | 6-year history tables | Preventative health analysis |

---

## 🎯 **Key Design Principles**

```
1. REQUEST FLOW:
   Mobile App → FastAPI Route → Service → DB + Claude → Pydantic Model → JSON Response

2. DATA HANDLING:
   ✅ All queries are parameterized (prevent SQL injection)
   ✅ Timestamps in ISO 8601 format
   ✅ Scores normalized to 0-100 integer range
   ✅ Status fields: ["Low", "Fair", "Good", "High"]

3. RESPONSE STRUCTURE:
   {
     "user_id": "UUID",
     "today": { metrics for today },
     "history": [ { date, metrics } ],
     "correlations": { correlation_name: value },
     "ai_insights": { structured Claude output },
     "status": "success"
   }

4. ERROR HANDLING:
   ✅ HTTPException(400) - invalid request
   ✅ HTTPException(500) - server error
   ✅ Response validation via Pydantic
```

---

## 🎨 **HOW TO RECREATE IN FIGMA (Simple Steps)**

### **COLOR SCHEME:**
```
🟢 Green (#E8F5E9)    → Mobile App / User
🔵 Blue (#E3F2FD)     → FastAPI / Routes
🟣 Purple (#F3E5F5)   → Claude AI
🟠 Orange (#FFF3E0)   → MySQL Database
🔴 Pink (#FCE4EC)     → Service Layer
🟦 Teal (#E0F2F1)     → Pydantic / Validation
```

### **LAYOUT IN FIGMA:**

**Diagram 1: Basic Flow** (Top to Bottom)
1. Create rectangle: "Mobile App" → Green, centered
2. Arrow down (solid line, arrow style)
3. Create rectangle: "FastAPI Routes" → Blue
4. Arrow down (splits into 2)
5. Left: Rectangle "MySQL Database" → Orange
6. Right: Rectangle "Service Layer" → Pink
7. From Service → Arrow to "Claude AI" → Purple
8. Back to Service → Arrow to "Pydantic Models" → Teal
9. Up to API → Arrow to Mobile

**Diagram 2: 6 Life Journeys** (Left to Right)
1. Top: "Mobile App" → Green
2. Below (3 rows, 2 columns):
   - Row 1: Beauty, Cycle, Athlete
   - Row 2: Pregnancy, Menopause, Lifelong
3. Each connects to service (pink box below)
4. Each service connects to DB tables (orange)
5. All converge to "Claude AI" (purple) at bottom

**Diagram 3: Request Cycle** (Left to Right Timeline)
1. Left column: "USER REQUEST"
   - POST endpoint
   - with params
2. Middle column: "SERVER PROCESSING"
   - Service called
   - Database queried
   - Claude generates
   - Response parsed
3. Right column: "USER RESPONSE"
   - JSON returned
   - Displayed on app

### **SHAPES & TEXT:**
- All boxes: Rounded corners (8px radius)
- Arrow width: 2px
- Text: 12px font, center aligned
- Box size: 120px × 60px (can adjust)
- Arrow labels: Add small text next to arrows

---

## 📌 **WHAT TO SHOW TEAM LEAD:**

✅ This document shows your GitHub repo  
✅ Shows simplified flow (not code)  
✅ Shows all 6 Life Journeys architecture  
✅ Shows request-response cycle  
✅ Ready to copy into Figma  

**Say to Team Lead:**
> "Here's the data workflow diagram showing how requests flow through our system. I can recreate this in Figma with exact colors/sizes. Should I create it now?"

---

Generated: 2026-09-22 | For: Team Alignment
