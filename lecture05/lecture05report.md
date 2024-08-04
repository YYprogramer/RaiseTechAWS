# 実装の報告
## 組み込みサーバーで起動
![check_server01.png](img/check_server01.png)
画像表示確認
![check_server02.png](img/check_server02.png)

## PumaサーバーとUnixSocketを使用して動作確認
curlコマンド
![socket_curl_log.png](img/socket_curl_log.png)
Serverログ
![socket_saver_log.png](img/socket_saver_log.png)

## Nginx単体での接続確認
![nginx_connect.png](img/nginx_connect.png)

## Nginx及びPumaを使用して接続確認
![check_nginx&puma.png](img/check_nginx%26puma.png)
新規保存も問題なくできるか確認済み。

## ELB(ALB)を導入して接続確認
![check_ALB.png](img%2Fcheck_ALB.png)
![check_ELB.png](img%2Fcheck_ELB.png)
![ALB-connect-test.png](img/ALB-connect-test.png)

## S3を導入
アプリケーションで新しいフルーツを登録
![S3_entry_newfruit.png](img%2FS3_entry_newfruit.png)
アプリケーションに登録されているか確認
![S3_entry_check.png](img%2FS3_entry_check.png)
S3のオブジェクトURLにアクセス
![S3_entry_url.png](img%2FS3_entry_url.png)

## 構成図
![Lecture05.drawio.png](img/Lecture05.drawio.png)

## 感想
- アプリケーションをデプロイまで持って行けてよかった
- UnixSocket,Nginx,Pumaそれぞれの役割が手を動かしながらだとだいたい身につきました
- 課題完了まで長かったです
- セキュリティについては１箇所に設定しておけばOKなのではなく、使用するサービスごとにアクセス制限をかけることでセキュリティーを頑丈にするということがわかりました。