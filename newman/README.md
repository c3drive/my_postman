# はじめに
containerにインストールしたnewman(postmanのコマンドライン実行ツール)を使う方法をまとめたものです。

# Using the docker official image
1. 以下のコマンドを実行する
```
docker pull postman/newman
```
2. Run newman commands on the image:
```
docker run -t postman/newman run "https://www.getpostman.com/collections/8a0c9bc08f062d12dcda" --verbose
```
if Mount host collections folder ~/newman, onto /etc/newman on the docker image, so that newman has access to collections
```
docker run --rm -v ~/work/docker/my_tools/newman:/etc/newman -t postman/newman run "collections/udemy-echo-collection.json" --verbose
```
# if you want to use orign container
# This dockerfile is almost the same as the official one.
# The difference is that the ENTRYPOINT ["newman"] definition is commented out.
# This has the advantage that you can request commands at any time to the container started with your preferred options.
1. 以下のコマンドを実行する
```
cd ~/work/docker/my_tools/newma
docker-compose build --no-cache
docker-compose up -d
```
2. Run newman commands on the image:
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
->docker exec [container name] [newman command]
```
docker exec newman newman run "collections/udemy-echo-collection.json" --verbose
```
## newman options
env file
```
docker exec newman newman run "collections/udemy-echo-collection.json" -e "environments/udemy-echo-dev-environment.json" --verbose
```
specify endpoint
```
docker exec newman newman run "collections/udemy-echo-collection.json" -e "environments/udemy-echo-dev-environment.json" --folder "SignUp" --verbose
```
Save the result of setCookie in jar and Use jar
```
// 保存する
docker exec newman newman run "collections/udemy-echo-collection.json" -e "environments/udemy-echo-dev-environment.json" --folder "Login" --verbose --export-cookie-jar cookies/login.jar
// 使う
docker exec newman newman run "collections/udemy-echo-collection.json" -e "environments/udemy-echo-dev-environment.json" --folder "Tasks" --verbose --cookie-jar cookies/login.jar
```
or Not Use Jar
`udemy-login-collection.json` include Login and Tasks.
```
docker exec newman newman run "collections/udemy-login-collection.json" -e "environments/udemy-echo-dev-environment.json" --verbose
```

# if you want to connect onother container
1. docker-compose.ymlの記述を接続したいcontainerを定義しているcompose-fileに追記します。
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

# memo
独自に環境を構築する場合、dockerfileのimageをalpineにしてnewmanの実行ファイルをインストールする必要がある。
コマンドが長いが、外部から実行する形の上記でOKとする
なお、image:newmanとするとendpointに「newman」が指定されておりnewman以外のコマンドを受け付けない。