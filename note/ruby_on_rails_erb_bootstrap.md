# Ruby on Rails での ERB と Bootstrap のセットアップ手順

## 1. 新しいRailsアプリケーションの作成

```bash
rails new rails-app --skip-bundle
```

## 2. コントローラの作成

```bash
rails generate controller StaticPages home
```

## 3. Bootstrapの導入

Gemfileに以下のgemを追加します。

```ruby
gem "bootstrap-sass", "3.4.1"
gem "sassc-rails", "2.1.2"
```

## 4. スタイルシートの作成

新しいSCSSファイルを作成します。

```bash
touch app/assets/stylesheets/custom.scss
```

`custom.scss` に以下のコードを追加します。

```scss
@import "bootstrap-sprockets";
@import "bootstrap";

/* mixins, variables, etc. */

$gray-medium-light: #eaeaea;

/* universal */

body {
    padding-top: 60px;
}

section {
    overflow: auto;
}

textarea {
    resize: vertical;
}

.center {
    text-align: center;

    h1 {
        margin-bottom: 10px;
    }
}

/* typography */

h1,
h2,
h3,
h4,
h5,
h6 {
    line-height: 1;
}

h1 {
    font-size: 3em;
    letter-spacing: -2px;
    margin-bottom: 30px;
    text-align: center;
}

h2 {
    font-size: 1.2em;
    letter-spacing: -1px;
    margin-bottom: 30px;
    text-align: center;
    font-weight: normal;
    color: $gray-light;
}

p {
    font-size: 1.1em;
    line-height: 1.7em;
}


/* header */

#logo {
    float: left;
    margin-right: 10px;
    font-size: 1.7em;
    color: white;
    text-transform: uppercase;
    letter-spacing: -1px;
    padding-top: 9px;
    font-weight: bold;

    &:hover {
        color: white;
        text-decoration: none;
    }
}

/* footer */

footer {
    margin-top: 45px;
    padding-top: 5px;
    border-top: 1px solid $gray-medium-light;
    color: $gray-light;

    a {
        color: $gray;

        &:hover {
            color: $gray-darker;
        }
    }

    small {
        float: left;
    }

    ul {
        float: right;
        list-style: none;

        li {
            float: left;
            margin-left: 15px;
        }
    }
}
```

## 5. ヘッダーとフッターの部分テンプレート

### ヘッダー (\_header.html.erb)

```erb
<header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", '#', id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home",   '#' %></li>
        <li><%= link_to "Help",   '#' %></li>
        <li><%= link_to "Log in", '#' %></li>
      </ul>
    </nav>
  </div>
</header>
```

### フッター (\_footer.html.erb)

```erb
<footer class="footer">
  <small>
    The <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
    by <a href="https://www.michaelhartl.com/">Michael Hartl</a>
  </small>
  <nav>
    <ul>
      <li><%= link_to "About",   '#' %></li>
      <li><%= link_to "Contact", '#' %></li>
      <li><a href="https://news.railstutorial.org/">News</a></li>
    </ul>
  </nav>
</footer>
```

## 6. メインのレイアウトファイル (application.html.erb)

```erb
<!DOCTYPE html>
<html>
  <head>
    <title><%= yield(:title) %></title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta charset="utf-8">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>
  <body>
    <%= render 'layouts/header' %>
    <div class="container">
      <%= yield %>
      <%= render 'layouts/footer' %>
    </div>
  </body>
</html>
```