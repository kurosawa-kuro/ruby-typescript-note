# Ruby on Rails Devise

・[【Rails】devise関連のルーティングまとめ](https://qiita.com/beanzou/items/1ff9c7cba61fd1fa5c80)  
・[Deviseのモヤモヤを解消して快適なRailsライフを送ろう！](https://zenn.dev/kitabatake/articles/start-to-like-the-devise)

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
