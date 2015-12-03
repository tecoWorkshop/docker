# Docker

## Docker とは
Docker社（旧 dotCloud）が開発するオープンソースのコンテナ管理ソフトウェア

コンテナとは Web サーバなどアプリケーションの実行環境を抽象化する技術であり、
VMware ESXi や Linux KVM などの「ハイパーバイザー型の仮想化」に対して、「コンテナ型の仮想化」と呼ばれることもある

コンテナ管理ソフトウェア自体の歴史は古く、
UNIX / Linux であれば「FreeBSD Jails」「OpenVZ」「LXC（Linux Container）」 Windows アプリケーション向けでは「VMware ThinApp」などがある

### Docker の主な機能と特徴

Docker は、コンテナでアプリケーションを実行するために以下の機能を持つ

* コンピュータリソースの隔離および制限
* 他のホスト、他のコンテナとのネットワークの構成
* ファイル／ディレクトリの世代と差分の管理

Docker の特徴の1つとして、新しい独自技術はほとんど使っていないということがある<br>
コンテナ管理に必要となる既存の OSS （オープンソースソフトウェア）を組み合わせて、
コマンドラインおよび REST API で手軽かつ効率良くコンテナ管理ができるようになっている

## Docker Toolbox

Docker Toolbox は、Docker 環境を簡単に構築するための Mac OS X・Windows 向けのインストーラ

Docker は、Linux 上で動作するため、OS X (Mac) や Windows では、Docker が動作する Linux 仮想マシンを準備し、それを利用する形態をとる

Docker Toolbox は、この Docker 環境を構築・運用するためのアプリケーションやツールをセットにして提供 する

|ツール|概要|
|:--|:--|
|Docker Client|Docker を操作するツール（docker コマンド）|
|Docker Machine|Docker 仮想マシンを簡単に構築・管理するためのツール（docker-machine コマンド）|
|Docker Compose|複数のコンテナで構成するシステムを定義・実行するためのツール（docker-compose コマンド）<br>OS X (Mac) のみ|
|Docker Kitematic|Docker を GUI で操作するツール<br>現在は、β版|
|Docker Quickstart Terminal|docker コマンドが即利用できるターミナル（Docker 仮想マシンを準備してくれる）|
|VirtualBox|仮想化アプリケーション|

### インストール

https://www.docker.com/docker-toolbox

Homebrew を使う場合
```
$ brew cask install dockertoolbox
```

## イメージとコンテナ
### コンテナとは
Linux 上で稼働するプロセスを複数のグループに分割し、それぞれのグループ毎に異なる環境設定を実現する。つまり、複数のユーザ空間を用意し、それぞれについて異なるディスクの内容を見せたり、異なるネットワーク設定を割り当てる。このユーザ空間のことをコンテナという。

### コンテナイメージとは
コンテナのテンプレート。イメージを元にして新しいコンテナを作成する。また、稼働中のコンテナのイメージを作成することも出来る。

## ディスクの管理
chroot の機能とほぼおなじ

ディスクイメージの内容をホスト Linux 上にマウントし、それをコンテナのルートファイル・システムとして割り当てる

CentOS 7 のディスク内容をコピーしたディスクイメージをコンテナに割り当てれば、コンテナ内部では CentOS 7 の環境と同じディスクの内容が見えることになる

## ネットワークの管理

## ボリューム



## 構成情報の確認
### バージョン確認
Docker のクライアントとサーバのバージョンを表示する

```
$ docker version
Client:
 Version:      1.8.2
 API version:  1.20
 Go version:   go1.4.2
 Git commit:   0a8c2e3
 Built:        Thu Sep 10 19:10:10 UTC 2015
 OS/Arch:      darwin/amd64

Server:
 Version:      1.9.1
 API version:  1.21
 Go version:   go1.4.3
 Git commit:   a34a1d5
 Built:        Fri Nov 20 17:56:04 UTC 2015
 OS/Arch:      linux/amd64
```

### 構成情報を表示する

```
$ docker info
Containers: 1
Images: 33
    .
    .
    .
```

## Docker イメージの管理
### イメージのダウンロード
```
$ docker pull NAME
```

### イメージ一覧表示
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
jenkins             latest              2172b036c2b9        2 weeks ago         707.8 MB
```

### イメージの削除
```
$ docker rmi REPOSITORY:TAG
    or
$ docker rmi IMAGE ID
```




