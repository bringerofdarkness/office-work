# 📊 Data Workflow Diagram - Pulse_E
## Simple Figma-Ready Data Flow

```mermaid
graph TD
    A["📱 Mobile App<br/>(User Interface)"]
    
    A -->|HTTP Request| B["🔌 FastAPI Routes<br/>(Request Handler)"]
    
    B -->|1. Fetch Data| C["💾 MySQL Database<br/>(AWS RDS)"]
    B -->|2. Process Logic| D["⚙️ Service Layer<br/>(Business Logic)"]
    
    D -->|Query Results| E["🤖 Claude AI API<br/>(Insights Generation)"]
    E -->|AI Response| D
    
    D -->|Validation| F["✅ Pydantic Models<br/>(Response Format)"]
    
    F -->|JSON Response| B
    B -->|API Response| A
    
    C -->|Return Data| D
    
    style A fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px
    style B fill:#E3F2FD,stroke:#1565C0,stroke-width:2px
    style C fill:#FFF3E0,stroke:#E65100,stroke-width:2px
    style D fill:#FCE4EC,stroke:#C2185B,stroke-width:2px
    style E fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px
    style F fill:#E0F2F1,stroke:#00695C,stroke-width:2px
```

---

## 📌 **6 Life Journey Endpoints - Data Flow**

```mermaid
graph LR
    Mobile["📱 Mobile App"]
    
    subgraph Endpoints["🔌 API Endpoints (7-8 total)"]
        E1["Beauty & Radiance"]
        E2["Cycle & Fertility"]
        E3["Athlete Performance"]
        E4["Pregnancy & Postpartum"]
        E5["Menopause & Vitality"]
        E6["Lifelong Thriving"]
    end
    
    subgraph Services["⚙️ Service Layer"]
        S1["beauty_service"]
        S2["cycle_service"]
        S3["athlete_service"]
        S4["pregnancy_service"]
        S5["menopause_service"]
        S6["thriving_service"]
    end
    
    subgraph Database["💾 Database Tables"]
        DB1["skin_scans"]
        DB2["menstrual_cycles<br/>terra_activity_data"]
        DB3["health_logs<br/>terra_activity_data"]
        DB4["pregnancy_info"]
        DB5["health_logs"]
        DB6["health_logs<br/>6-year history"]
    end
    
    subgraph AI["🤖 Claude AI"]
        CLAUDE["Claude Vision +<br/>Claude Opus"]
    end
    
    Mobile --> E1 & E2 & E3 & E4 & E5 & E6
    
    E1 --> S1
    E2 --> S2
    E3 --> S3
    E4 --> S4
    E5 --> S5
    E6 --> S6
    
    S1 --> DB1 --> CLAUDE
    S2 --> DB2 --> CLAUDE
    S3 --> DB3 --> CLAUDE
    S4 --> DB4 --> CLAUDE
    S5 --> DB5 --> CLAUDE
    S6 --> DB6 --> CLAUDE
    
    CLAUDE --> S1 & S2 & S3 & S4 & S5 & S6
    
    S1 --> Mobile
    S2 --> Mobile
    S3 --> Mobile
    S4 --> Mobile
    S5 --> Mobile
    S6 --> Mobile
    
    style Mobile fill:#E8F5E9,stroke:#2E7D32,stroke-width:3px
    style Endpoints fill:#E3F2FD,stroke:#1565C0,stroke-width:2px
    style Services fill:#FCE4EC,stroke:#C2185B,stroke-width:2px
    style Database fill:#FFF3E0,stroke:#E65100,stroke-width:2px
    style AI fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px
```

---

## 🔄 **Single Endpoint Request-Response Cycle**

```mermaid
sequenceDiagram
    actor User as 📱 User
    participant API as 🔌 FastAPI Route
    participant Service as ⚙️ Service Layer
    participant DB as 💾 MySQL
    participant AI as 🤖 Claude AI
    
    User->>API: HTTP POST /endpoint<br/>with user_id, params
    
    API->>Service: Call service function
    
    Service->>DB: Query data<br/>(30-day history, current state)
    DB-->>Service: Return records (JSON, timestamps)
    
    Service->>AI: Send prompt<br/>(data + context)
    AI-->>Service: Return insights<br/>(text response)
    
    Service->>Service: Parse response<br/>(extract scores 0-100)
    
    Service-->>API: Return PydanticModel<br/>(validated data)
    
    API->>API: Serialize to JSON
    API-->>User: HTTP 200 + JSON<br/>(today, history,<br/>insights, scores)
    
    style User fill:#E8F5E9
    style API fill:#E3F2FD
    style Service fill:#FCE4EC
    style DB fill:#FFF3E0
    style AI fill:#F3E5F5
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

## 📝 **To Recreate in Figma:**

1. **Boxes (Rectangles):**
   - Mobile App (green)
   - FastAPI Routes (blue)
   - Service Layer (pink)
   - MySQL Database (orange)
   - Claude AI (purple)
   - Pydantic Models (teal)

2. **Arrows:**
   - User Request → API (green)
   - API → Service (blue)
   - Service → Database (orange)
   - Service → Claude (purple)
   - Claude → Service (purple)
   - Service → Pydantic (teal)
   - Pydantic → API (blue)
   - API → User Response (green)

3. **Colors:**
   - Green: User interactions
   - Blue: API/Routing
   - Pink: Business logic
   - Orange: Database
   - Purple: AI
   - Teal: Validation

---

Generated: 2026-09-22 | For: Team Alignment
