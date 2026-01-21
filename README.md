Project Architecture & Data Flow Diagrams

Here are the visual representations of the "ShikshaSahayak" system. These diagrams are written in Mermaid.js, which renders automatically in GitHub, Notion, and many documentation tools.

1. High-Level System Architecture

This diagram illustrates the technical flow from the teacher's WhatsApp query to the AI's response.

graph TD
    %% Styling
    classDef mobile fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef backend fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef ai fill:#f3e5f5,stroke:#4a148c,stroke-width:2px;
    classDef db fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;

    %% Nodes
    subgraph "Teacher Layer (Frontend)"
        T[Teacher (WhatsApp)]:::mobile
        V[Voice Note / Text]:::mobile
    end

    subgraph "Processing Layer (Backend)"
        API[WhatsApp API / Twilio]:::backend
        S2T[Whisper (Speech-to-Text)]:::ai
        Router[Query Router & Parser]:::backend
    end

    subgraph "Intelligence Layer"
        RAG[Hybrid RAG Engine]:::ai
        LLM[LLM (GPT-4)]:::ai
        
        subgraph "Knowledge Base"
            VectorDB[(Vector DB)]:::db
            Docs[NIPUN/FLN Docs]:::db
            Personas[Student Personas]:::db
        end
    end

    subgraph "Storage Layer"
        SQL[(PostgreSQL)]:::db
    end

    %% Flow
    T -->|1. Records Audio| V
    V -->|2. Sends Msg| API
    API -->|3. Forward| S2T
    S2T -->|4. Text| Router
    
    Router -->|5. Fetch Context| RAG
    Personas -.->|Inject Local Context| RAG
    Docs -.->|Inject Global Pedagogy| RAG
    VectorDB -.->|Retrieve Similar Cases| RAG
    
    RAG -->|6. Context + Query| LLM
    LLM -->|7. Generate 3-Step Plan| Router
    
    Router -->|8. Store Interaction| SQL
    Router -->|9. Send Response| API
    API -->|10. Reply| T


2. The "Virtuous Cycle" Data Loop

This diagram shows how individual teacher queries are transformed into district-level training modules.

stateDiagram-v2
    direction LR
    
    state "Classroom Chaos" as Chaos
    state "Teacher Query" as Query
    state "AI Intervention" as AI
    state "Data Aggregation" as Data
    state "Pattern Detection" as Pattern
    state "District Action" as District
    state "New Training Module" as Module

    Chaos --> Query: Teacher asks for help
    Query --> AI: Sends Voice Note
    AI --> Chaos: "Pair Ravi & Priya" (Immediate Fix)
    
    AI --> Data: Logs Issue & Solution
    Data --> Pattern: 30% Teachers ask same Q
    
    Pattern --> District: Alert: "Shy Students in Math"
    District --> Module: Auto-Generate Micro-Course
    
    Module --> Chaos: Deployed to all Teachers
    
    note right of Pattern
        Transforming private struggles
        into public insights.
    end note


3. Entity Relationship Diagram (ERD)

This demonstrates the data structure connecting teachers, students, and learning outcomes.

erDiagram
    TEACHER ||--o{ STUDENT : manages
    TEACHER ||--o{ QUERY : sends
    STUDENT ||--o{ PERSONA_FLAG : has
    QUERY ||--|{ FEEDBACK : generates
    QUERY }|--|| TOPIC_CLUSTER : belongs_to
    TOPIC_CLUSTER ||--o{ TRAINING_MODULE : triggers

    TEACHER {
        string id PK
        string school_id
        string language_pref
    }

    STUDENT {
        string id PK
        int learning_level "L1-L4"
        string attendance_rate
    }

    PERSONA_FLAG {
        string type "Shy/Disruptive/Fast"
        date added_on
        int confidence_score
    }

    QUERY {
        string audio_url
        string transcript
        string sentiment
        timestamp created_at
    }

    TRAINING_MODULE {
        string title
        string auto_generated_content
        int teacher_completion_count
    }
