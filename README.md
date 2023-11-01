## <center>CY2 FAQ System ERD V0.1</center>

```mermaid
erDiagram

pipeline ||--|{ documentsource_pipeline : ""
documentsource_pipeline }o--|| documentsource: ""
organisation ||--o{ pipeline: ""
documentsource }o--|| organisation: ""
llm }o--|| organisation: ""

accesstoken }o--|| organisation: ""
user ||--o{ accesstoken: ""  

user ||--o{ organisation_user : ""

organisation_user }o--|| organisation: ""

llm ||--|| pipeline : ""

organisation {
    series id PK
    varchar name
    timestamp join_date
}

user {
    string id PK
}

accesstoken {
    series id PK
    int user_id FK
    int organisation_id FK
    string hashed_token
    timestamp issue_date
    timestamp expiration_date
}

documentsource {
    series id PK
    int organisation_id FK
    JSON config
    enum type
}

pipeline {
    series id PK
    int organisation_id FK
    int llm_id FK
    varchar name
}

llm {
    series id PK
    int organisation_id FK
    JSON config
    enum type
}

documentsource_pipeline {
    int document_source_id FK
    int pipeline_id FK
}

organisation_user { 
    series id PK
    int user_id FK
    int organisation_id FK
}
```

<!-- accesstoken_organisation {
    series id PK
    int accesstoken_id FK
    int organisation_id FK
} 

accesstoken ||--|{ accesstoken_organisation : ""
accesstoken_organisation }|--|| organisation: ""

-->
