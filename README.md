# 機能
- リバースプロキシサーバとして、`ef_react`, `ef_express` に接続を振り分け  
- SSL を終端  

# 利用技術
- 環境構築: `Docker:18.09.2`  
- サーバ: `nginx:1.14.1`  

# 使用法
- `Docker`, `mkcert` をローカルにインストール  
- ローカルの `/etc/hosts` に、`app.expwy-footprints.com`, `api.expwy-footprints.com` を `loopback address` として登録  
- ローカルの `~/.bash_profile` に、`/etc/hosts` で定義した `loopback address` のエイリアスを作成  
- `docker network create --driver bridge expwy_footprints` で、コンテナ内のネットワークを立ち上げ  
- `ef_nginx`, `ef_react`, `ef_express` を同じ階層に `git pull`  
- `mkcert` で、上記のドメインを含む公開鍵＆秘密鍵を作成（同時にルート認証局も出来る） 
- 作成した鍵を、`/certs/expwy-footprints.com.crt`, `/certs/expwy-footprints.com.crt` に保存  
- `ef_nginx`, `ef_react`, `ef_express` の順に `docker-compose up -d`  

# 今までの取り組み
今まで、勉強してきたことをまとめておく

## Step.1:  ローカルの apache をリバースプロキシサーバ化
- `/etc/hosts` に、`app.expwy-footprints.com`, `api.expwy-footprints.com` を `loopback address` として登録  

- `~/.bash_profile` に、`/etc/hosts` で定義した `loopback address` のエイリアスを作成  
(https://qiita.com/HanaeKae/items/79d783521b83e350fa42)  

- `/etc/apache2/httpd.conf` で、`/etc/apache2/extra/httpd-vhost.conf` を読み込むように設定  

- `/etc/apache2/extra/httpd-vhost.conf` で、各ドメインの `VirutalHost` を設定
(https://httpd.apache.org/docs/2.2/ja/vhosts/examples.html) 

## Step.2:  Step.1 のサーバで、SSL 終端
- `mkcert` で、各ドメインを含む公開鍵＆秘密鍵を作成（同時にルート認証局も出来る）  
(https://qiita.com/rkunihiro/items/530b5dc685bd3bff2082)  

- `/etc/apache2/extra/httpd-vhost.conf` で、各ドメインでの SSL 接続を有効化  
(http://cmshikaku.com/feature/?p=1429)  

## Step.3:  nginx コンテナをリバースプロキシサーバ化し、SSL 終端
- 公式ドキュメントと、`docker-compose.yml` のコメントを参照  
(https://hub.docker.com/r/jwilder/nginx-proxy/)   

## Misc:  apache コンテナをリバースプロキシサーバ化し、SSL 終端（断念）
最初は、ローカルでも使用した `apache` でサーバ構築を試みたが、  
設定が上手くいかず、上述の `nginx-proxy` イメージを使用することに。  
しかし、その過程で色々と設定をしたので、備忘録として書き残しておく。

- `Docker for Mac` の制約  
(https://docs.docker.com/docker-for-mac/networking/)  

- `Dockerfile` のベストプラクティス（パッケージのインストールは `Dockerfile` の `RUN` でないと不可能っぽい？？  
(http://docs.docker.jp/engine/articles/dockerfile_best-practice.html)  

- `docker-compose` で複数行のコマンドを記述する  
(https://github.com/docker/compose/issues/2033)  

- コンテナからファイルをコピーする  
(https://qiita.com/gologo13/items/7e4e404af80377b48fd5)  

- コンテナに固定 IP を付与する  
(https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)  
(https://qiita.com/takara@github/items/2349fff473474d7fcf47)  

- コンテナの DNS を編集する  
(https://qiita.com/zembutsu/items/9e9d80e05e36e882caaa#extra_hosts)  

- `Could not reliably determine the server's fqdn` の警告を消去する  
(https://qiita.com/pugiemonn/items/41d4bebf48437eed3f4c)  

- SSL の仕組み  
(https://www.infraeye.com/2017/10/01/network028/)  
(https://cspssl.jp/guide/mechanism.php)  

- SSL を有効化する (`apache`)  
(https://docs.docker.com/samples/library/httpd/#configuration)  

- SSL を有効化する (`Docker for Mac`)  
(https://docs.docker.com/docker-for-mac/#add-tls-certificates)  

- SSL 通信を確認する  
(https://laboradian.com/try-openssl-s_client-command/)  
(http://hideharaaws.hatenablog.com/entry/2018/06/18/110053)  

- リバースプロキシを設定する  
(https://qiita.com/okiyuki99/items/83c1fb07644cd232d91e)  
(https://blog.neet-shikakugets.com/construct-reverse-proxy-using-apache)  
