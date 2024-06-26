---
title: Git Hooksのpre-commitが動かない
tags:
  - Git
  - githooks
private: false
updated_at: '2024-06-26T23:14:16+09:00'
id: e802d36bec64d6fd2f9f
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
Git Hooksのpre-commitの設定時、うまく動かなかった時の対処方法をまとめました。

# pre-commitとは
「pre-commit」はGit Hooksの一種で、コミットが実際に行われる前に自動的に実行されるスクリプトです。
このhookを使用すれば、コミット時に、コードのチェックを行ったり、特定のファイルをコミット対象から外したりなど、自動で検証を行うことができます。

# pre-commitが動かない時の確認事項
### pre-commitファイルの実行権限
pre-commitに実行権限が付与されていないと動かないので、以下のコマンドで実行権限を付与します
```
chmod +x pre-commit
```

### configファイルの確認
.git/config　の中身を確認して、hooksPathで設定しているパスにpre-commitがあるか確認
```
[core]
	hooksPath = .git/hooks/
```

### コミットプロセスの確認
--no-verify オプションを使用すると、設定しているhookをスキップしてコミットを行います。
このオプションが設定されていないか確認してください。
```
git commit -m "message" --no-verify
```

# 最後に
以上の確認を行って、正しくpre-commitが起動したら幸いです。
