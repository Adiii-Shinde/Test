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
