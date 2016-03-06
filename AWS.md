# AWS(Amazon Web Services)手順書

## AWSコマンドラインインターフェイスのインストール
-----

[公式サイト](http://aws.amazon.com/jp/cli/)参照。

     sudo apt-get install python-pip
     sudo pip --proxy=http://172.16.40.1:8888 install awscli
    
インスタンス作成
インスタンス作作成後、pemファイルダウンロードされるので、権限を400に変える。

下記のコマンドでアクセスキーを入力して、コマンドラインから操作できるようにする。

     aws configure



## AWS EC2 + Ansible
-----
Ansibeをインストール

     sudo apt-get install software-properties-common
     sudo apt-add-repository ppa:ansible/ansible
     sudo apt-get update
     sudo apt-get install ansible

playbookをAWSで動くように記述する。

hostsファイルのアドレスをEC2のアドレスに書き換える。

playbookを適用させる

     ansible-playbook -i hosts --private-key ./[pemファイル名] playbook.yml

EC2のアドレスにブラウザからアクセスして、wordpressのインストール画面が出てるか確認する。

### AMI(Amazon Machine Image)を作る

自分のインスタンスを選択した状態で、アクションからイメージの作成を選択(インスタンス右クリックでも出てくる)。

イメージ名を記入する。

インスタンスを再起動後、wordpressが表示されるか確認。


環境の構築が終わったら、AMIを作成します。AMIを作成後、同じマシンを2つ起動して、コピーができていることを確認してください。

## AWS EC2(AMIMOTO)
-----

インスタンスの作成からAWS Marketplaceを選択、amimotoで検索してamimoto(HHVM)を選択。

そのままインスタンス作成して、接続確認。

AMIMOTOのWordpressを起動してWordpressが見れることを確認する。

この様にして他の人のAMIを使用してサーバーを起動できる。

## Route53
-----

Route53のHosted ZonesでCreate Hosted Zoneを選択。

Import Zone Fileにzone fileをコピペしてCreateする。

Route53はAWSが提供するDNSサービス。

## S3
-----
コマンドラインから  
バケッドを作る

     aws s3 md se://n14013

これでn140013というバケッドが出来てる。
適当に作ったWebサイトをアップロード

     aws s3 cp --acl public-read index.html s3://n14004/index.html
     
     ※--acl public-read を書かないとアップロードはされるけどWebサイトが見れない。



S3はSimple Storage Service。その名の通り、ファイルを保存し、(状況によっては)公開するサービス。

S3にアップロードする際にはAWSコマンドラインインターフェイスを使う。

## CloudFront
-----

AMIを起動。

CloudFrontの「Create Distribution」からWebで登録。

「Origin Domain Name」のところにEC2の起動したAMIのパブリックDNSをコピペ。

「Origin ID」は適当に書く。

下にスクロールすると「Forward Query Strings」の項目があるから、それをYesにする。

あとはCreate Distributionを押すだけ。完全に起動するまで時間かかる。


CloudFrontはCDNサービスです。
CDNとは、Webコンテンツを配信するのに最適化されたネットワークのこと。

##　RDS
-----

AWSマネジメントコンソールからRDS選択。DBインスタンスの起動。

MySQLを選択。

いいえを選択。

DBインスタンスのクラスを一番最初のmicroって書かれているやつに変更。(安いらしい)

マルチAZ配置→はい

ストレージタイプをマグネティックに。

パブリック・アクセス可能をいいえに変更。

データベースの名前を入れてインスタンス作成。

EC2のインスタンスにsshで接続してRDSのmysqlにログインしてなにも触らずログアウト。

/usr/share/nginx/wordpress/wp-config.phpを削除してブラウザでWordPressを起動、データベースをlocalhostからRDSのDB名に変更

ブラウザからアクセスして動くのを確認。

RDSは…データベースエンジンのこと。例えばMySQLやMariaDBなど。

## ELB
-----

ELBはロードバランサーです。

##　API叩いてみよう
-----

AWSは自分で作ったプログラムからもいろいろ制御できます!
なんでもいいのでがんばってプログラム書いてみてね(おすすめはSES)。


参考（https://github.com/n14004/serverbuilding-2015_repo/blob/master/section6/Section6.md）
