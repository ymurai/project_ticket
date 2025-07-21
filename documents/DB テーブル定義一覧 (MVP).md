# DB テーブル定義一覧 (MVP)

| テーブル | 主キー | 主なカラム | 説明 |
| ---- | --- | ----- | -- |
|      |     |       |    |

| **customers**     | `id` (int) | `email` (string, unique)`role` (enum: systemAdmin / organizer / staff / checker / customer)                                                                | ログインユーザ。観客含む。    |
| ----------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| **events**        | `id` (int) | `event_name` (string, not null)`event_description` (text, not null)`genre` (enum)`duration` (int)`performance_start_date` (date)`performance_end_date` (date)`director` (string)`cast` (text)`poster_image_path` (string)`website_url` (string)`social_media_info` (text)`age_restriction` (enum)`language` (enum)`has_subtitle` (boolean)`is_accessible` (boolean)`notes` (text)`status` (enum)`allow_reservations` (boolean)`email_notifications` (boolean)`created_at` (timestamp)`updated_at` (timestamp) | 公演情報。基本情報から設定までを含む。 |
| **venues**        | `id` (int) | `name` (string)`capacity` (int)                                                                                                                            | 会場情報。キャパシティ保持。   |
| **performances**  | `id` (int) | `event_id` (fk -> events.id)`venue_id` (fk -> venues.id)`starts_at` (datetime)`sold` (int, default 0)                                                      | ステージ。日時と販売済数を保持。  |
| **ticket\_types** | `id` (int) | `name` (string)`price` (int)                                                                                                                               | 券種と金額。           |
| **orders**        | `id` (int) | `customer_id` (fk -> customers.id)`performance_id` (fk -> performances.id)`ticket_type_id` (fk -> ticket\_types.id)`quantity` (int)`created_at` (datetime) | 購入（予約）単位。        |
| **tickets**       | `id` (int) | `order_id` (fk -> orders.id)`qr_token` (string, unique)`checked_in` (boolean, default false)                                                               | 1 枚のチケット。QR を保持。 |

## カラム型と制約のポイント

* すべての ID は `INT` ＋ AUTO\_INCREMENT (PostgreSQL: `SERIAL`、SQLite: `INTEGER PRIMARY KEY`).
* `qr_token` は UUIDv4 を 36 文字で格納し、一意制約。
* `role` はアプリケーション側の列挙型でバリデーション (DB は `VARCHAR`).
* `sold` カウンタは注文確定トランザクション内でインクリメントし、行ロックで競合を防止。

### events テーブル詳細仕様

* `genre`: 'drama', 'musical', 'concert', 'dance', 'comedy', 'other'
* `duration`: 1-600 (分)、NOT NULL
* `performance_start_date`, `performance_end_date`: 上演期間、NOT NULL
* `age_restriction`: '', 'R15', 'R18', 'PG12' (空文字列=制限なし)
* `language`: 'ja', 'en', 'ko', 'zh', 'other' (デフォルト: 'ja')
* `status`: 'draft', 'active', 'archived' (デフォルト: 'draft')
* `allow_reservations`, `email_notifications`: boolean (デフォルト: true)
* `poster_image_path`: ファイルストレージのパスを格納
* 制約: `performance_end_date >= performance_start_date`

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
    string event_name
    text event_description
    enum genre
    int duration
    date performance_start_date
    date performance_end_date
    string director
    text cast
    string poster_image_path
    string website_url
    text social_media_info
    enum age_restriction
    enum language
    boolean has_subtitle
    boolean is_accessible
    text notes
    enum status
    boolean allow_reservations
    boolean email_notifications
    timestamp created_at
    timestamp updated_at
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
