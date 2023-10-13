# 予約アプリ開発ドキュメント

## 1. 環境構築手順作成

### 1.1 バックエンド環境

- **API**: api の設定
- **Session**: session設定
- **Testing**: rspec, guard
- **Authentication**: devise
- **Code Quality**: rubocop
- **Image Processing**: mini\_magick

### 1.2 フロントエンド環境

- **Programming**: typescript
- **Framework**: next.js
NexAuth
Firebase Auth
- **Styling**: tailwindcss
- **Testing**: jest, react-testing-library, cypress
- **Mocking**: msw
- **Code Quality**: eslint, prettier
- **Pre-commit Hooks**: husky, lint-staged
- **UI Development**: storybook

## 2. 中期目標

- 予約アプリの開発

## 3. 機能一覧

### 3.1 管理画面

- **認証**: ログイン, ログアウト
- **商品**: 商品の登録、参照、更新、削除、商品画像のアップロード、商品とタグの紐づけ、商品CSV入力、出力,カレンダー
- **検索**: ホテル名, タグ, 住所, レビュー
- **位置情報**: 商品の位置情報の登録、参照、更新、削除
- **予約**: 予約の参照、更新、削除
- **タグ**: タグの登録、参照、更新、削除
- **レビュー**: レビューの参照、更新、削除
- **ユーザー**: ユーザーの参照、更新、削除
- **特典制度**: ポイント制度（予約やレビューを行うごとにポイント獲得しクーポンが発行される）、クーポンの登録、参照、更新、削除
- **その他**: Stripe決済機能、 レポート機能（売上や予約状況の自動生成）、キャンセル待ち機能、メール一斉送信機能

### 3.2 ユーザー画面

- **認証**: ユーザー登録, ログイン, ログアウト
- **ユーザー情報**: ユーザー情報の更新、削除
- **トップ画面**: 商品の参照
- **商品**: 商品の参照
- **予約**: 予約の登録
- **クーポン**: クーポンの適用
- **レビュー**: レビューの登録、参照、更新、削除