---
title: "GitBucketのURLを変更する"
emoji: "♻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git", "gitbucket", "nginx", "ubuntu"]
published: true
---

GitBucketのURLを変更したくて試行錯誤したので、その備忘録。
備忘録なので、丁寧に整備はしていません。

環境は Ubuntu22.04 LTSだった気がする。

通常、`http://localhost:8080`に立ってしまうGitBucketを、`http://localhost/gitbucket`に変更します。
URLに`:8080`ってつくのが嫌な、ポート番号アレルギーの人はためしてみてください。

# セットアップ

## 1. nginxの準備

ポートフォワーディングのために、nginxを利用します。

### 1-1. nginxをインストール

```bash
sudo apt update
sudo apt install nginx
```

下記のコマンドでは、少し前のnginxがインストールされるようです。
今回したいことの機能としては問題ないので、このまま使います。

### 1-2. nginxの設定ファイルを編集

`/etc/nginx/sites-enabled/default` の中身を書き換えます。

```conf diff
# Default server configuration

server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;

    # Add index.php to the list if you are using PHP
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }
    
+    # - - - - - GitBucket - - - - - #
+    location /gitbucket/ {
+       proxy_set_header Host $http_host;
+       proxy_pass http://localhost:8080;
+   }
}
```

### 1-3. nginxを再起動

設定を適用するために、nginxを再起動します。

```bash
sudo systemctl restart nginx
```

## 2. gitbucketの準備

### 2-1. javaをインストール

javaの最新版をインストールします。

```bash
sudo apt install default-jdk
```

### 2-2. gitbucketをインストール

gitbucketをGithubから取得します。

```
mkdir ~/gitbucket
cd ~/gitbucket
wget https://github.com/gitbucket/gitbucket/releases/download/4.40.0/gitbucket.war
```

編集時点で最新だった`4.40.0`を使います。
最新バージョンとURLは、Githubを確認してください。

**最新版:**
https://github.com/gitbucket/gitbucket/releases/latest/


### 2-3. gitbucketを起動

```
java -jar ~/gitbucket/gitbucket.war --prefix=/gitbucket
```

### 2-4. gitbucketのBaseURLを起動

`/home/<user>/.gitbucket/gitbucket.conf` を書き換えます。

```conf diff
- base_url=http://localhost
+ base_url=http://localhost/gitbucket
```

起動すると、`http://localhost/gitbucket` でアクセスできます。

# gitbucketのポートも変えたいんですけど！

ポートも変えたいんですか。
そうですか、私もそう思っていました。

GitBucketのポートを`11111`にしてみます。

### 3-1. gitbucketのポートを変更

`--port`オプションで、ポートを変更できます。

```
java -jar ~/gitbucket/gitbucket.war --port=11111 --prefix=/gitbucket
```

### 3-2. nginxの設定を変更

gitbucketのポートに合わせて変更しましょう。

```conf diff
    # - - - - - GitBucket - - - - - #
    location /gitbucket/ {
       proxy_set_header Host $http_host;
-      proxy_pass http://localhost:8080;
+      proxy_pass http://localhost:11111;
   }
```

できましたね。
