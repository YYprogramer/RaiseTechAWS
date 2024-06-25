# 第５回課題の実装

---
#### 実装内容　第３回課題のサンプルアプリケーションを第４回課題で構築したVPC上にデプロイ

---

## 動作環境の構築
サンプルアプリケーションを動作させるための環境を確認します。  
[サンプルアプリケーションのリンク](https://github.com/yuta-ushijima/raisetech-live8-sample-app)

| パッケージ   | バージョン | 目的                                 |
|---------|-------|------------------------------------|
| Ruby    |3.2.3| アプリケーションを実行するため                    |
| Bundler |2.3.14| Rubyを起動させるために必要な依存環境を適切に管理してくれるツール |
| Rails   |7.1.3.2| Ruby用のWebアプリケーションフレームワーク           |
| Node    |v17.9.1| JavaScriptを実行するツール                 |
| yarn    |1.22.19| JavaScriptのパッケージ管理ツール              |
※令和６年６月時点のバージョンです

## 実装手順
### 1. EC2に接続
```
ssh -i キーペア名.pem ec2-user@<パブリックIP>
```
### 2. EC2上にRuby on Rails環境を構築するための基本的なツールとライブラリをインストール
```
sudo yum -y update
```
```
sudo yum  -y install git make gcc-c++ patch libyaml-devel libffi-devel libicu-devel zlib-devel readline-devel libxml2-devel libxslt-devel ImageMagick ImageMagick-devel openssl-devel libcurl-devel curl
```
### 3.rbenvをインストール 
`rbenv`とは複数のバージョンのrubyをまとめて管理するツールです。
```
git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
```
ターミナルでrbenvコマンドが実行できるようにパスを追加します
```
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
```
シェルを起動するたびにrbenvが正しく初期化される設定を行います
```
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
```
.bashrcファイルを再読み込みさせ上記２つの設定を反映させます
```
source ~/.bash_profile
```
### 4.ruby-buildをインストール
`ruby-build`とはrbenvのプラグインの一つです。特定のバージョンのRubyをインストールすることができます。  
.bashrcファイルを再読み込みさせ上記２つの設定を反映させます
```
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```
### 5.Rubyをインストール
ダウンロード可能なRubyのバージョンリストを表示させ,
3.2.3が含まれていることを確認します。
```
rbenv install --list-all
```
Ruby3.2.3をインストールします。結構時間がかかります。
```
rbenv install 3.2.3
```
デフォルトで使用すRubyが3.2.3になるよう設定します。
```
rbenv global 3.2.3
```
Rubyのバージョンを確認します
```
ruby -v
```
### 6.Bundlerをインストール
`Bundler`はRubyの依存関係を管理するツールです。  
Bundler2.3.14をインストールします。
```
gem install bundler -v '2.3.14'
```
インストールしたBundlerのバージョンを確認します。
```
gem list bundler
```
### 7.Railsをインストール
`Rails`とはRuby on Railsの略称でオープンソースのWebアプリケーションフレームワークです。  
Rails7.1.3.2をインストールします。
```
gem install rails -v 7.1.3.2
```
インストールしたRailsのバージョンを確認します。
```
rails -v
```
### 7.NVMをインストール
`NVM`とはNodeVersionManagerの略で複数のNodeのバージョンを簡単に管理することができます。 
NVMをインストールします。
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```
.bashrcファイルを再読み込みさせNVMのインストールを反映させます
```
source ~/.bashrc
```
インストールしたNVMのバージョンを確認します。
```
nvm -v
```
### 8.Nodeをインストール
'Node'とはJavaScriptを実行できるツールです。  
Node v17.9.1 をインストールします。
```
nvm install v17.9.1
```
インストールしたNodeのバージョンを確認します。
```
node -v
```
### 9.Yarnをインストール
'Yarn'とはJavaScriptのパッケージマネージャーで依存関係を適切に管理してくれます。  
npm(NodePackageManager)を使用してYarn1.22.19をインストールします。
```
npm install -global yarn@1.22.19
```
インストールしたYarnのバージョンを確認します。
```
yarn -v
```
### 10.MySQLをインストール
AmazonLinux2の場合を想定しています。  
yumを最新にアップデートします。
```
sudo yum update -y
```
デフォルトで作成されているMariaDB関連ファイルを削除します。  
この作業を行わないとMysqlにおいてうまく実行してくれないコマンドがあります。
```
sudo yum remove -y mariadb-*
```
MySQLのリポジトリをyumに追加します
```
sudo yum localinstall -y https://dev.mysql.com/get/mysql80-community-release-el7-11.noarch.rpm
```
MySQLの起動に必要なパッケージをインストール
```
sudo yum install -y mysql-community-server
sudo yum install -y --enablerepo=mysql80-community mysql-community-devel
```
ログファイルの作成
```
sudo touch /var/log/mysqld.log
```
MySQLの起動
```
sudo systemctl start mysqld
```
ディレクトリの所有権と権限を修正
```
sudo chown -R mysql:mysql /var/lib/mysql
sudo chmod -R 750 /var/lib/mysql
sudo chown mysql:mysql /var/log/mysqld.log
sudo chmod 640 /var/log/mysqld.log
```
MySQLの再起動
```
sudo systemctl restart mysqld
```
MySQLのステータス確認
```
sudo systemctl status mysqld
```
***初期パスワードの設定***  
初期パスワードを確認します。
```
sudo systemctl status mysqld
```
実行すると下記ログが表示されますのでパスワードをコピーします。
```
/// 表示されるログです
A temporary password is generated for root@localhost: [パスワード]
```
MySQLにログイン
```
mysql -u root -p
//[パスワード]を入力しEnter
```
***エラーの解消***  
MySQLにログインしようとしても下記のようなエラーが発生しました
```
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (13)
```
- エラーの内容  
MySQLクライアントが指定されたソケットファイル（/var/lib/mysql/mysql.sock）を介してMySQLサーバーに接続できなかったことを示しています。
- エラーの解消方法
ソケットファイルの作成
```
sudo touch /tmp/mysql.sock
```
を実行し、ソケットファイルを作成することでエラー解消できました。
