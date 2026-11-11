erDiagram
    ACCOUNT ||--|| PROFILE : "has"
    ACCOUNT ||--o{ COMMISSION : "as commissioner"
    ACCOUNT ||--o{ COMMISSION : "as partaker"
    COMMISSION ||--|| TRANSACTION : "has"
    THIRD_PARTY ||--o{ TRANSACTION : "processes"
    TRANSACTION ||--o{ PAYMENT : "records"

    ACCOUNT {
        bigint id PK
        string email UK
        string password_hash
        enum role "user | admin"
        enum status "active | suspended | pending_verification"
        timestamp created_at
        timestamp updated_at
    }

    PROFILE {
        bigint id PK
        bigint account_id FK "unique (1:1)"
        string nickname
        string profile_photo
        text bio
        text skills
        timestamp created_at
        timestamp updated_at
    }

    THIRD_PARTY {
        bigint id PK
        string name
        enum type "bank | escrow_service | payment_processor"
        decimal commission_percentage
        enum status "active | inactive"
        timestamp created_at
        timestamp updated_at
    }

    COMMISSION {
        bigint id PK
        bigint commissioner_id FK "accounts.id"
        bigint partaker_id FK "accounts.id, nullable"
        string title
        text description
        decimal price
        date deadline
        enum status "open | in_progress | submitted | completed | cancelled | disputed"
        timestamp created_at
        timestamp updated_at
    }

    TRANSACTION {
        bigint id PK
        bigint commission_id FK "unique (1:1)"
        bigint third_party_id FK
        decimal base_amount
        decimal commissioner_fee_percentage
        decimal commissioner_fee_amount
        decimal partaker_fee_percentage
        decimal partaker_fee_amount
        decimal submission_fee_amount
        decimal total_charged_to_commissioner
        decimal net_to_partaker
        decimal platform_revenue
        string external_reference
        enum status "pending | held | released | refunded | disputed"
        timestamp held_at
        timestamp released_at
        timestamp created_at
        timestamp updated_at
    }

    PAYMENT {
        bigint id PK
        bigint transaction_id FK
        enum type "deposit | release | refund"
        decimal amount
        string reference
        timestamp created_at
        timestamp updated_at
    }