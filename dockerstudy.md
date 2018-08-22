# Dockerについて
## docker-compose.ymlについて
- Dockerで複数のコンテナを設定に従ってまとめて起動する構成ファイル
- version: フォーマットのバージョン
- web: Railsのコンテナ
- db: MySQLのコンテナ


## コンテナ内にRailsを作成
```
$ docker-compose run web rails new . --force --database=mysql
```

- `docker-compose run web` の部分はwebサービスのコンテナで後ろのコマンドを実行するコマンド
- `rails new .` でコマンド実行時のディレクトリ上に新しいRailsプロジェクトのファイルを作成
- `--force` は既存ファイルを上書きする
- `--database=mysql` はMySQLを使用する設定を入れる


## Dockerfileからイメージをビルドする

```
dockedr-compose build
```
- サービスの構築、再構築
- Rails用ファイルの生成によりGemfileに追記された項目などを再度取り込む意味でもbuildが必要


## コンテナの起動
```
docker-compose up -d
```

-  デフォルトは「アタッチド」モードであり、全てのコンテナのログが画面上に表示される。
- 「デタッチド」モード（ -d ）では、Compose はコンテナを実行すると終了するが、コンテナは後ろで動き続ける 。



## サービスの停止

```
docker-compose stop
```

サイトにアクセスできなくなる


## コンテナの削除

```
docker-compose down
```


## コンテナの起動確認
```
docker-compose ps
```


## コンテナが起動しないとき
server.pidファイルが原因の可能性があるので、削除してみる。

```
rm tmp/pids/server.pid
```


## コンテナ内のMySQLにデータベースを作成

```
docker-compose run web bundle exec rails db:create
```

# Railsについて

## GemfileやGemfile.lockに追記、変更したらbuildのあとにup
```
docker-compose build
docker-compose up -d
```

buildすると以下が走ってGemfileが上書きされる

```Dockerfile
COPY Gemfile /app/Gemfile
COPY Gemfile.lock /app/Gemfile.lock
```


## モデルおよびマイグレーションファイルの生成

```
docker-compose run web bundle exec rails g model board name:string title:string body:text
```

- `docker-compose run web`: webサービスのコンテナ内で後ろに続くコマンドを実行することを意味する
- `bundle exec rails g model`: マイグレーションファイルとモデルのファイルが作成される
- `board`: 作成するモデル名。boardモデルの作成によりboardsテーブルを作成するマイグレーションファイルが作成される
- `name:string title:string body:text`: boardsテーブルに作成するカラム:カラムの型


### 実行結果

```sh
      invoke  active_record
      create    db/migrate/20180822140306_create_boards.rb # マイグレーションファイルの生成
      create    /models/board.rb # boardモデル
      invoke    test_unit
      create      test/models/board_test.rb
      create      test/fixtures/boards.yml
```


## マイグレーションの実行

DBが作成される

```
docker-compose run web bundle exec rails db:migrate
```

実行したマイグレーションを取り消したい場合はロールバックする

```
rails db:rollback
```

