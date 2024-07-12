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