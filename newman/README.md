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
```
# Mount host collections folder ~/collections, onto /etc/newman on the docker image, so that newman
# has access to collections
```
docker run -v ~/collections:/etc/newman -t postman/newman run "HTTPBinNewmanTestNoEnv.json.postman_collection"
```

# memo
独自に環境を構築する場合、dockerfileのimageをalpineにしてnewmanの実行ファイルをインストールする必要がある。
コマンドが長いが、外部から実行する形の上記でOKとする
なお、image:newmanとするとendpointに「newman」が指定されておりnewman以外のコマンドを受け付けない。