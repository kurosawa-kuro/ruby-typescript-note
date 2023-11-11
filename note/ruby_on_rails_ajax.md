## Ruby on RailsによるAJAXメモアプリケーション

### プロジェクトのセットアップ
1. 新しいRailsアプリケーションの作成:
   ```shell
   rails new rails-todo-ajax
   ```
2. 作成したアプリケーションディレクトリへの移動:
   ```shell
   cd rails-todo-ajax
   ```

### モデルの作成
1. タイトルと説明を持つ`Todo`モデルの生成:
   ```shell
   rails g model Todo title:string
   ```
2. `todos`テーブルを作成するためのデータベースマイグレーションの実行:
   ```shell
   rails db:migrate
   ```

### コントローラーとビューのセットアップ
1. 特定のアクションを持つ`Todos`コントローラーの生成:
   ```shell
   rails generate controller Todos index create update destroy ajax_create
   ```
2. 必要なビューファイルの作成:
   ```shell
   touch app/views/todos/_form.html.erb
   touch app/views/todos/_list.html.erb
   touch app/views/todos/_show.html.erb
   touch app/views/todos/ajax_create.js.erb
   touch app/views/todos/index.html.erb
   ```

### コントローラーアクションの実装
以下のようにコントローラーアクションを実装してください：
```ruby
# app/controllers/todos_controller.rb
class TodosController < ApplicationController
  def index
    @todo_new = Todo.new
    @todos = Todo.all
  end
 
  def create
    @todo_new = Todo.new(todos_params)
    @todo_new.save
    redirect_to root_path
  end
 
  def update
    @todo = Todo.find(params[:id])
    @todo.update(todos_params)
    redirect_to root_path
  end
 
  def destroy
    @todo = Todo.find(params[:id])
    @todo.destroy
    redirect_to root_path
  end

  def ajax_create
    @todo_new = Todo.new(todos_params)
    @todo_new.save
    @todos = Todo.all
  end
 
  private
 
  def todos_params
    params.require(:todo).permit(:title)
  end
end
```

### ルーティングの設定
```ruby
Rails.application.routes.draw do
  root :to => 'todos#index'
  resources :todos, only: [:index, :update, :destroy, :create]
  post 'ajax_todos_create', to: 'todos#ajax_create', as: 'ajax_todos_create'
end
```

### フロントエンドの統合
`app/views/layouts/application.html.erb`にて、AJAXサポートのためにjQueryとRails UJSスクリプトを含めてください。

```
    <%= csp_meta_tag %>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@rails/ujs@6.0.2/lib/assets/compiled/rails-ujs.js"></script>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
```

### ビューテンプレート
`index.html.erb`、`_form.html.erb`、`_list.html.erb`、`_show.html.erb`、`ajax_create.js.erb`をセットアップして、メモの非同期作成と表示を可能にします。

index.html.erb
```
<div class="wrapper">
    <div class="container flex-start">
        <div class="col-box-1">
            <h2>メモ一覧</h2>
        </div>
 
        <div class="col-box-3" id="todo-post">
            <%= render "todos/form", todo_new: @todo_new %>
        </div>
     </div>
 
    <div class="container flex-start" id="todo-list">
        <%= render "todos/list", todos: @todos %>
    </div>
</div>
```

_form.html.erb
```
<%= form_with model: todo_new, url: ajax_todos_create_path, local: false, method: :post do |f| %>
    <div class="todo-header">
        <%= f.text_field :title, placeholder: "タイトル", class: "form-box" %>
    </div>
    <div class="todo-footer">
        <%= f.submit "作成", class: "btn" %>
    </div>
<% end %>
```

_list.html.erb
```
<% todos.reverse.each do |todo| %>
    <div class="col-box-3">
        <%= render "todos/show", todo: todo %>
    </div>
<% end %>
```

_show.html.erb
```
<%= form_with model: todo, url: todo_path(todo.id), local: true, method: :patch do |f| %>
    <div class="todo-header">
        <%= f.text_field :title, placeholder: "タイトル", class: "form-box" %>
    </div>
    <div class="todo-center">
        <%= f.text_area :description, placeholder: "メモ", class: "form-box" %>
    </div>
    <div class="todo-footer">
        <%= f.submit "更新", class: "btn" %>
    </div>
<% end %>
<div class="todo-other">
    <%= button_to "削除", todo_path(todo.id), method: :delete, class: "btn" %>
</div>
```




### JavaScriptの応答
`ajax_create.js.erb`を使用して、新しいメモ作成後にページ全体をリロードせずにメモリストを動的に更新します。

ajax_create.js.erb
```
$('#todo-list').html("<%= j(render "todos/list", todos: @todos) %>");
$('#todo-post').find('.form-box').val('');
```