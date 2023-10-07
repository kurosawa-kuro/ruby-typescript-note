# Ruby on Rails タグ付きTodoアプリ作成ガイド

このガイドでは、Ruby on Railsを用いて、タグ機能が組み込まれたTodoアプリの構築手順について説明します。本アプリでは、Todo項目の作成とそれらに対するタグの付与が可能です。

## 1. プロジェクトのセットアップ

### 1.1 Railsプロジェクトの新規作成

新しいRails APIプロジェクトを作成します。ここでは、データベースとしてPostgreSQLを使用します。

```sh
rails new rails-api-todo-with-tag --api --database=postgresql
```

### 1.2 Todoスキャフォールドの生成

タイトル属性を持つTodoモデル、および関連するコントローラー、ビュー、マイグレーションファイルを生成します。

```sh
rails generate scaffold Todo title:string
```

### 1.3 データベースのマイグレーション

モデルの変更をデータベースに適用します。

```sh
rails db:migrate
```

```ruby
# db/seeds.rb

3.times do |i|
  Todo.create(title: "Todo#{i + 1}")
end
```

```sh
rails db:seed
```

## 2. アプリケーションのテスト

### 2.1 Todo項目の作成

`curl`コマンドを使用して、新しいTodo項目をPOSTリクエストで作成します。

```sh
curl -X POST http://localhost:3001/todos \
-H "Content-Type: application/json" \
-d '{"title": "abc"}'
```

### 2.2 Todo項目の取得

作成されたTodo項目をGETリクエストで取得します。

```sh
curl -X GET http://localhost:3001/todos
```

## 3. タグ機能の実装

### 3.1 タグモデルとTodoTagモデルの生成

タグを管理するTagモデルと、TodoとTagの関連を表すTodoTag中間モデルを生成します。

```sh
rails generate model Tag name:string
rails generate model TodoTag todo:references tag:references
rails db:migrate
```

### 3.2 アソシエーションの設定

各モデルにアソシエーションを設定します。

```ruby
# app/models/todo.rb
class Todo < ApplicationRecord
  has_many :todo_tags, dependent: :destroy
  has_many :tags, through: :todo_tags
end

# app/models/tag.rb
class Tag < ApplicationRecord
  has_many :todo_tags, dependent: :destroy
  has_many :todos, through: :todo_tags
end

# app/models/todo_tag.rb
class TodoTag < ApplicationRecord
  belongs_to :todo
  belongs_to :tag
end
```

### 3.3 シードデータの作成

テスト用のシードデータを作成します。

```ruby
# db/seeds.rb

# TodoとTagを各3つ作成
3.times do |i|
  Todo.create(title: "Todo#{i + 1}")
  Tag.create(name: "Tag#{i + 1}")
end

# それぞれのTodoに全てのTagを関連付け
Todo.all.each do |todo|
  Tag.all.each do |tag|
    TodoTag.create(todo: todo, tag: tag)
  end
end
```

シードデータをデータベースに反映させます。

```sh
rails db:reset
```

## 4. タグ機能の確認

Railsコンソールを利用して、タグ機能が適切に動作していることを確認します。

```sh
rails c
todo = Todo.find(1)
p todo.tags

tag = Tag.find(1)
p tag.todos
```

## 5. タグコントローラーの追加と設定

### 5.1 タグコントローラーの生成

タグのCRUD操作を実装するため、タグコントローラーを生成します。

```sh
rails generate controller Tags
```

### 5.2 ルーティングの設定

タグリソースのルーティングを`config/routes.rb`に設定します。

```ruby
# config/routes.rb

Rails.application.routes.draw do
  resources :todos
  resources :tags  # タグリソースのルーティングを追加
end
```

### 5.3 タグコントローラーのアクションの実装

`tags_controller.rb`に、タグのCRUD操作を行うアクションを実装します。

```ruby
# app/controllers/tags_controller.rb

class TagsController < ApplicationController
  before_action :set_tag, only: [:show, :update, :destroy]

  # GET /tags
  def index
    @tags = Tag.all
    render json: @tags
  end

  # GET /tags/1
  def show
    render json: @tag
  end

  # POST /tags
  def create
    @tag = Tag.new(tag_params)
    if @tag.save
      render json: @tag, status: :created, location: @tag
    else
      render json: @tag.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT /tags/1
  def update
    if @tag.update(tag_params)
      render json: @tag
    else
      render json: @tag.errors, status: :unprocessable_entity
    end
  end

  # DELETE /tags/1
  def destroy
    @tag.destroy
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_tag
      @tag = Tag.find(params[:id])
    end

    # Only allow a list of trusted parameters through.
    def tag_params
      params.require(:tag).permit(:name)
    end
end

```

## 6. タグの追加機能

### 6.1 `TodosController`に`add_tag`アクションの追加

`TodosController`に`add_tag`アクションを追加し、Todoにタグを追加できるようにします。

```ruby
# app/controllers/todos_controller.rb

class TodosController < ApplicationController
  before_action :set_todo, only: [:show, :update, :destroy, :add_tag]

  # (他のアクション)

  # POST /todos/1/add_tag
  def add_tag
    tag = Tag.find(params[:tag_id])
    @todo.tags << tag unless @todo.tags.include?(tag)
    render json: @todo
  end

  private

  def set_todo
    @todo = Todo.find(params[:id])
  end

  # (他のプライベートメソッド)
end
```

### 6.2 ルーティングの設定

`add_tag`アクションに対応するルーティングを設定します。

```ruby
# config/routes.rb

Rails.application.routes.draw do
  resources :todos do
    member do
      post :add_tag
    end
  end
  resources :tags
end
```

### 6.3 タグの追加テスト

`curl`コマンドを使用して、タグの追加をテストします。

```sh
# タグを追加するTodoのIDと追加するタグのIDを適切に設定
curl -X POST http://localhost:3001/todos/1/add_tag \
-H "Content-Type: application/json" \
-d '{"tag_id": 1}'
```

ここまでで、タグ付きTodoアプリの基本的な機能が全て実装されました。ブラウザや`curl`コマンドを使用して動作確認を行ってください。
