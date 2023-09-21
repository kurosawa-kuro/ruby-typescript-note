# Ruby on Rails Devise

・[【Rails】devise関連のルーティングまとめ](https://qiita.com/beanzou/items/1ff9c7cba61fd1fa5c80)  
・[Deviseのモヤモヤを解消して快適なRailsライフを送ろう！](https://zenn.dev/kitabatake/articles/start-to-like-the-devise)

## Create

```
rails new rails-todo-backend --api
find . -type d -name .git -exec rm -rf {} +
```

## Devise

```
gem 'devise'
gem 'devise_token_auth'
```

```
bundle install
rails generate devise:install
rails generate devise_token_auth:install User auth
```

```
rails db:migrate
```

```
rails generate model Todo title:string user:references
```

```
rails db:migrate
```

```
# app/models/user.rb
class User < ActiveRecord::Base
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
  include DeviseTokenAuth::Concerns::User

  has_many :todos
end
```

```
# app/models/todo.rb
class Todo < ApplicationRecord
  belongs_to :user
end
```

```
rails generate controller Todos
```

```
# app/controllers/todos_controller.rb
class TodosController < ApplicationController
  before_action :authenticate_user!, only: [:create]

  def index
    @todos = current_user.todos
    render json: @todos
  end

  def create
    @todo = current_user.todos.build(todo_params)
    if @todo.save
      render json: @todo, status: :created
    else
      render json: @todo.errors, status: :unprocessable_entity
    end
  end

  private

  def todo_params
    params.require(:todo).permit(:title)
  end
end
```

```
# config/routes.rb
Rails.application.routes.draw do
  mount_devise_token_auth_for 'User', at: 'auth'
  resources :todos, only: [:index, :create]
end
```

```
# db/seeds.rb
user = User.create!(email: 'test@example.com', password: 'password', password_confirmation: 'password')
3.times do |i|
  user.todos.create!(title: "Sample Todo #{i + 1}")
end
```

```
rails db:seed
```

```
# config/application.rb

config.session_store :cookie_store, key: '_session'
config.middleware.use ActionDispatch::Cookies
config.middleware.use config.session_store, config.session_options
```

```
rails middleware | grep Cookie

# use ActionDispatch::Cookies
# use ActionDispatch::Session::CookieStore
```

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
