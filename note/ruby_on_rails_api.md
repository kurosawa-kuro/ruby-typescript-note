# 1. Rails APIのセットアップ

新しいRails APIを作成するには以下のコマンドを実行します。

```bash
rails new backend --api
```

## 1.1 セッションの設定

初期設定としてセッションの設定を行います。

```ruby
# config/application.rb
config.session_store :cookie_store, key: '_session'
config.middleware.use ActionDispatch::Cookies
config.middleware.use config.session_store, config.session_options
```

## 1.2 CORSの設定

フロントエンドとの通信を許可するためのCORS設定を行います。

```ruby
# config/initializers/cors.rb
Rails.application.config.middleware.insert_before 0, Rack::Cors do
    allow do
    origins 'http://localhost:3000', 'http://127.0.0.1:3000'
  
      resource '*',
          headers: :any,
          expose: ["access-token", "expiry", "token-type", "uid", "client"],
          methods: [:get, :post, :put, :patch, :delete, :options, :head],
          credentials: true
    end
end
```
