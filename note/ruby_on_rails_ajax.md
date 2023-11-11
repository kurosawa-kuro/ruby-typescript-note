## Ruby on RailsによるAJAXメモアプリケーション

### プロジェクトのセットアップ
1. 新しいRailsアプリケーションの作成:
   ```shell
   rails new rails-memo-ajax
   ```
2. 作成したアプリケーションディレクトリへの移動:
   ```shell
   cd rails-memo-ajax
   ```

### モデルの作成
1. タイトルと説明を持つ`Memo`モデルの生成:
   ```shell
   rails g model Memo title:string description:text
   ```
2. `memos`テーブルを作成するためのデータベースマイグレーションの実行:
   ```shell
   rails db:migrate
   ```

### コントローラーとビューのセットアップ
1. 特定のアクションを持つ`Memos`コントローラーの生成:
   ```shell
   rails generate controller Memos index create update destroy ajax_create
   ```
2. 必要なビューファイルの作成:
   ```shell
   touch app/views/memos/_form.html.erb
   touch app/views/memos/_list.html.erb
   touch app/views/memos/_show.html.erb
   touch app/views/memos/ajax_create.js.erb
   touch app/views/memos/index.html.erb
   ```

### コントローラーアクションの実装
以下のようにコントローラーアクションを実装してください：
```ruby
# app/controllers/memos_controller.rb
class MemosController < ApplicationController
  def index
    @memo_new = Memo.new
    @memos = Memo.all
  end
 
  def create
    @memo_new = Memo.new(memos_params)
    @memo_new.save
    redirect_to root_path
  end
 
  def update
    @memo = Memo.find(params[:id])
    @memo.update(memos_params)
    redirect_to root_path
  end
 
  def destroy
    @memo = Memo.find(params[:id])
    @memo.destroy
    redirect_to root_path
  end

  def ajax_create
    @memo_new = Memo.new(memos_params)
    @memo_new.save
    @memos = Memo.all
  end
 
  private
 
  def memos_params
    params.require(:memo).permit(:title, :description)
  end
end
```

### ルーティングの設定
```ruby
Rails.application.routes.draw do
  root :to => 'memos#index'
  resources :memos, only: [:index, :update, :destroy, :create]
  post 'ajax_memos_create', to: 'memos#ajax_create', as: 'ajax_memos_create'
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
 
        <div class="col-box-3" id="memo-post">
            <%= render "memos/form", memo_new: @memo_new %>
        </div>
     </div>
 
    <div class="container flex-start" id="memo-list">
        <%= render "memos/list", memos: @memos %>
    </div>
</div>
```

_form.html.erb
```
<%= form_with model: memo_new, url: ajax_memos_create_path, local: false, method: :post do |f| %>
    <div class="memo-header">
        <%= f.text_field :title, placeholder: "タイトル", class: "form-box" %>
    </div>
    <div class="memo-center">
        <%= f.text_area :description, placeholder: "メモ", class: "form-box" %>
    </div>
    <div class="memo-footer">
        <%= f.submit "作成", class: "btn" %>
    </div>
<% end %>
```

_list.html.erb
```
<% memos.reverse.each do |memo| %>
    <div class="col-box-3">
        <%= render "memos/show", memo: memo %>
    </div>
<% end %>
```

_show.html.erb
```
<%= form_with model: memo, url: memo_path(memo.id), local: true, method: :patch do |f| %>
    <div class="memo-header">
        <%= f.text_field :title, placeholder: "タイトル", class: "form-box" %>
    </div>
    <div class="memo-center">
        <%= f.text_area :description, placeholder: "メモ", class: "form-box" %>
    </div>
    <div class="memo-footer">
        <%= f.submit "更新", class: "btn" %>
    </div>
<% end %>
<div class="memo-other">
    <%= button_to "削除", memo_path(memo.id), method: :delete, class: "btn" %>
</div>
```




### JavaScriptの応答
`ajax_create.js.erb`を使用して、新しいメモ作成後にページ全体をリロードせずにメモリストを動的に更新します。

ajax_create.js.erb
```
$('#memo-list').html("<%= j(render "memos/list", memos: @memos) %>");
$('#memo-post').find('.form-box').val('');
```