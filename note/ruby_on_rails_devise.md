# Ruby on Rails Devise

・[【Rails】devise関連のルーティングまとめ](https://qiita.com/beanzou/items/1ff9c7cba61fd1fa5c80)  
・[Deviseのモヤモヤを解消して快適なRailsライフを送ろう！](https://zenn.dev/kitabatake/articles/start-to-like-the-devise)


### 1. 新しい Rails API アプリケーションの作成

```bash
rails new backend --api
find . -type d -name .git -exec rm -rf {} +
```

### 2. 必要な gem の追加

Gemfile:

```ruby
gem "rack-cors"
gem 'devise'
gem 'devise_token_auth'
```

ターミナルで以下を実行:

```bash
bundle install
```

### 3. Devise と Devise Token Auth のインストール

```bash
rails g devise:install
rails g devise_token_auth:install User auth
rails db:migrate
```

### 4. Devise Token Auth の設定

config/initializers/devise\_token\_auth.rb:

```ruby
config.change_headers_on_each_request = false
  
config.headers_names = {
  :'authorization' => 'Authorization',
  :'access-token' => 'access-token',
  :'client' => 'client',
  :'expiry' => 'expiry',
  :'uid' => 'uid',
  :'token-type' => 'token-type'
}
```

### 5. CORS の設定

config/initializers/cors.rb:

```ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'http://localhost:3000'

    resource '*',
        headers: :any,
        expose: ["access-token", "expiry", "token-type", "uid", "client"],
        methods: [:get, :post, :put, :patch, :delete, :options, :head],
        credentials: true
  end
end
```

### 6. コントローラーの生成

```bash
rails g controller auth/registrations
rails g controller auth/sessions
```

### 7. コントローラーのカスタマイズ

app/controllers/auth/registrations\_controller.rb:

```ruby
class Auth::RegistrationsController < DeviseTokenAuth::RegistrationsController
  private

  def sign_up_params
    params.permit(:email, :password, :password_confirmation, :name)
  end
end
```

app/controllers/auth/sessions\_controller.rb:

```ruby
class Auth::SessionsController < ApplicationController
  def index
    if current_user
      render json: { is_login: true, data: current_user }
    else
      render json: { is_login: false, message: "ユーザーは存在しません" }
    end
  end
end
```

app/controllers/application\_controller.rb:

```ruby
class ApplicationController < ActionController::Base
  include DeviseTokenAuth::Concerns::SetUserByToken
  skip_before_action :verify_authenticity_token
end
```

### 8. セッションストアの設定

config/application.rb:

```ruby
config.session_store :cookie_store, key: '_session'
config.middleware.use ActionDispatch::Cookies
config.middleware.use config.session_store, config.session_options
```

### 9. ルーティングの設定

config/routes.rb:

```ruby
Rails.application.routes.draw do
  mount_devise_token_auth_for 'User', at: 'auth', controllers: {
    registrations: 'auth/registrations'
  }

  namespace :auth do
    resources :sessions, only: %i[index]
  end
end
```

---

上記の手順に従うことで、Rails アプリケーションに Devise と Devise Token Auth を導入できます。


## Rspec

```
group :development, :test do
  gem 'rspec-rails', '~> 5.0'
  gem 'factory_bot_rails'
end

group :test do
  gem 'faker'
end
```

```
bundle install
```

```
rails generate rspec:install
```

spec/controllers/todos_controller_spec.rb

```
require 'rails_helper'

RSpec.describe TodosController, type: :controller do
  describe "GET #index" do
    before do
      FactoryBot.create_list(:todo, 3)
      get :index
    end

    it "returns a successful response" do
      expect(response).to be_successful
    end

    it "returns all todos" do
      expect(JSON.parse(response.body).size).to eq(3)
    end
  end

  describe "POST #create" do
    context "with valid attributes" do
      it "creates a new todo" do
        expect {
          post :create, params: { todo: FactoryBot.attributes_for(:todo) }
        }.to change(Todo, :count).by(1)
      end
    end

    context "with invalid attributes" do
      it "does not create a new todo" do
        expect {
          post :create, params: { todo: { title: nil } }
        }.to_not change(Todo, :count)
      end
    end
  end
end
```
