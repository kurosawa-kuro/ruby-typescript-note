# Ruby on Rails Devise

・[【Rails】devise関連のルーティングまとめ](https://qiita.com/beanzou/items/1ff9c7cba61fd1fa5c80)  
・[Deviseのモヤモヤを解消して快適なRailsライフを送ろう！](https://zenn.dev/kitabatake/articles/start-to-like-the-devise)

# Ruby on Rails Devise構築手順

## Railsアプリケーションの作成
rails new backend --api
find . -type d -name .git -exec rm -rf {} +

## Gemの追加とインストール
### Gemfileに追加
gem 'devise'
gem 'devise_token_auth'
### インストール
bundle install

## DeviseとDeviseTokenAuthのセットアップ
rails g devise:install
rails g devise_token_auth:install User auth
rails db:migrate

## 各種設定変更

### DeviseTokenAuth設定 (config/initializers/devise_token_auth.rb)
config.headers_names = {
  :'authorization' => 'Authorization',
  :'access-token' => 'access-token',
  :'client' => 'client',
  :'expiry' => 'expiry',
  :'uid' => 'uid',
  :'token-type' => 'token-type'
}
config.change_headers_on_each_request = false

### CORS設定 (config/initializers/cors.rb)
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

## コントローラの作成と編集
### コントローラの作成
rails g controller auth/registrations
rails g controller auth/sessions

### auth/registrations_controller.rb
class Auth::RegistrationsController < DeviseTokenAuth::RegistrationsController
  private
  def sign_up_params
    params.permit(:email, :password, :password_confirmation, :name)
  end
end

### auth/sessions_controller.rb
class Auth::SessionsController < ApplicationController
  def index
    if current_user
      render json: { is_login: true, data: current_user }
    else
      render json: { is_login: false, message: "ユーザーは存在しません" }
    end
  end
end

### ApplicationControllerの編集 (app/controllers/application_controller.rb)
class ApplicationController < ActionController::Base
  include DeviseTokenAuth::Concerns::SetUserByToken
  skip_before_action :verify_authenticity_token
end

## アプリケーション全体設定 (config/application.rb)
config.session_store :cookie_store, key: '_session'
config.middleware.use ActionDispatch::Cookies
config.middleware.use config.session_store, config.session_options

## ルーティングの設定 (config/routes.rb)
Rails.application.routes.draw do
  mount_devise_token_auth_for 'User', at: 'auth', controllers: {
    registrations: 'auth/registrations'
  }
  namespace :auth do
    resources :sessions, only: %i[index]
  end
end


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
