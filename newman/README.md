# はじめに
containerにインストールしたnewman(postmanのコマンドライン実行ツール)を使う方法をまとめたものです。

# Using the docker official image
1. 以下のコマンドを実行する
```
docker pull postman/newman
```
2. Run newman commands on the image:
```
docker run -t postman/newman run "https://www.getpostman.com/collections/8a0c9bc08f062d12dcda"
# or
docker run --rm -v ~/work/docker/my_tools/newman:/etc/newman -t postman/newman \
run "collections/udemy-echo-collection.json" \
--environment="environments/udemy-echo-dev-environment.json"
```
# Mount host collections folder ~/collections, onto /etc/newman on the docker image, so that newman
# has access to collections

1. 以下のコマンドを実行する
```
docker-compose build --no-cache
docker-compose up -d
```
2. Run newman commands on the image:
```
docker run -t postman/newman run "https://www.getpostman.com/collections/8a0c9bc08f062d12dcda"
# or
docker run --rm -v ~/work/docker/my_tools/newman:/etc/newman -t postman/newman \
run "collections/udemy-echo-collection.json" \
--environment="environments/udemy-echo-dev-environment.json"
```

# if you want to connect db in container
1. docker-compose.ymlの記述を接続したいpostgresを定義しているcompose-fileに追記します。
以下例
networkの定義がある場合は同一ネットワークに含める（以下例に関して言えばネットワーク設定はしなくても接続できます）
go-rest-apiへアクセスする場合は、newmanのenvファイルでhostnameをgo-rest-api:8080で定義する。
```
version: '3.8'
services:
  go-rest-api:
    image: golang:latest
    # build: .
    container_name: go-rest-api
    volumes:
      - type: bind
        source: "./"
        target: "/go/src/app/go-rest-api"
    working_dir: /go/src/app/go-rest-api
    ports:
      - "8080:8080"
    restart: always
    networks:
      - lesson
    tty: true          #-t ttyを割り当てます。
    stdin_open: true   #-i STDINを開きます。
  newman:
    #image: postman/newman
    build: ~/work/docker/my_tools/newman/.
    container_name: newman
    volumes:
     - ~/work/docker/my_tools/newman/collections:/etc/newman
    networks:
      - lesson
    tty: true          #-t ttyを割り当てます。
    stdin_open: true   #-i STDINを開きます。
networks:
    lesson:
```
2. 以下コマンドを実行する
```
docker exec newman newman run "https://www.getpostman.com/collections/8a0c9bc08f062d12dcda"
# or
# docker exec [container name] [newman command]
docker exec newman newman run "collections/udemy-echo-collection.json" \
--environment="environments/udemy-echo-dev-environment.json"

docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```
# memo
独自に環境を構築する場合、dockerfileのimageをalpineにしてnewmanの実行ファイルをインストールする必要がある。
コマンドが長いが、外部から実行する形の上記でOKとする
なお、image:newmanとするとendpointに「newman」が指定されておりnewman以外のコマンドを受け付けない。