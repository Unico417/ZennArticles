---
title: "GCEのUbuntu22.04でSSHができない"
emoji: "⌨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["linux", "ubuntu", "googlecloudplatform"]
published: true
---

Google Cloud Platform(GCP)の無料枠を試してみた時にハマったので、書き残し。

# どうすればいいの

インターネット上に転がっている先人の書き残しから、少し手を加えます。  
ちなみに、環境は**Windows**。

1. 秘密鍵を生成する際に、`rsa` ではなく `ed25519` にする。
    ```powershell
    ssh-keygen -t ed25519 -C "user"
    ```
    次の2つが生成される。
    - id_ed25519
    - id_ed25519.pub

2. GCE の vm の秘密鍵に`id_ed25519.pub` の中身を設定する。

3. ssh接続するときには、`id_ed25519` を使う。
    ```powershell
    ssh user@12.123.12.123 -i C:\id_ed25519
    ```
    
4. できた。

# どうして

脆弱性の観点から、`SHA-1`の秘密鍵を拒否るようになったとかなんとか...？
詳しくは、出典2を見てください。

# 出典
1. https://stackoverflow.com/a/74273581/11823331
2. https://askubuntu.com/questions/1409105/ubuntu-22-04-ssh-the-rsa-key-isnt-working-since-upgrading-from-20-04/1409528#1409528
