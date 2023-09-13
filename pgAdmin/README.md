# はじめに
containerにインストールしたpdAdminを使う方法をまとめたものです。

# Using the docker official image
1. 以下のコマンドを実行する
```
docker pull dpage/pgadmin4
```
2. Run psAdminServer on the image:
```
docker run --name "pgadmin4" \
    -e "PGADMIN_DEFAULT_EMAIL=user@domain.com" \
    -e "PGADMIN_DEFAULT_PASSWORD=SuperSecret" \
    -e "SCRIPT_NAME=/pgadmin4" \
    -l "traefik.frontend.pgadmin4.rule=Host(`host.example.com`) && PathPrefix(`/pgadmin4`)" \
    -d dpage/pgadmin4
```
# Mount host collections folder ~/collections, onto /etc/newman on the docker image, so that newman
# has access to collections
1. 以下のコマンドを実行する
```
docker-compose up -d
```
2. ブラウザからlocalhost:5050でアクセスする。
3. 以下の情報でログインする。
```
        PGADMIN_DEFAULT_EMAIL: admin@admin.com
        PGADMIN_DEFAULT_PASSWORD: root
```
4. 新しいサーバを追加から利用するpostgresサーバの情報を入力する


# if you want to connect db in container
1. docker-compose.ymlの記述を接続したいpostgresを定義しているcompose-fileに追記します。
以下例
networkの定義がある場合は同一ネットワークに含める（以下例に関して言えばネットワーク設定はしなくても接続できます）
```
version: '3.8'
services:
  db:
    container_name: pg_container
    image: postgres
    restart: always
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
      POSTGRES_DB: test_db
    ports:
      - "5432:5432"
    # networks:
    #   - lesson
  pgadmin:
    container_name: pgadmin4_container
    image: dpage/pgadmin4
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: root
    ports:
      - "5050:80"
    # networks:
    #   - lesson

# networks:
#     lesson:
```
2. ブラウザからlocalhost:5050でアクセスする。
3. 以下の情報でログインする。
```
        PGADMIN_DEFAULT_EMAIL: admin@admin.com
        PGADMIN_DEFAULT_PASSWORD: root
```
4. 新しいサーバを追加から利用するpostgresサーバの情報を入力する
以下例
```
POSTGRES_USER=postgres
POSTGRES_PW=postgres
POSTGRES_DB=postgres
POSTGRES_PORT=5432 # 5434ではないので注意
POSTGRES_HOST=pg_container # localhostではないので注意（コンテナ名）
```