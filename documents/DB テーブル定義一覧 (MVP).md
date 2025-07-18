# DB テーブル定義一覧 (MVP)

| テーブル | 主キー | 主なカラム | 説明 |
| ---- | --- | ----- | -- |
|      |     |       |    |

| **customers**     | `id` (int) | `email` (string, unique)`role` (enum: systemAdmin / organizer / staff / checker / customer)                                                                | ログインユーザ。観客含む。    |
| ----------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| **events**        | `id` (int) | `title` (string)`description` (text)                                                                                                                       | 作品情報。            |
| **venues**        | `id` (int) | `name` (string)`capacity` (int)                                                                                                                            | 会場情報。キャパシティ保持。   |
| **performances**  | `id` (int) | `event_id` (fk -> events.id)`venue_id` (fk -> venues.id)`starts_at` (datetime)`sold` (int, default 0)                                                      | 上演回。日時と販売済数を保持。  |
| **ticket\_types** | `id` (int) | `name` (string)`price` (int)                                                                                                                               | 券種と金額。           |
| **orders**        | `id` (int) | `customer_id` (fk -> customers.id)`performance_id` (fk -> performances.id)`ticket_type_id` (fk -> ticket\_types.id)`quantity` (int)`created_at` (datetime) | 購入（予約）単位。        |
| **tickets**       | `id` (int) | `order_id` (fk -> orders.id)`qr_token` (string, unique)`checked_in` (boolean, default false)                                                               | 1 枚のチケット。QR を保持。 |

## カラム型と制約のポイント

* すべての ID は `INT` ＋ AUTO\_INCREMENT (PostgreSQL: `SERIAL`、SQLite: `INTEGER PRIMARY KEY`).
* `qr_token` は UUIDv4 を 36 文字で格納し、一意制約。
* `role` はアプリケーション側の列挙型でバリデーション (DB は `VARCHAR`).
* `sold` カウンタは注文確定トランザクション内でインクリメントし、行ロックで競合を防止。

---

## ER 図 (Mermaid)

```mermaid
erDiagram
  customers ||--o{ orders : places
  customers {
    int id PK
    string email
    string role
  }

  events ||--o{ performances : has
  events {
    int id PK
    string title
    string description
  }

  venues ||--o{ performances : hosts
  venues {
    int id PK
    string name
    int capacity
  }

  performances ||--o{ orders : contains
  performances {
    int id PK
    int event_id FK
    int venue_id FK
    datetime starts_at
    int sold
  }

  ticket_types ||--o{ orders : pricedAs
  ticket_types {
    int id PK
    string name
    int price
  }

  orders ||--o{ tickets : issues
  orders {
    int id PK
    int customer_id FK
    int performance_id FK
    int ticket_type_id FK
    int quantity
    datetime created_at
  }

  tickets {
    int id PK
    int order_id FK
    string qr_token
    boolean checked_in
  }
```

---

> **備考**
>
> * 最小構成のため、会員詳細情報や決済情報のテーブルは未実装。
> * 決済機能追加時は `payments` テーブルを追加し、`orders` と 1:1 リレーションで紐付け予定。
