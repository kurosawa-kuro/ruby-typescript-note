# 予約ウェブアプリケーションの構築：React & Rails

## 1. モデルの生成

### 1.1 ScaffoldによるCRUD操作の生成

各テーブルのCRUD操作をScaffoldを使用して生成します。

```bash
rails generate scaffold User name:string email:string:uniq password:string admin:boolean

rails generate scaffold Room room_number:string:uniq description:text capacity:integer price_per_night:decimal room_type:string image_path:string

rails generate scaffold Booking user:references room:references start_date:date end_date:date total_price:decimal

rails generate scaffold Tag name:string:uniq

rails generate model RoomTag room:references tag:references

```

### 1.2 モデルの関連付け

モデル間の関係を以下のように定義します。

```ruby
class User < ApplicationRecord
  has_many :bookings
end

class Room < ApplicationRecord
  has_many :bookings
  has_many :room_tags, dependent: :destroy
  has_many :tags, through: :room_tags
end

class Booking < ApplicationRecord
  belongs_to :user
  belongs_to :room
end

class Tag < ApplicationRecord
  has_many :room_tags, dependent: :destroy
  has_many :rooms, through: :room_tags
end

class RoomTag < ApplicationRecord
  belongs_to :room
  belongs_to :tag
end
```

## 2. データの初期化

### 2.1 シードデータの準備

以下のデータを`db/seeds.rb`に記述して、初期データを設定します。

```ruby
# db/seeds.rb

# Users
user_attributes = [
  {
    name: 'admin User',
    email: 'admin@example.com',
    password: 'password',
    admin: true
  },
  {
    name: 'Kurosawa Toshifumi',
    email: 'kurosawa@example.com',
    password: 'password',
    admin: false
  },
  {
    name: 'Taro Yamada',
    email: 'yamada@example.com',
    password: 'password',
    admin: false
  }
]

User.create!(user_attributes)

# Image paths for rooms
image_paths = [
  'https://static.amanaimages.com/imgroom/rf_preview640/11014/11014019870.jpg',
  'https://pro.foto.ne.jp/free/img/images_big/sit0009-001.jpg',
  'https://gahag.net/img/201605/23s/gahag-0088897439-1.jpg',
  'http://www.appli-cation.com/photo/wp-content/uploads/2015/08/0192.jpg',
  'https://static.amanaimages.com/imgroom/rf_preview640/10904/10904001375.jpg',
  'https://pro.foto.ne.jp/free/img/images_big/sit0009-001.jpg'
]

# Rooms
room_attributes = [
  {
    room_number: '101',
    description: 'オーシャンビューのデラックスシングルルーム',
    capacity: 1,
    price_per_night: 100.0,
    room_type: 'Single',
    image_path: image_paths[0]
  },
  {
    room_number: '102',
    description: '庭の景色が楽しめるデラックスダブルルーム',
    capacity: 2,
    price_per_night: 180.0,
    room_type: 'Double',
    image_path: image_paths[1]
  },
  {
    room_number: '103',
    description: 'バルコニー付きのエグゼクティブスイートルーム',
    capacity: 4,
    price_per_night: 350.0,
    room_type: 'Suite',
    image_path: image_paths[2]
  },
  {
    room_number: '104',
    description: '快適なダブルルーム',
    capacity: 2,
    price_per_night: 200.0,
    room_type: 'Double',
    image_path: image_paths[3]
  },
  {
    room_number: '105',
    description: 'ゆったりとしたスイートルーム',
    capacity: 3,
    price_per_night: 250.0,
    room_type: 'Suite',
    image_path: image_paths[4]
  },
  {
    room_number: '106',
    description: 'シンプルで機能的なシングルルーム',
    capacity: 1,
    price_per_night: 150.0,
    room_type: 'Single',
    image_path: image_paths[5]
  }
]

Room.create!(room_attributes)

# Bookings
booking_attributes = [
  {
    user_id: User.first.id,
    room_id: Room.first.id,
    start_date: Date.today + 7,
    end_date: Date.today + 10,
    total_price: 300.0
  },
  {
    user_id: User.second.id,
    room_id: Room.second.id,
    start_date: Date.today + 5,
    end_date: Date.today + 8,
    total_price: 540.0
  },
  {
    user_id: User.third.id,
    room_id: Room.third.id,
    start_date: Date.today + 10,
    end_date: Date.today + 14,
    total_price: 1400.0
  }
]

Booking.create!(booking_attributes)

# Tags
tags = ['ビューオーシャン', 'シングル', 'デラックス', 'スイート', 'ペット可']
tag_instances = Tag.create!(tags.map { |name| { name: name } })

# Associate tags with rooms
Room.all.each do |room|
  room.tags << tag_instances.sample(rand(1..3))
end
```

## 3. 認証機能の実装

### 3.1 Authコントローラの生成

認証のためのコントローラを生成します。

```bash
rails generate controller Auth
```

### 3.2 認証ルーティングの設定

以下のルーティングを追加して、ユーザーの登録、ログイン、ログアウトのエンドポイントを設定します。

```ruby
Rails.application.routes.draw do
  scope 'auth' do
    post 'register', to: 'auth#register'
    post 'login', to: 'auth#login'
    delete 'logout', to: 'auth#logout'
  end

  resources :bookings
  resources :rooms
  resources :users
end
```

### 3.3 Authコントローラの実装

認証に関する処理を`AuthController`に実装します。

```ruby
class AuthController < ApplicationController
  # ユーザー登録
  def register
    user = User.new(register_params)
    user.password = params[:user][:password] # 練習用のため、パスワードの暗号化をしない

    if valid_password_confirmation? && user.save
      session[:user_id] = user.id
      render json: { status: 'SUCCESS', data: user }
    else
      render json: { status: 'ERROR', data: user.errors }
    end
  end

  # ログイン
  def login
    user = User.find_by(email: params[:email])

    # パスワードの一致を確認
    if user && user.password == params[:password]
      session[:user_id] = user.id
      render json: { status: 'SUCCESS', data: user }
    else
      render json: { status: 'ERROR', message: 'Invalid email or password' }
    end
  end

  # ログアウト
  def logout
    reset_session
    render json: { status: 'SUCCESS', message: 'Logged out' }
  end

  private

  def register_params
    params.require(:user).permit(:email, :password)
  end

  def valid_password_confirmation?
    params[:user][:password] == params[:user][:password_confirmation]
  end
end
```

