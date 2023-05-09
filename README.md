# README

# 環境構築手順

## 1. Dockerfile 作成
- 既存のままでも rails new 可能(手順 5)
- 内容は file 参照

## 2. docker-compose.yml 作成
- 既存のままでも rails new 可能(手順 5)
- 内容は file 参照

## 3. Gemfile 作成
- 既存のままでも rails new 可能(手順 5)
```
bundle init
```

- 必要があれば、Gemfile の rails の version を任意の version に変更

## 4. Gemfile.lock 作成
- 既存のままでも rails new 可能(手順 5)
- 空の Gemfile.lock を作成

```
touch Gemfile.lock
```

## 5. コンテナ内で rails new を実行

```
docker-compose run rails rails new . --force -d postgresql -j esbuild --skip-bundle
```

- `run`: image の構築から、コンテナの構築・起動まで行う。
- `--force`: ファイルが存在する場合に上書きで作成するためのオプション。
- `-d postgresql`: 使用するデータベースの指定をするためのオプション。
- `--skip-bundle`: bundle install をスキップするためのオプション。

## 6. .env を作成、編集

```
touch .env
```

- 下記を追記

```
POSTGRES_USER="postgres"
POSTGRES_PASSWORD="password"
```

## 7. config/database.yml に下記追加

```
default: &default
  adapter: postgresql
  encoding: unicode
+ host: db
  # For details on connection pooling, see Rails configuration guide
  # https://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
+ username: <%= ENV["POSTGRES_USER"] %>
+ password: <%= ENV["POSTGRES_PASSWORD"] %>
```

## 8. build & bundle install

```
docker-compose build
```

```
docker-compose run rails bundle install
```

- 5 で Gemfile の内容が変更されている為

## 9. 起動

```
docker-compose up -d
```

## 10. database 作成

```
docker-compose run rails rails db:create
```

## frontend と連携させる場合

- [frontend](https://github.com/curtaincall888/next_on_docker) と開発環境で連携させる場合は、下記の network を作成する

```
docker network create external-network --subnet=192.168.192.0/20 --gateway=192.168.192.1
```
