# RailsプロジェクトでのRSpecセットアップガイド

RSpecはRubyの主要なテストフレームワークの一つです。このガイドではRailsプロジェクトでのRSpecの導入方法を説明します。

## 1. 必要なgemのインストール

### 1.1 `Gemfile`に以下のgemを追加

```ruby
group :development, :test do
  gem 'rspec-rails', '~> 5.0'
  gem 'factory_bot_rails'
end

group :test do
  gem 'faker'
end
```

### 1.2 Bundlerを使用してgemをインストール

```sh
bundle install
```

## 2. RSpecの初期設定を行う

```sh
rails generate rspec:install
```

## 3. 既存の`test`ディレクトリを削除

```sh
rm -rf test
```

## 4. ヘルパーモジュールのセットアップ

### 4.1 `spec/support/response_debugger.rb`を作成

```ruby
module ResponseDebugger
  def print_response_details
    puts ""
    puts "Status: #{response.status}"
    puts "Headers: #{response.headers.select { |k, v| ['Content-Type', 'X-Frame-Options'].include? k }}"
    puts "Request Params: #{response.request.params.inspect}"

    if response.body.present?
      puts "Body: #{JSON.pretty_generate(JSON.parse(response.body))}"
    else
      puts "Body: Empty"
    end
  end
end
```

### 4.2 `spec/rails_helper.rb`にヘルパーを追加

```ruby
require 'support/response_debugger'

RSpec.configure do |config|
  # arbitrary gems may also be filtered via:
  # config.filter_gems_from_backtrace("gem name")
  config.include FactoryBot::Syntax::Methods
  config.include ResponseDebugger
end
```

## 5. Sampleアプリケーションのセットアップ

### 5.1 Scaffoldを生成し、それを修正

- **Scaffoldの生成**

```bash
rails generate scaffold Sample title:string
```

- **コントローラーの修正**

```ruby
# app/controllers/samples_controller.rb

class SamplesController < ApplicationController
  def index
    @samples = Sample.all
    render json: @samples
  end

  def create
    @sample = Sample.new(sample_params)
    if @sample.save
      render json: @sample, status: :created
    else
      render json: @sample.errors, status: :unprocessable_entity
    end
  end

  private

  def sample_params
    params.require(:sample).permit(:title)
  end
end
```

### 5.2 Sampleモデルにバリデーションを追加

```ruby
class Sample < ApplicationRecord
  validates :title, presence: true
end
```

### 5.3 マイグレーションを実行

```bash
rails db:migrate
```

### 5.4 シードデータを作成して適用

- **`db/seeds.rb`を編集**

```ruby
# ...

10.times do |i|
  Sample.create!(title: "Sample Title #{i + 1}")
end
```

- **シードデータをデータベースに適用**

```bash
rails db:seed
```

### 5.5 Sampleのモデルとルーティングのテストを書く

- **モデルのテスト**

```ruby
# spec/models/sample_spec.rb
require 'rails_helper'

RSpec.describe Sample, type: :model do
  subject { described_class.new(title: "Valid Title") }

  it "is valid with a title" do
    expect(subject).to be_valid
  end

  it "is not valid without a title" do
    subject.title = nil
    expect(subject).not_to be_valid
  end
end
```

- **ルーティングのテスト**

```ruby
# samples_routing_spec.rb
require "rails_helper"

RSpec.describe SamplesController, type: :routing do
  describe "routing" do
    it "routes to #index" do
      expect(get: "/samples").to route_to("samples#index")
    end

    it "routes to #create" do
      expect(post: "/samples").to route_to("samples#create")
    end
  end
end
```

### 5.6 Sampleエンドポイントのテストを書く

```ruby
# spec/requests/samples_spec.rb

require 'rails_helper'

RSpec.describe "/samples", type: :request do
  let(:valid_attributes) { { title: "Valid Sample Title" } }
  let(:invalid_attributes) { { title: "" } }
  let(:valid_headers) { {} }

  describe "GET /index" do
    let!(:sample) { Sample.create! valid_attributes }
    before { get samples_url, headers: valid_headers, as: :json }

    it "renders a successful response" do
      print_response_details
      expect(response).to be_successful
      expect(response.parsed_body.first["title"]).to eq(sample.title)
    end
  end

  describe "POST /create" do
    context "with valid parameters" do
      before { post samples_url, params: { sample: valid_attributes }, headers: valid_headers, as: :json }

      it "creates a new Sample" do
        expect(Sample.last.title).to eq("Valid Sample Title")
      end

      it "renders a JSON response with the new sample" do
        expect(response).to have_http_status(:created)
        expect(response.content_type).to match(a_string_including("application/json"))
        expect(response.parsed_body["title"]).to eq("Valid Sample Title")
      end
    end

    context "with invalid parameters" do
      before { post samples_url, params: { sample: invalid_attributes }, headers: valid_headers, as: :json }

      it "does not create a new Sample" do
        expect(response).to have_http_status(:unprocessable_entity)
      end

      it "renders a JSON response with errors for the new sample" do
        expect(response.content_type).to match(a_string_including("application/json"))
        expect(response.parsed_body["title"]).to include("can't be blank")
      end
    end
  end
end
```

### 5.7 不要なファイルを検索して削除

- **検索**

```sh
find . -type f -name "*sample*" ! -path "./tmp/*" ! -path "./.git/*"
```

- **削除**

```sh
find . -type f -name "*sample*" -print0 | xargs -0 rm -f
```

- **再確認**

```sh
find . -type f -name "*sample*" ! -path "./tmp/*" ! -path "./.git/*"
```

