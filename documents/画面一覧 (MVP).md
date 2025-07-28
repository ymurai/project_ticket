# 画面一覧 (MVP)

| 画面ID    | 画面名 / URL 例                        | 主な利用ロール           | 機能概要                               |
| ------- | ---------------------------------- | ----------------- | ---------------------------------- |
| **S01** | `/login`                           | 全ロール           | Magic‑link ログインフォームとリンク送信結果表示      |
| **S02** | `/home`                            | Organizer / Staff | ホーム（公演一覧・クイックアクション・最近の活動） |
| **S03** | `/events/new`                      | Organizer / Staff | 新しい公演の登録フォーム                  |
| **S04** | `/events/:id/edit`                 | Organizer / Staff | 公演概要・編集・ステージ・設定                         |
| **S04.1** | `/events/:id/summary`              | Organizer / Staff | 公演サマリー（売上・残席状況・）             |
| **S05** | `/venues`                          | Organizer / Staff | 会場マスター管理（一覧・登録・編集）                |
| **S06** | `/stages/new`                      | Organizer / Staff | ステージ追加フォーム（公演へのステージ設定） |
| **S07** | `/stages/:id/edit`                 | Organizer / Staff | ステージ詳細・編集 (残席・券種別売上・CSV)   |
| **S08** | `/ticket-types`                    | Organizer / Staff | 券種・価格設定管理（一覧・登録・編集）            |
| **S09** | `/tickets/:stageId/purchase`       | Customer          | チケット購入フォーム (券種・枚数入力)               |
| **S10** | `/orders/complete`                 | Customer          | 予約完了画面 (QR 表示、メール送信案内)             |
| **S11** | `/mypage/orders`                   | Customer          | 購入履歴一覧・QR 再表示                      |
| **S12** | `/checkin`                         | Checker           | 公演選択画面（当日受付用）                      |
| **S13** | `/checkin/:token`                  | Checker           | QR スキャン結果表示・チェックイン更新               |
| **S14** | `/admin/users`                     | SystemAdmin       | ユーザ・ロール管理 (招待・権限変更・無効化)            |
| **S15** | `/status`                          | SystemAdmin       | システムステータス (DB 接続・メール API 健康チェック)   |
| **S16** | `/docs`                            | 全ロール              | OSS ドキュメント / 利用ガイド                 |

> **備考**
>
> * URL は Remix ルーティング例。Next.js なら `pages` 構造に準ずる。
> * モバイル受付を意識し **S12/S13** は PWA フルスクリーンを想定。
> * Stripe 決済を導入する場合、**S09** に Checkout セッションを組み込む予定。
