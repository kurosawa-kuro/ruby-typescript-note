# RailsプロジェクトでのRSpecセットアップガイド

RSpecはRubyの主要なテストフレームワークの一つです。このガイドではRailsプロジェクトでのRSpecの導入方法を説明します。

## 1. 必要なgemのインストール

### 1.1 `Gemfile`に以下のgemを追加

```ruby
group :development, :test do
  gem 'rspec-rails', '~> 5.0'
  gem 'factory_bot_rails'
  gem 'rubocop', require: false
  gem 'rubocop-performance', require: false
  gem 'rubocop-rails', require: false
  gem 'rubocop-rspec'
end

group :test do
  gem 'faker'
end
```

### 1.2 Bundlerを使用してgemをインストール

```sh
bundle install
```

## Rubocop

touch .rubocop.yml

```
AllCops:
  # 自動生成されるファイルやディレクトリを除外
  Exclude:
    - "vendor/**/*"
    - "db/**/*"
    - "config/**/*"
    - "bin/*"
    - "node_modules/**/*"
  
  # 新しいCopをデフォルトで有効にする
  NewCops: enable

# 行の最大長を制限し、特定のファイルを除外する
Layout/LineLength:
  Max: 130
  Exclude:
    - "Rakefile"
    - "spec/rails_helper.rb"
    - "spec/spec_helper.rb"

# RSpecのブロックは長くなることが多いため、除外する
Metrics/BlockLength:
  Exclude:
    - "spec/**/*"
    - "Guardfile"

# 複雑度に関する各種メトリクスの制限を設定
Metrics/AbcSize:
  Max: 50 # ABCサイズが指定値を超えると警告
Metrics/PerceivedComplexity:
  Max: 8 # 認識される複雑度が指定値を超えると警告
Metrics/CyclomaticComplexity:
  Max: 10 # 循環的複雑度が高すぎると警告
Metrics/MethodLength:
  Max: 30 # メソッドの行数が多すぎると警告
Metrics/BlockNesting:
  Max: 5 # コードのネストが深すぎると警告

# クラスの長さとモジュール内クラスのネストに関するチェックを無効にする
Metrics/ClassLength:
  Enabled: false
Style/ClassAndModuleChildren:
  Enabled: false

# 空のメソッドは複数行スタイルを許可する
Style/EmptyMethod:
  EnforcedStyle: expanded

# 非ASCII文字のコメントを許可する
Style/AsciiComments:
  Enabled: false

# クラスのトップレベルのドキュメントに関するチェックを無効にする
Style/Documentation:
  Enabled: false

# %i()構文によるシンボル配列リテラルのチェックを無効にする
Style/SymbolArray:
  Enabled: false

# 文字列リテラルの変更可能性に関するチェックを無効にする
Style/FrozenStringLiteralComment:
  Enabled: false

# メソッドパラメータ名の最小文字数を設定
Naming/MethodParameterName:
  MinNameLength: 1
```

```
bundle exec rubocop -a
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

