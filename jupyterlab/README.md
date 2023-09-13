# はじめに
containerにインストールしたjupyter-notebookを使う方法をまとめたものです。

# Using my dockerfile
## Mount host folder ~/mounted_folder/sample, onto /work on the docker image
1. 以下のコマンドを実行する
```
mkdir ./mounted_folder/sample
docker build -f ./Dockerfile . -t miharasatsuki/jupyter-notebook
docker run -p 8888:8888 -v ./mounted_folder/sample/:/work --name my-lab miharasatsuki/jupyter-notebook
```
2. ブラウザからlocalhost:8888でアクセスする。
ブラウザ上にworkフォルダができているのでそこにファイルを作成するとhost側に作成されている。

### awsにアクセスする場合(dockerawsにもマウント)
1. 以下のコマンドを実行する
```
docker run -p 8888:8888 -v ./mounted_folder/sample/:/work -v /Users/yukokanai/work/aws/dockeraws/.aws:/root/.aws　--name my-lab miharasatsuki/jupyter-notebook
```
2. ブラウザからlocalhost:8888でアクセスする。

### awsの場合
docker run -v ~:/work -p 8888:8888 <imageID>
https://ec2-18-179-8-155.ap-northeast-1.compute.amazonaws.com:8888/

# Using the docker official image
1. 以下のコマンドを実行する
```
docker run -it -p 8888:8888 --rm jupyter/datascience-notebook bash jupyter notebook
```
2. ブラウザからlocalhost:8888でアクセスする。