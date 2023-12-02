---
title: "gitだけでできる リポジトリの共有環境"
emoji: "⚒️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git"]
published: true
---

バージョン管理のgitは複数人数で共有する場合、管理するシステムが必要だと思っていましたが、そうではないようです。
なので、今回はリモートリポジトリとローカルリポジトリの作成を行ってみます。

まずは、リモートリポジトリを作成するディレクトリを作ります。

**ファイルサーバー側**

```powershell
mkdir repos
cd repos
```

## 共有用のリポジトリを作る

**ファイルサーバー側**

共有用のベアリポジトリを作成します。
個々のコミットのプッシュ先にするリポジトリです。

```powershell
git init --bare --share=true <RepoName>
```

**ローカル側**

開発者用のローカルリポジトリを作成します。
先ほどのリポジトリのディレクトリを `git clone` します。

```powershell
git clone \\server\path\to\repos\<RepoName>
```

## 最初のコミットを行う

コミット数が0だと ちょっと使いづらいので、前もって1コミットだけしておきます。

**ローカル側**

```powershell
# リポジトリのディレクトリに入る
cd <RepoName>
# ユーザ名を設定する（ユーザごとに一度だけ）
git config --global user.name "<Name>"
# ブランチを新しく作る
git checkout -b <BranchName>
# ダミーファイルを作る
'' > dummy
# 変更のあった全ファイルをステージに上げる
git add .
# ステージの全ファイルをコミット
git commit -m "Initial release message."
# プッシュ
git push --set-upstream <BranchName> origin
```

1. `git checkout -b <BranchName>` 新しいブランチを作成し、チェックアウトします。

2. `git add .` で、変更のあったファイルを全てコミット候補（ステージ）にします。

3. `git commit` で、コミットします。
    `-m "<Comment>"` で、コメントを付ける事もできます。
    まだファイルサーバーのリポジトリには、適用されません。

4. `git push` で、プッシュします。
    新しく作ったブランチは、`--set-upstream <BranchName> origin` で、ファイルサーバーにもブランチを公開してあげる必要があります。
    この時点で、ファイルサーバー側のリポジトリに適用されます。

## ２人目以降の人は

二人目以降の人は、新しいブランチやコミットを作る必要がありません。
なので、クローンしてブランチを選択するだけで大丈夫です。

```powershell
# リポジトリをクローン
git clone \\server\path\to\repos\<RepoName>
# リポジトリの中に入る
cd <RepoName>
# ユーザ名を設定する（ユーザごとに一度だけ）
git config --global user.name "<Name>"
# ブランチを選択
git checkout <BranchName>
```

# おわりに

これで、ファイルサーバーを使ったgitの共有環境ができました。

