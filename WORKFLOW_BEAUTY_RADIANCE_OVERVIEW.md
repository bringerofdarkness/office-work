# WORKFLOW DESIGN: Beauty & Radiance Overview Endpoint

## 📋 DOCUMENT METADATA
- **Task:** Beauty & Radiance Overview Implementation
- **Assigned To:** AI Engineer (Shahrul)
- **Date:** 2026-09-22
- **Status:** Design Phase
- **Priority:** High (MVP Feature)

---

## 1️⃣ WHAT IS A WORKFLOW?

A **workflow** is a step-by-step document that shows:
- **What** needs to happen
- **In what order** it happens
- **Who/What** is responsible for each step
- **Where** data comes from and goes to
- **What** could go wrong and how to handle it

Think of it like **cooking instructions** - you need to follow steps in order, not randomly.

---

## 2️⃣ BUSINESS REQUIREMENT

### User Story:
> "As a female health app user, I want to see a beauty & radiance overview dashboard showing:
> - My current skin health score
> - 30-day skin score trends
> - How my sleep affects my skin
> - How my menstrual cycle affects my skin
> - AI-powered personalized insights"

### Success Criteria:
- ✅ Endpoint responds in < 2 seconds
- ✅ Shows accurate skin trends
- ✅ Correlations are statistically meaningful
- ✅ Works for new users (no data)
- ✅ Handles missing data gracefully

---

## 3️⃣ SYSTEM ARCHITECTURE OVERVIEW

```
┌─────────────────────────────────────────────────────────────┐
│                     MOBILE APP / FRONTEND                    │
│               GET /api/v1/skin-scan/overview/{user_id}       │
└───────────────────────────────────┬─────────────────────────┘
                                    │
                    ┌───────────────▼──────────────┐
                    │    FASTAPI ROUTE HANDLER     │
                    │ (ai/routes/skin_scan_routes) │
                    └───────────────┬──────────────┘
                                    │
                    ┌───────────────▼──────────────────┐
                    │   SERVICE LAYER (ORCHESTRATION) │
                    │ (ai/services/skin_scan_service) │
                    └───────────────┬──────────────────┘
                                    │
                ┌───────────────────┼───────────────────┐
                │                   │                   │
        ┌───────▼────────┐  ┌──────▼───────┐  ┌──────▼──────┐
        │  DATABASE QUERIES│  │ CALCULATIONS │  │ CLAUDE API  │
        │ (ai/utils/db.py)  │  │(correlation) │  │ (insights)  │
        └───────┬────────┘  └──────┬───────┘  └──────┬──────┘
                │                  │                  │
        ┌───────▼────────────────────────────────────▼──────┐
        │          PYDANTIC MODEL VALIDATION                │
        │    (ai/models/skin_scan_models.py)               │
        └───────┬──────────────────────────────────────────┘
                │
        ┌───────▼──────────────┐
        │  JSON RESPONSE       │
        │  (200 / 400 / 500)   │
        └──────────────────────┘
```

---

## 4️⃣ COMPLETE WORKFLOW: STEP BY STEP

### 🟦 PHASE 1: REQUEST RECEIVED

```
Step 1.1: Client sends request
  INPUT:  GET /api/v1/skin-scan/overview/2
  WHERE:  2 = user_id
  
Step 1.2: Route handler receives request
  HANDLER: skin_scan_routes.get_beauty_radiance_overview()
  ACTION:  Validate user_id is integer and > 0
  ERROR HANDLING:
    - If user_id invalid → 400 Bad Request
    - If user_id < 0 → 422 Unprocessable Entity
```

### 🟦 PHASE 2: DATA GATHERING (Database Queries)

```
Step 2.1: Call service function
  CALL: await skin_scan_service.get_beauty_radiance_overview(user_id=2)
  RESPONSIBILITY: Orchestrate all data retrieval

Step 2.2: Fetch today's skin scan
  QUERY: SELECT * FROM skin_scans 
         WHERE user_id = 2 AND DATE(created_at) = TODAY
         ORDER BY created_at DESC LIMIT 1
  
  RESULT: {
    "id": 123,
    "overall_score": 82,
    "hydration_score": 85,
    "redness_score": 45,
    "texture_score": 78,
    "glow_index": 88,
    "pore_health_score": 72,
    "elasticity_score": 80,
    "hydration_status": "Good",
    "redness_status": "Fair",
    "texture_status": "Good",
    "glow_status": "High",
    "pore_health_status": "Fair",
    "elasticity_status": "Good",
    "neumera_insight": "Your skin is glowing today!",
    "created_at": "2026-09-22T10:30:00Z"
  }
  
  ERROR HANDLING:
    - If no today's scan → Use yesterday's or latest available
    - If no scans at all → Return empty object with defaults

Step 2.3: Fetch 30-day skin history
  QUERY: SELECT id, overall_score, created_at FROM skin_scans
         WHERE user_id = 2 
         AND created_at >= NOW() - INTERVAL 30 DAY
         ORDER BY created_at ASC
  
  RESULT: [
    {"date": "2026-08-24", "score": 72},
    {"date": "2026-08-25", "score": 75},
    {"date": "2026-08-26", "score": 73},
    ...
    {"date": "2026-09-22", "score": 82}
  ]
  
  COUNT: How many days of data? (e.g., 15 days, 30 days, 5 days?)
  
  ERROR HANDLING:
    - If < 5 days of data → Flag as "insufficient data"
    - Return empty history list if no data

Step 2.4: Fetch sleep data (30 days)
  QUERY: SELECT DATE(data_generated_at) as date,
                JSON_EXTRACT(payload, '$.data[0].scores.sleep') as sleep_hours
         FROM terra_activity_data
         WHERE user_id = 2
         AND type = 'daily'
         AND JSON_EXTRACT(payload, '$.data[0].scores.sleep') IS NOT NULL
         ORDER BY data_generated_at ASC
  
  RESULT: [
    {"date": "2026-08-24", "sleep_hours": 7.5},
    {"date": "2026-08-25", "sleep_hours": 6.8},
    {"date": "2026-08-26", "sleep_hours": 8.2},
    ...
  ]
  
  ERROR HANDLING:
    - If user has NO sleep data → Return empty list
    - Correlation will be skipped

Step 2.5: Fetch cycle phase (current)
  QUERY: SELECT id, current_phase, current_cycle_day, 
                cycle_length, fertile_start_day, fertile_end_day
         FROM menstrual_cycles
         WHERE user_id = 2 AND is_completed = 0
         LIMIT 1
  
  RESULT: {
    "id": 45,
    "current_phase": "follicular",
    "current_cycle_day": 8,
    "cycle_length": 28,
    "fertile_start_day": 12,
    "fertile_end_day": 16
  }
  
  ERROR HANDLING:
    - If no active cycle → Use last completed cycle
    - If no cycles at all → Return empty object
```

### 🟦 PHASE 3: CORRELATIONS (Statistical Calculations)

```
Step 3.1: Calculate Sleep ↔ Skin Correlation
  
  ONLY IF: sleep_data.length >= 5 AND history.length >= 5
  
  ALGORITHM: Pearson Correlation Coefficient
  INPUT:
    - sleep_hours array: [7.5, 6.8, 8.2, 7.1, 6.5, ...]
    - skin_scores array: [72, 75, 73, 80, 68, ...]
  
  FORMULA:
    r = Σ((sleep - sleep_mean) * (skin - skin_mean)) / 
        √(Σ(sleep - sleep_mean)² * Σ(skin - skin_mean)²)
  
  INTERPRETATION:
    - r > 0.5  → Strong positive correlation ("Better sleep = Better skin")
    - r > 0.3  → Moderate positive ("Sleep helps skin")
    - r > 0    → Weak positive ("Sleep slightly helps")
    - r = 0    → No correlation
    - r < 0    → Negative correlation ("More sleep = Worse skin" - unusual)
  
  OUTPUT: {
    "coefficient": 0.67,
    "interpretation": "Strong positive",
    "summary": "Your skin improves significantly when you get 7+ hours of sleep",
    "data_points": 15
  }
  
  ERROR HANDLING:
    - If < 5 data points → return null for correlation
    - If all values same → return 0 coefficient

Step 3.2: Calculate Cycle ↔ Skin Correlation
  
  ONLY IF: history.length >= 2 cycles
  
  ALGORITHM: Phase-based grouping
  INPUT:
    - For each skin scan, find which cycle phase it was in
    - Group scores by phase: menstrual, follicular, ovulatory, luteal
  
  CALCULATION: Average score per phase
    - Menstrual: avg = 70.2
    - Follicular: avg = 78.5
    - Ovulatory: avg = 82.1
    - Luteal: avg = 74.3
  
  OUTPUT: {
    "highest_phase": "ovulatory",
    "lowest_phase": "menstrual",
    "coefficient": 0.58,
    "interpretation": "Moderate positive",
    "summary": "Your skin looks best during ovulation phase",
    "phase_breakdown": {
      "menstrual": 70.2,
      "follicular": 78.5,
      "ovulatory": 82.1,
      "luteal": 74.3
    }
  }
  
  ERROR HANDLING:
    - If insufficient data → return null
    - If phase data missing → skip phase calculation

Step 3.3: Calculate Trend (Last 7 days)
  
  ALGORITHM: Simple trend analysis
  INPUT: Last 7 days of skin scores
  
  CALCULATION:
    - Day 1-4 average: 75
    - Day 5-7 average: 82
    - Trend: (82 - 75) / 75 * 100 = +9.3% (IMPROVING)
  
  OUTPUT: {
    "direction": "improving",
    "percentage_change": 9.3,
    "summary": "Your skin is improving this week"
  }
```

### 🟦 PHASE 4: AI INSIGHTS (Claude API Call)

```
Step 4.1: Prepare insight prompt
  
  CONTEXT: {
    "user_id": 2,
    "current_score": 82,
    "trend": "improving (+9.3%)",
    "sleep_correlation": 0.67,
    "cycle_phase": "follicular",
    "age": 28,
    "skin_type": "combination",
    "previous_insights": [...]
  }
  
  SYSTEM PROMPT:
    "You are a dermatology AI expert. Analyze the user's skin data 
     and provide personalized, actionable insights. Be specific, 
     encouraging, and scientific."

Step 4.2: Call Claude API
  
  API_CALL: ClaudeLLM.chat(
    system_prompt = DERMATOLOGY_SYSTEM_PROMPT,
    user_message = f"""
      Based on this skin data:
      - Overall Score: 82/100
      - Sleep correlation: 0.67
      - Cycle phase: follicular (day 8)
      - Trend: improving
      
      Provide 2-3 actionable insights.
    """,
    model = "claude-3-5-sonnet-20241022",
    max_tokens = 300
  )
  
  RESPONSE: {
    "content": "Your skin is doing excellently! The high correlation 
               between sleep and skin health suggests maintaining your 
               7+ hour sleep schedule. During follicular phase, consider 
               focusing on hydration to prepare for ovulation.",
    "usage": {"input_tokens": 150, "output_tokens": 87}
  }

Step 4.3: Parse Claude response
  
  ACTION: Extract text from Claude response
  OUTPUT: insight_text = "Your skin is doing excellently!..."
  
  ERROR HANDLING:
    - If Claude API fails → Use fallback insight
    - If timeout > 10s → Return cached insight
    - If rate limited → Retry with exponential backoff
```

### 🟦 PHASE 5: BUILD RESPONSE OBJECT

```
Step 5.1: Create response data structure
  
  RESPONSE_OBJECT = BeautyRadianceOverview(
    user_id=2,
    
    overall_score=82,
    status="Good",
    description="Your skin is healthy and improving",
    
    today={
      "score": 82,
      "hydration_score": 85,
      "hydration_status": "Good",
      "redness_score": 45,
      "redness_status": "Fair",
      "texture_score": 78,
      "texture_status": "Good",
      "glow_index": 88,
      "glow_status": "High",
      "pore_health_score": 72,
      "pore_health_status": "Fair",
      "elasticity_score": 80,
      "elasticity_status": "Good",
      "timestamp": "2026-09-22T10:30:00Z"
    },
    
    history=[
      {"date": "2026-08-24", "score": 72},
      {"date": "2026-08-25", "score": 75},
      ...
      {"date": "2026-09-22", "score": 82}
    ],
    
    sleep_correlation={
      "coefficient": 0.67,
      "interpretation": "Strong positive",
      "summary": "Your skin improves significantly when you get 7+ hours of sleep",
      "recommendation": "Maintain consistent sleep schedule"
    },
    
    cycle_correlation={
      "highest_phase": "ovulatory",
      "lowest_phase": "menstrual",
      "coefficient": 0.58,
      "summary": "Your skin looks best during ovulation phase"
    },
    
    ai_insights={
      "generated_by": "Claude 3.5 Sonnet",
      "content": "Your skin is doing excellently!...",
      "confidence": 0.92
    },
    
    generated_at="2026-09-22T10:30:00Z"
  )

Step 5.2: Validate response model
  
  VALIDATION: Pydantic model checks:
    - overall_score: 0 ≤ x ≤ 100
    - status: one of ["Low", "Fair", "Good", "High"]
    - user_id: > 0
    - timestamps: valid ISO format
    - correlation coefficients: -1 ≤ x ≤ 1
  
  ERROR HANDLING:
    - If validation fails → Log error, return 500
    - If data missing → Use None or default values
```

### 🟦 PHASE 6: SEND RESPONSE

```
Step 6.1: Return HTTP Response
  
  STATUS: 200 OK
  CONTENT_TYPE: application/json
  
  BODY: {
    "user_id": 2,
    "overall_score": 82,
    "status": "Good",
    ...
  }

Step 6.2: Handle errors
  
  IF database error → 500 Internal Server Error
  IF invalid user_id → 400 Bad Request
  IF user not found → 404 Not Found
  IF Claude API fails → 503 Service Unavailable
  IF timeout → 504 Gateway Timeout
```

---

## 5️⃣ DATA FLOW DIAGRAM

```
┌─────────────────────────────────────────────────────────────────┐
│                    1. REQUEST RECEIVED                           │
│              GET /api/v1/skin-scan/overview/2                   │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│        2. DATA GATHERING (Parallel Database Queries)            │
│                                                                  │
│    Today's Skin  ─┐                                             │
│    30-day        ├─→ Collected Data                             │
│    Sleep Data    ├─→ Combined & Formatted                       │
│    Cycle Phase   ─┘                                             │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│      3. CALCULATE CORRELATIONS (Local Processing)               │
│                                                                  │
│    Sleep ↔ Skin (Pearson) ─┐                                   │
│    Cycle ↔ Skin (Phase)    ├─→ Correlation Objects              │
│    Trend Analysis          ─┘                                   │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│      4. CALL CLAUDE API (External Service)                      │
│                                                                  │
│    System Prompt + User Context ─→ Claude API ─→ Insights      │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│      5. BUILD RESPONSE OBJECT (Data Structuring)                │
│                                                                  │
│    BeautyRadianceOverview Model ─→ Validation ─→ JSON          │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│      6. SEND RESPONSE (HTTP)                                    │
│                                                                  │
│    Status: 200 OK                                              │
│    Body: {...all data...}                                       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 6️⃣ IMPLEMENTATION CHECKLIST

### Phase 1: Models (ai/models/skin_scan_models.py)
```
☐ Create Correlation model
☐ Create HistoryItem model  
☐ Create BeautyRadianceOverview model
☐ Add field validators
☐ Document with examples
```

### Phase 2: Database Queries (ai/utils/db.py)
```
☐ get_skin_scans(user_id, days=30)
☐ get_sleep_data(user_id, days=30)
☐ get_current_cycle(user_id)
☐ Handle NULL/missing data
☐ Test each query manually
```

### Phase 3: Utility Functions (ai/utils/correlation.py - NEW)
```
☐ Create pearson_correlation(x, y)
☐ Create analyze_phase_correlation(scans, cycles)
☐ Create calculate_trend(history)
☐ Error handling for edge cases
☐ Unit tests
```

### Phase 4: Service Layer (ai/services/skin_scan_service.py)
```
☐ get_beauty_radiance_overview(user_id)
☐ Orchestrate all functions
☐ Handle timeouts
☐ Implement caching if needed
☐ Fallback strategies
```

### Phase 5: Route Handler (ai/routes/skin_scan_routes.py)
```
☐ @router.get("/overview/{user_id}")
☐ Parameter validation
☐ Error handling
☐ Response model validation
☐ API documentation
```

### Phase 6: Testing
```
☐ Unit tests for each function
☐ Integration tests (end-to-end)
☐ Error scenario tests
☐ Performance tests
☐ Edge case tests
```

### Phase 7: Documentation
```
☐ Code comments
☐ Docstrings
☐ API documentation
☐ README update
```

---

## 7️⃣ ERROR HANDLING STRATEGY

```
┌─────────────────────────────────────┐
│  TRY: Execute all steps             │
└──────────────┬──────────────────────┘
               │
        ┌──────▼──────────────────────┐
        │  Database Query Fails?       │
        └──────┬───────────┬──────────┘
               │ YES       │ NO
               │           └──→ Continue
               │
        ┌──────▼──────────────────────┐
        │  Retry 2 times               │
        │  If still fails → Cache old  │
        │  data or return empty        │
        └─────────────────────────────┘

        ┌──────────────────────────────┐
        │  Claude API Fails?           │
        └──────┬───────────┬──────────┘
               │ YES       │ NO
               │           └──→ Continue
               │
        ┌──────▼──────────────────────┐
        │  Use Fallback Insight:       │
        │  "Keep your routine going!   │
        │   Your skin is improving"    │
        └─────────────────────────────┘

        ┌──────────────────────────────┐
        │  Insufficient Data?          │
        └──────┬───────────┬──────────┘
               │ YES       │ NO
               │           └──→ Continue
               │
        ┌──────▼──────────────────────┐
        │  Mark as "insufficient_data"│
        │  Return partial response     │
        │  Continue with available data│
        └─────────────────────────────┘
```

---

## 8️⃣ PERFORMANCE REQUIREMENTS

```
Metric              Target      Action if Failed
────────────────────────────────────────────────
Response Time       < 2 sec     Add caching / optimize queries
Database Query      < 500ms     Add indexes on user_id, created_at
Claude API Call     < 3 sec     Implement timeout fallback
Memory Usage        < 100MB     Stream large result sets
Concurrent Users    100+        Use connection pooling
```

---

## 9️⃣ TESTING STRATEGY

### Unit Tests:
```python
# Test pearson_correlation
assert pearson_correlation([1,2,3], [1,2,3]) == 1.0  # Perfect correlation
assert pearson_correlation([1,2,3], [3,2,1]) < 0     # Negative correlation

# Test get_skin_scans
result = get_skin_scans(user_id=2, days=30)
assert len(result) >= 0
assert all('score' in item for item in result)

# Test BeautyRadianceOverview model
model = BeautyRadianceOverview(
    user_id=2,
    overall_score=82,
    status="Good",
    ...
)
assert model.overall_score == 82
assert model.status in ["Low", "Fair", "Good", "High"]
```

### Integration Tests:
```python
# Test full endpoint
response = client.get("/api/v1/skin-scan/overview/2")
assert response.status_code == 200
assert "overall_score" in response.json()
assert "sleep_correlation" in response.json()

# Test with new user (no data)
response = client.get("/api/v1/skin-scan/overview/999")
assert response.status_code == 200
assert response.json()["overall_score"] is None
```

### Edge Cases:
```
✓ User with no skin scans
✓ User with no sleep data
✓ User with < 5 days of data
✓ User with 30+ days of data
✓ User with NULL cycle data
✓ Claude API timeout
✓ Database connection failure
✓ Invalid user_id (negative, string, null)
```

---

## 🔟 TIMELINE & MILESTONES

```
Day 1 (2-3 hours):
  ✓ Create Pydantic models
  ✓ Create database query functions
  ✓ Test database queries manually

Day 2 (3-4 hours):
  ✓ Create correlation calculation functions
  ✓ Test with sample data
  ✓ Handle edge cases

Day 3 (2-3 hours):
  ✓ Create service function
  ✓ Integrate Claude API
  ✓ Build response object

Day 4 (2 hours):
  ✓ Create route handler
  ✓ Full integration testing
  ✓ Error handling & edge cases

Day 5 (2 hours):
  ✓ Code review
  ✓ Performance optimization
  ✓ Documentation
  ✓ Ready for deployment

Total: ~12-16 hours
```

---

## 1️⃣1️⃣ DELIVERABLES

```
Code Files:
  ✓ ai/models/skin_scan_models.py (Updated with new models)
  ✓ ai/utils/db.py (New query functions)
  ✓ ai/utils/correlation.py (NEW FILE)
  ✓ ai/services/skin_scan_service.py (New function)
  ✓ ai/routes/skin_scan_routes.py (New route)
  ✓ tests/test_beauty_radiance.py (NEW FILE)

Documentation:
  ✓ This workflow document
  ✓ API documentation update
  ✓ Code comments & docstrings
  ✓ README update with examples

Verification:
  ✓ All tests passing
  ✓ Manual testing with Docker
  ✓ Performance benchmark
  ✓ Error scenario testing
```

---

## 1️⃣2️⃣ ASSUMPTIONS & CONSTRAINTS

```
Assumptions:
  ✓ User has at least some skin scan data
  ✓ Database connection is stable
  ✓ Claude API is accessible
  ✓ Data timestamps are accurate
  ✓ Sleep data path is $.data[0].scores.sleep

Constraints:
  ✗ Cannot use > 30 days of data (API rate limits)
  ✗ Must complete in < 2 seconds (API response time)
  ✗ Cannot modify database schema
  ✗ Must support Python 3.12+

Dependencies:
  ✓ pymysql (database)
  ✓ anthropic (Claude API)
  ✓ numpy/scipy (correlation)
  ✓ pydantic (validation)
```

---

## 1️⃣3️⃣ SUCCESS CRITERIA FOR TEAM LEAD

"The Beauty & Radiance Overview endpoint is complete when:

1. ✅ **Functionality:**
   - Returns accurate skin score and status
   - Calculates sleep ↔ skin correlation (when data available)
   - Calculates cycle ↔ skin correlation (when data available)
   - Provides AI insights via Claude
   - Handles edge cases gracefully

2. ✅ **Performance:**
   - Responds in < 2 seconds for 30-day history
   - Database queries execute in < 500ms
   - Can handle 100+ concurrent users

3. ✅ **Reliability:**
   - All error scenarios handled (DB failure, API timeout, no data)
   - Fallback mechanisms in place
   - No unhandled exceptions

4. ✅ **Testing:**
   - 90%+ code coverage
   - All unit tests passing
   - Integration tests passing
   - Edge cases tested

5. ✅ **Documentation:**
   - Code is well-commented
   - API is documented
   - This workflow document completed
   - Examples provided
"

---

## 1️⃣4️⃣ NEXT STEPS (FOR TEAM LEAD APPROVAL)

1. Review this workflow document
2. Approve technical approach
3. Confirm database access & permissions
4. Approve timeline (12-16 hours)
5. Assign code review partner

Once approved, engineering work begins immediately.

---

**End of Workflow Document**

