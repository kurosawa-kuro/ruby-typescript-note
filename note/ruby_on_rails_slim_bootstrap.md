# Slim と Bootstrap の導入方法 (Ruby on Rails)

## 1. 新しいRailsアプリケーションを作成

```bash
rails new rails-app --skip-bundle
```

## 2. コントローラの生成

```bash
rails generate controller StaticPages home
```

## 3. 必要なgemのインストール

Gemfileに以下を追加:

```ruby
gem "bootstrap-sass", "3.4.1"
gem "sassc-rails", "2.1.2"
gem 'slim-rails'
```

その後、以下のコマンドでgemをインストール:

```bash
bundle
```

## 4. Bootstrapのスタイルシートの設定

新しいSCSSファイルを作成:

```bash
touch app/assets/stylesheets/custom.scss
```

そして、`custom.scss` にBootstrapの設定やカスタムスタイルを追加。

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

## 5. Slimでのヘッダーとフッターのテンプレート

### ヘッダー: `_header.html.slim`

```slim
header.navbar.navbar-fixed-top.navbar-inverse
  .container
    = link_to "sample app", '#', id: "logo"
    nav
      ul.nav.navbar-nav.navbar-right
        li = link_to "Home", '#'
        li = link_to "Help", '#'
        li = link_to "Log in", '#'
```

### フッター: `_footer.html.slim`

```slim
footer.footer
  small
    | The 
    a href="https://railstutorial.jp/" Ruby on Rails Tutorial
    | by 
    a href="https://www.michaelhartl.com/" Michael Hartl
  nav
    ul
      li = link_to "About", '#'
      li = link_to "Contact", '#'
      li
        a href="https://news.railstutorial.org/" News
```

## 6. メインのレイアウトテンプレート

### application.html.slim:

```slim
doctype html
html
  head
    title= yield(:title)
    meta name="viewport" content="width=device-width, initial-scale=1"
    meta charset="utf-8"
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag "application", "data-turbo-track": "reload"
    = javascript_importmap_tags
  body
    = render 'layouts/header'
    .container
      = yield
      = render 'layouts/footer'
```

### メーラーレイアウト:

- `app/views/layouts/mailer.html.slim`:

```slim
doctype html
html
  head
    meta http-equiv="Content-Type" content="text/html; charset=utf-8"
    style
      | /* Email styles need to be inline */
  body
    = yield
```

- `app/views/layouts/mailer.text.slim`:

```slim
= yield
```

### 静的ページの例:

- `app/views/static_pages/home.html.slim`:

```slim
h1 StaticPages#home
p Find me in app/views/static_pages/home.html.slim
```