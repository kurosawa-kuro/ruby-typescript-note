# Ruby on Rails ファイルアップロードガイド

このガイドでは、Ruby on Railsを使用してファイルアップロード機能を実装する方法を説明します。

## 環境設定

最初に、Active Storageのインストールを行い、データベースのマイグレーションを実施します。

```
rails active_storage:install
rails db:migrate
```

## モデルの生成

Micropostモデルを生成し、データベースのマイグレーションを実行します。

```
rails g model Micropost title:string
rails db:migrate
```

Micropostモデルには、画像をアタッチするための関連付けを追加します。

```ruby
class Micropost < ApplicationRecord
  has_one_attached :image
end
```

## コントローラの生成

Micropostsコントローラを生成します。

```
rails g controller Microposts
```

## Micropostsコントローラの設定

Micropostsコントローラでは、以下のアクションを定義します。

```ruby
class MicropostsController < ApplicationController
  def create
    @micropost = Micropost.new(micropost_params)
    if @micropost.save
      redirect_to @micropost, notice: 'Micropostが正常に作成されました。'
    else
      render :new
    end
  end

  def index
    @microposts = Micropost.all
  end

  def show
    @micropost = Micropost.find(params[:id])
  end

  private

  def micropost_params
    params.require(:micropost).permit(:title, :image)
  end
end
```

## ルーティングの設定

必要なアクションのルーティングを設定します。

```ruby
Rails.application.routes.draw do
  resources :microposts, only: [:new, :create, :index, :show]
end
```

## ビューの作成

### Indexビュー

```erb
<% @microposts.each do |micropost| %>
  <p>
    <%= link_to micropost do %>
      <%= micropost.title %><br>
      <% if micropost.image.attached? %>
        <%= image_tag(micropost.image, style: 'width:200px;height:200px;object-fit:cover;') %>
      <% end %>
    <% end %>
  </p>
<% end %>
```

### Newビュー

```erb
<%= form_with model: Micropost.new, local: true do |form| %>
  <p>
    <%= form.label :title, 'タイトル' %><br>
    <%= form.text_field :title %>
  </p>

  <p>
    <%= form.label :image, '画像' %><br>
    <%= form.file_field :image %>
  </p>

  <p>
    <%= form.submit '保存' %>
  </p>
<% end %>
```

### Showビュー

```erb
<p>
  <%= @micropost.title %><br>
  <% if @micropost.image.attached? %>
    <%= image_tag(@micropost.image, style: 'width: 400px; height: 400px; object-fit: cover;') %>
  <% end %>
</p>
```

