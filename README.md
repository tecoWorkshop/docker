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
各コンテナのプロセスは、ホスト Linux 上に用意された仮想ブリッジ「docker0」を通じで通信を行う

コンテナ内部と仮想ブリッジ間は「Virtual Ethernet Tunnel (veth)」によって接続されている<br />
※ veth は仮想 NIC のペアでそれらが直結した状態となっている


Docker はコンテナ毎に veth を用意して、片方の NIC をコンテナ内部に入れ、もう一方の NIC を仮想ブリッジ「docker0」に接続する

コンテナ内部の NIC のデバイス名には「eth0」が割り当てられる

コンテナ内部から外部ネットワークに接続する際には、IP マスカレードにより、ホスト Linux の物理 NIC の IP アドレスを代表アドレスとして接続できる

外部ネットワークからコンテナ内部へ接続する場合は、ポートフォワーディングによりコンテナ内部にパケットを転送できる

同一ホスト内でのコンテナ間では、ポートフォワーディングは必要なく、Docker のリンク機能で通信することができる

## Docker でのデータの管理
Docker でのデータの管理には、データボリュームとデータボリュームコンテナの 2 つの方法がある

### データボリューム（Data Volumes）
データボリュームは複数のコンテナ間でのデータの共有や永続化に対して、以下の様な便利な機能を提供する
* コンテナ間での共有や再利用ができる
* データに対して直接変更ができる
* イメージを更新してもデータボリュームは変更されない
* コンテナが削除されてもデータボリュームは永続化される

#### データボリュームを追加する
`docker run` コマンドに`-v`または`--volume=`オプションをつける

```
$ docker run --name data -v /opt/data_volume busybox

$ docker inspect data

...
    "Mounts": [
        {
            "Name": "8d2536606b67f57c7500a4075eb71077c8e09cedf8af290b2cd5634893ad6748",
            "Source": "/mnt/sda1/var/lib/docker/volumes/8d2536606b67f57c7500a4075eb71077c8e09cedf8af290b2cd5634893ad6748/_data",
            "Destination": "/opt/data_volume",
            "Driver": "local",
            "Mode": "",
            "RW": true
        }
    ],
...
```

#### ホストのディレクトリをデータボリュームとしてマウントする
`-v`オプションにホストのディレクトリを含めることで、コンテナ内にマウントできる

```
docker run -v /Users/<path>:/<container path> ...
```

### データボリュームコンテナ（Data Volumes Containers）
`--volumes-from`オプションで指定したコンテナのデータボリュームをマウントすることができる

`-v`オプションでデータボリュームを持った状態のコンテナ（Data Volume Container）を用意して、そのデータボリュームを`--volumes-from`でアプリケーションを動かすコンテナから参照するという方法がある

データボリュームコンテナを使うことによって、永続データが隔離された状態になるので、ホスト側がコンテナに与える影響を最小限に抑えられる


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

## コンテナの操作
### コンテナの起動
```
$ docker run
```

#### 基本的なオプション
|オプション|説明|
|:--|:--|
|-d, --detach=false              |Run container in background and print container ID|
|-i, --interactive=false         |Keep STDIN open even if not attached|
|-t, --tty=false                 |Allocate a pseudo-TTY|
|--name=                         |Assign a name to the container|

例）CentOS イメージからコンテナを起動して bash を実行する
```
$ docker run -it --name centos centos:centos7 /bin/bash
...
[root@210e39f05121 /]#
```

#### ネットワーク関連のオプション
|オプション|説明|
|:--|:--|
|-p, --publish=[]                |Publish a container's port(s) to the host|
|--link=[]                       |Add link to another container|
|--expose=[]                     |Expose a port or a range of ports|

例）80 番ポートでリクエストを受けて php-fpm に直接接続する
```
$ docker run --name nginx -p 80:80 --link php:php -d sandbox-nginx
```

#### マウントに関するオプション
|オプション|説明|
|:--|:--|
|-v, --volume=[]                 |Bind mount a volume|
|--volumes-from=[]               |Mount volumes from the specified container(s)|


### コンテナの停止、再起動
```
$ docker stop CONTAINER
$ docker start CONTAINER
$ docker restart CONTAINER
```


