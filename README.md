# Nsnet

Nsnetは、Dockerのオーケストレーションツールである[docker-compose](https://docs.docker.com/compose/)とDockerの構成要素である[Namespace](https://man7.org/linux/man-pages/man7/namespaces.7.html)のLinux管理コマンド(ipコマンド)をラッパーして、ネットワークテスト環境を提供する。

Nsnetでは各マシン(コンテナ)スペックは既存の`docker-compose.yaml`に記述し、  
コンテナ間のネットワークおよびアドレス割り当て、コンテナ起動後に設定するコマンドを独自の`net.yaml`に記述して、コンテナをデプロイすることができる。  

下記の付加価値を既存のdocker-composeに主に提供する。

* `docker-compose.yaml`の記述量の肥大化防止
  * `net.yaml`はcue_languageにより分離カスタム可能
* コンテナ間のネットワーク作成、各コンテナのアドレッシングの簡素化
  * コンテナ名、インタフェース名、IPアドレスを宣言するだけのケーブル/ネットワーク作成
  * bridge接続(コンテナ3つ以上のスイッチ接続)の提供
* コンテナ環境のより高速なスクラッチ&リビルド(コンテナの作成と廃棄)
* 細かいロギングによるデプロイ時のエラーデバックの改善
* Nsnetのコンテナデプロイ時に、`/var/run/netns`からコンテナプロセスのFDにシンボリックリンクを自動生成するため、Dockerコマンドだけでなくipコマンドでもコンテナ(Namespace)が操作可能

## Host Requirement

* OS: `Ubuntu 18.04.6 LTS`
* Do not reference iptables from the bridge
  * ex1: `$ sudo sysctl -w net.bridge.bridge-nf-call-iptables=0`
  * ex2: `$ iptables -I FORWARD -m physdev --physdev-is-bridged -j ACCEPT`
* Python: 3.6.9
  * ex: `$ pyenv install 3.6.9 && pyenv local 3.6.9`
  * Related packages
    * python3-setuptools
    * build-essential
    * libssl-dev
    * libffi-dev
    * python3-dev
      * python3-pip

## Install & Setup

```shell
% git clone https://github.com/shin-ch13/network-lab.git && cd nsnet
# install
% sudo python3 setup.py develop
or 
% sudo pip3 install -e .
# uninstall
% sudo rm /usr/local/lib/python3.6/dist-packages/nsnet-*
% sudo rm /usr/local/bin/nsnet
% sudo rm /usr/local/lib/python3.6/dist-packages/link_dokcer_ns-*
% sudo rm /usr/local/bin/link-dokcer-ns
```

## Usage

nsnet usage

```shell
usage: nsnet [-h] {create,recreate,destroy,shell} ...

Nsnet creat docker container with flexible network

positional arguments:
  {create,recreate,destroy,shell}
    create              Create and start nsnet
    recreate            Recreate and restart nsnet
    destroy             Stop and remove nsnet
    shell               Shell connect container

optional arguments:
  -h, --help            show this help message and exit
```

optional cue language usage to generate net.yaml from scattered yaml file

```shell
% docker run --rm -it -v $PWD:/cue/app -w /cue/app shinch13/cue:0.4.3 cue export config/networks*.yaml config/commands*.yaml --out yaml > net.yaml
```

## Run Example

```shell
% cd nsnet/test/simple_network

### check config file to generate net.yaml
% ls config/
### generate net.yaml
% docker run --rm -it -v $PWD:/cue/app -w /cue/app shinch13/cue:0.4.3 cue export config/networks*.yaml config/commands*.yaml --out yaml > net.yaml

### create docker container enviroment with network
% sudo nsnet create -cf docker-compose.yml -nf net.yaml
### "node1" is service name in docker-compose.yml
### Login Terminal of "node1"
% sudo nsnet shell node1
  root@xxxxx:/# ip a
  root@xxxxx:/# ping 20.0.0.2
  root@xxxxx:/# ping6 2001:2222:2222::2
  root@xxxxx:/# exit

### after edit docker-compose.yml or net.yaml
% docker run --rm -it -v $PWD:/cue/app -w /cue/app shinch13/cue:0.4.3 cue export config/networks*.yaml config/commands*.yaml --out yaml > net.yaml
% sudo nsnet recreate

### destroy docker container enviroment
% sudo nsnet destroy
```

## Links

* <https://github.com/buzz66boy/YNTDL/tree/master/resources>
* <https://nmstate.io/examples.html#interfaces-ethernet>
* <https://qiita.com/zen3/items/757f96cbe522a9ad397d>
* <https://docs.openstack.org/project-deploy-guide/tripleo-docs/latest/features/custom_networks.html>
* <https://access.redhat.com/documentation/ja-jp/red_hat_openstack_platform/14/html/advanced_overcloud_customization/custom-network-interface-templates>
* <https://www.maytry.net/how-to-use-yaml/>
* <https://www.task-notes.com/entry/20150922/1442890800>
* <https://www.lifewithpython.com/2013/06/pyyaml.html>
* <https://github.com/Grokzen/pykwalify/blob/master/docs/validation-rules.rst>
* <https://docs.python.org/ja/3.7/library/ipaddress.html>
