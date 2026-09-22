# CORRECT: 6 LIFE JOURNEYS → ENDPOINTS (From UI Mockups)

## ✅ THE ACTUAL 6 LIFE JOURNEYS (From Your UI)

```
1. Beauty & Radiance
2. Cycle & Fertility  
3. Athlete Performance
4. Pregnancy & Postpartum
5. Menopause & Vitality
6. Lifelong Thriving
```

---

## 📱 ANALYZING YOUR UI MOCKUPS

### **LIFE JOURNEY 1: Beauty & Radiance**

UI Shows: 3 Tabs
- Today tab (current skin score + metrics)
- History tab (30-day comparative analysis)
- Correlations tab (sleep correlation + cycle correlation)

**Endpoint needed: 1**
```
GET /api/v1/skin-scan/overview/{user_id}

Returns: {
  today: {...},
  history: [...],
  correlations: {...}
}
```

---

### **LIFE JOURNEY 2: Cycle & Fertility**

UI Shows: 2 Tabs
- Tracking Fertility tab (cycle overview graph, fertile window prediction, hormone trends, insights)
- Avoiding Pregnancy tab (contraceptive guidance)

**Endpoints needed: 1-2**
```
Option A (1 endpoint):
GET /api/v1/cycle-engine/overview/{user_id}
  Returns: {
    cycle_overview,
    fertile_window,
    hormone_trends,
    insights,
    contraceptive_mode: boolean
  }

Option B (2 endpoints):
GET /api/v1/cycle-engine/overview/{user_id}
GET /api/v1/cycle-engine/calendar/{user_id}?month=2026-09
```

⚠️ **Need clarification:** Does frontend need calendar endpoint or is overview enough?

---

### **LIFE JOURNEY 3: Athlete Performance**

UI Shows: 1 Screen
- Readiness score (84/100)
- Peak body status
- Training capacity
- Active recovery
- Sleep quality
- Fatigue alerts
- Cycle-based training phases
- Focus recommendations

**Endpoint needed: 1**
```
GET /api/v1/athlete-performance/overview/{user_id}

Returns: {
  readiness_score,
  peak_status,
  training_capacity,
  active_recovery,
  sleep_quality,
  fatigue_alerts,
  cycle_phase_training,
  focus_recommendations
}
```

---

### **LIFE JOURNEY 4: Pregnancy & Postpartum**

UI Shows: 3 Tabs
- Pregnancy tab (trimester info, week milestones, care checklist)
- Postpartum tab (recovery progress, recovery metrics, mental health, care community)
- Support tab (support groups, care community, loss support)

**Endpoint needed: 1**
```
GET /api/v1/pregnancy/overview/{user_id}

Returns: {
  pregnancy_mode: boolean,
  current_stage: "pregnancy" | "postpartum",
  trimester_info: {...},
  week_milestones: [...],
  recovery_metrics: {...},
  mental_health_check_in: {...},
  care_community: [...],
  support_resources: [...]
}
```

---

### **LIFE JOURNEY 5: Menopause & Vitality**

UI Shows: 3 Tabs
- Symptoms tab (transition stage tracker, vasomotor tracker, intimate & urinary health)
- Insights tab (symptom matrix insights showing correlations)
- Export tab (clinical consultation report PDF export)

**Endpoint needed: 1**
```
GET /api/v1/menopause/overview/{user_id}

Returns: {
  transition_stage,
  symptoms: {
    vasomotor: [...],
    intimate_health: {...},
    urinary_health: {...}
  },
  symptom_matrix_insights: {...},
  export_ready: boolean
}
```

---

### **LIFE JOURNEY 6: Lifelong Thriving**

UI Shows: 3 Tabs
- Vitality tab (Multi-year vitality index, vitality trends, vitality component breakdown)
- Life Arc tab (Timeline of health milestones, historical tracking)
- Reminders tab (Preventative health reminders, upcoming screenings, overdue tests)

**Endpoint needed: 1**
```
GET /api/v1/lifelong-thriving/overview/{user_id}

Returns: {
  vitality_index,
  vitality_trends: [...],
  vitality_components: {...},
  life_arc_timeline: [...],
  preventative_reminders: [...],
  upcoming_screenings: [...],
  overdue_tests: [...]
}
```

---

## 📊 ENDPOINT COUNT: REVISED

```
LIFE JOURNEY                    ENDPOINTS    Total
──────────────────────────────────────────────────
1. Beauty & Radiance            1            1
2. Cycle & Fertility            1-2          1-2 ⚠️
3. Athlete Performance          1            1
4. Pregnancy & Postpartum       1            1
5. Menopause & Vitality         1            1
6. Lifelong Thriving            1            1
                                      ─────────────
                            TOTAL:     7-8 endpoints
```

**Question for Team Lead:**
Does Cycle & Fertility need a separate calendar endpoint or is overview with tabs enough?

---

## 🎯 FINAL ENDPOINT LIST

```
1. GET /api/v1/skin-scan/overview/{user_id}
   → Beauty & Radiance (1 endpoint, 3 tabs)

2. GET /api/v1/cycle-engine/overview/{user_id}
   → Cycle & Fertility (1 endpoint, 2 tabs)
   
3. [OPTIONAL] GET /api/v1/cycle-engine/calendar/{user_id}?month=2026-09
   → Cycle Calendar (only if frontend needs separate calendar view)

4. GET /api/v1/athlete-performance/overview/{user_id}
   → Athlete Performance (1 endpoint)

5. GET /api/v1/pregnancy/overview/{user_id}
   → Pregnancy & Postpartum (1 endpoint, 3 tabs)

6. GET /api/v1/menopause/overview/{user_id}
   → Menopause & Vitality (1 endpoint, 3 tabs)

7. GET /api/v1/lifelong-thriving/overview/{user_id}
   → Lifelong Thriving (1 endpoint, 3 tabs)
```

**Total: 7 main endpoints (possibly 8 with optional calendar)**

---

## 📊 DATA MAPPING: UI Screens → Endpoints

```
SCREEN                          DATA SOURCE              ENDPOINT
─────────────────────────────────────────────────────────────────────

Beauty & Radiance
  Today tab                  skin_scans (today)         /skin-scan/overview
  History tab                skin_scans (30 days)       /skin-scan/overview
  Correlations tab           terra_activity_data        /skin-scan/overview
                            menstrual_cycles
                            skin_scans

Cycle & Fertility
  Tracking Fertility         menstrual_cycles           /cycle-engine/overview
                            bbt_logs
                            opk_logs
  Avoiding Pregnancy         contraceptive_modes        /cycle-engine/overview
                            (stored in menstrual_cycles)

Athlete Performance
  All metrics                terra_activity_data        /athlete-performance/overview
                            menstrual_cycles
                            bbt_logs

Pregnancy & Postpartum
  Pregnancy tab              health_logs                /pregnancy/overview
  Postpartum tab             health_logs
  Support tab                care_community table

Menopause & Vitality
  Symptoms tab               health_logs                /menopause/overview
                            hormone_snapshots
  Insights tab               health_logs
  Export tab                 health_logs

Lifelong Thriving
  Vitality tab               health_logs                /lifelong-thriving/overview
  Life Arc tab               health_logs (history)
  Reminders tab              preventative_screenings
```

---

## ✅ CORRECTED SUMMARY

| # | Life Journey | UI Tabs | Endpoints | Main Data |
|---|---|---|---|---|
| 1 | Beauty & Radiance | Today, History, Correlations | 1 | skin_scans, sleep, cycle |
| 2 | Cycle & Fertility | Tracking, Avoiding | 1-2* | menstrual_cycles, BBT, OPK |
| 3 | Athlete Performance | (single view) | 1 | activity, BBT, cycle |
| 4 | Pregnancy & Postpartum | Pregnancy, Postpartum, Support | 1 | health_logs, tests |
| 5 | Menopause & Vitality | Symptoms, Insights, Export | 1 | health_logs, hormones |
| 6 | Lifelong Thriving | Vitality, Life Arc, Reminders | 1 | health_logs, screenings |
| | **TOTAL** | | **7-8*** | |

*Cycle might need calendar endpoint
***Depends on whether calendar is separate endpoint

---

## 🎓 KEY DIFFERENCES FROM MY WRONG ASSUMPTION

| What I Said | What's Actually True |
|---|---|
| 6 journeys = 8 endpoints | 6 journeys = 7-8 endpoints |
| Health Trends & Mood is a feature | Lifelong Thriving is the actual feature |
| Health Trends has 2 endpoints | Lifelong Thriving has 1 endpoint |
| | Most features use tabs, not separate endpoints |

---

## ⚠️ QUESTIONS FOR TEAM LEAD

1. **Cycle & Fertility:** Does the calendar need a separate endpoint, or can it be in the overview response?

2. **Tab Navigation:** Should each tab be a separate API call or one call that returns all tabs?

3. **Lifelong Thriving:** What is the data source for "Preventative Health Reminders"? (New table needed?)

4. **Pregnancy Support:** Is the "Care Community" tab using a separate community table?

---

