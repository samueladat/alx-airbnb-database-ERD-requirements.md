# AirBnB Database – ER Diagram Requirements

This document defines the entities, attributes, relationships, constraints, and indexes for the AirBnB clone database, based on the provided specification. A Mermaid ER diagram is included for quick visualization on GitHub.

---

## Entities & Attributes

### User
- **user_id**: UUID, **PK**, Indexed
- **first_name**: VARCHAR, **NOT NULL**
- **last_name**: VARCHAR, **NOT NULL**
- **email**: VARCHAR, **UNIQUE**, **NOT NULL**
- **password_hash**: VARCHAR, **NOT NULL**
- **phone_number**: VARCHAR, NULL
- **role**: ENUM(`guest`, `host`, `admin`), **NOT NULL**
- **created_at**: TIMESTAMP, DEFAULT `CURRENT_TIMESTAMP`

### Property
- **property_id**: UUID, **PK**, Indexed
- **host_id**: UUID, **FK** → User(user_id)
- **name**: VARCHAR, **NOT NULL**
- **description**: TEXT, **NOT NULL**
- **location**: VARCHAR, **NOT NULL**
- **pricepernight**: DECIMAL, **NOT NULL**
- **created_at**: TIMESTAMP, DEFAULT `CURRENT_TIMESTAMP`
- **updated_at**: TIMESTAMP, `ON UPDATE CURRENT_TIMESTAMP`

### Booking
- **booking_id**: UUID, **PK**, Indexed
- **property_id**: UUID, **FK** → Property(property_id)
- **user_id**: UUID, **FK** → User(user_id)
- **start_date**: DATE, **NOT NULL**
- **end_date**: DATE, **NOT NULL**
- **total_price**: DECIMAL, **NOT NULL**
- **status**: ENUM(`pending`, `confirmed`, `canceled`), **NOT NULL**
- **created_at**: TIMESTAMP, DEFAULT `CURRENT_TIMESTAMP`

### Payment
- **payment_id**: UUID, **PK**, Indexed
- **booking_id**: UUID, **FK** → Booking(booking_id)
- **amount**: DECIMAL, **NOT NULL**
- **payment_date**: TIMESTAMP, DEFAULT `CURRENT_TIMESTAMP`
- **payment_method**: ENUM(`credit_card`, `paypal`, `stripe`), **NOT NULL**

### Review
- **review_id**: UUID, **PK**, Indexed
- **property_id**: UUID, **FK** → Property(property_id)
- **user_id**: UUID, **FK** → User(user_id)
- **rating**: INTEGER, **NOT NULL**, CHECK `1 ≤ rating ≤ 5`
- **comment**: TEXT, **NOT NULL**
- **created_at**: TIMESTAMP, DEFAULT `CURRENT_TIMESTAMP`

### Message
- **message_id**: UUID, **PK**, Indexed
- **sender_id**: UUID, **FK** → User(user_id)
- **recipient_id**: UUID, **FK** → User(user_id)
- **message_body**: TEXT, **NOT NULL**
- **sent_at**: TIMESTAMP, DEFAULT `CURRENT_TIMESTAMP`

---

## Relationships (Cardinality)

- User (host) **1 — N** Property  
- User (guest) **1 — N** Booking  
- Property **1 — N** Booking  
- Booking **1 — N** Payment  
- Property **1 — N** Review  
- User **1 — N** Review  
- User **1 — N** Message (as sender)  
- User **1 — N** Message (as recipient)

---

## Mermaid ER Diagram

```mermaid
erDiagram
  USER {
    uuid user_id PK
    string first_name
    string last_name
    string email
    string password_hash
    string phone_number
    string role
    timestamp created_at
  }

  PROPERTY {
    uuid property_id PK
    uuid host_id FK
    string name
    text description
    string location
    decimal pricepernight
    timestamp created_at
    timestamp updated_at
  }

  BOOKING {
    uuid booking_id PK
    uuid property_id FK
    uuid user_id FK
    date start_date
    date end_date
    decimal total_price
    string status
    timestamp created_at
  }

  PAYMENT {
    uuid payment_id PK
    uuid booking_id FK
    decimal amount
    timestamp payment_date
    string payment_method
  }

  REVIEW {
    uuid review_id PK
    uuid property_id FK
    uuid user_id FK
    int rating
    text comment
    timestamp created_at
  }

  MESSAGE {
    uuid message_id PK
    uuid sender_id FK
    uuid recipient_id FK
    text message_body
    timestamp sent_at
  }

  USER ||--o{ PROPERTY : "hosts"
  USER ||--o{ BOOKING  : "makes"
  PROPERTY ||--o{ BOOKING : "has"
  BOOKING ||--o{ PAYMENT : "paid by"
  PROPERTY ||--o{ REVIEW : "receives"
  USER ||--o{ REVIEW : "writes"
  USER ||--o{ MESSAGE : "sends"
  USER ||--o{ MESSAGE : "receives"
