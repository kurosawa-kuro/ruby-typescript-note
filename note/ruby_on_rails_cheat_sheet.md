# Ruby on Rails チートシート

このチートシートは、Ruby on Railsのプロジェクトのセットアップ、クリーンアップ、およびコード生成の基本的なコマンドを提供します。

## クリーンアップ

下記のコマンドで、プロジェクト内の全ての`.git`ディレクトリを削除します。

```sh
find . -type d -name ".git" -exec rm -rf {} +
```

## セットアップ

新しいRailsプロジェクトを作成し、PostgreSQLデータベースを設定します。

1. 新しいプロジェクトの作成:

   ```sh
   rails new project_name --api --database=postgresql
   ```

2. プロジェクトディレクトリに移動:

   ```sh
   cd project_name
   ```

3. PostgreSQLデータベースのセットアップスクリプトを実行:

   ```sh
   cd setup
   ./setup_postgresql_db.sh
   ```

4. データベースのドロップ、作成、マイグレーション:

   ```sh
   bundle exec rails db:drop
   bundle exec rails db:create
   bundle exec rails db:migrate
   ```

## ジェネレート

モデル、コントローラー、およびスキャフォールドのコードを生成します。

1. Todoモデルの生成:

   ```sh
   rails generate model Todo title:string
   ```

2. Todosコントローラーの生成:

   ```sh
   rails generate controller Todos
   ```

3. Todoスキャフォールドの生成:

   ```sh
   rails generate scaffold Todo title:string
   ```

これで、基本的なRailsプロジェクトのセットアップとコード生成ができます。必要に応じてコマンドをカスタマイズして使用してください。
