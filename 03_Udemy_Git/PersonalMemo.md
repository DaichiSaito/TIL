# Git+Githubのノート

### ○ ローカルでの作業からリモートレポジトリへのプッシュまで

1. git add . (全てのファイルの場合)
2. git commit
    - git commit . (新規ファイルの作成がない場合、git add . が省略できる)
    - 「-m "メッセージ"」でメッセージを追加できる
    - 「-v」でエディターにてメッセージを追加できる
3. git status, git log, git diff などで状況を確認
    - git log --oneline
    - git log -n で表示する数を指定できる
    - git diff --staged にてステージとローカルレポジトリの差分を確認できる
4. git push リモート名 ブランチ名 にてリモートレポジトリにプッシュできる

### ○ ブランチを使った作業について

GitHub Flowモデルという簡単なブランチの運用モデルがある。
masterブランチ、topicブランチの2種類を用意する。

1. git clone でリモートレポジトリからコピーする
2. git checkout -b ブランチ名 でブランチを切る
3. ローカルで作業する
4. git commit まで持っていく
5. 適宜、git rebase -i HEAD~# にて、commitを整理する
    - 絶対pushした内容は、rebase i にて修正しないこと！
6. git pull --rebase origin feature にてリモートブランチの最新のpush内容を改めて取得
7. あれば、conflictを解消する
8. git push origin ブランチ名 にてリモートにプッシュする
9. Githubからプルリクを送る
10. LGTMがもらえれば、リモート上でMergeして削除する
11. ローカル上でも、Mergeしてmasterではない方のブランチを削除する

 一応、コマンドライン上で試してみたが、勘違いがありそう。。。
 実践して、色々な方法を模索すること。

### ○ .gitignore

ここで公開しないファイルを指定する。
パスワード等は特に注意する。

AWS関係のパスワード等が公開されてしまい、
悪用された結果、数百万円の請求が来てしまった事例があるらしい。。。

### ○ mergeとrebase

とりあえずmergeを主に使っていこうと思うが、
適宜使い分け方についても学んでいきたい。

### ○ tag

コミットにタグをつけて、探しやすくできる。

### ○ stash

一時避難できる。急な依頼に対応する際などに使用する。

### ○ Gitに関するリンク集
- [githubでissue管理を実践してみた](https://qiita.com/fukubaka0825/items/c7710b4e87d478c8ba3b)
- [いまさらだけどGitを基本から分かりやすくまとめてみた](https://qiita.com/gold-kou/items/7f6a3b46e2781b0dd4a0)