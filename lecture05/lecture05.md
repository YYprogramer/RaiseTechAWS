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