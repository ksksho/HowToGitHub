# branchの使い分け
- dashing: コンテスト終了時など時間に余裕があるときに最新版に更新。普段は触らない。
   - dashing_develop: event/developで動作確認した最強版。merge等をミスしたときの保険。
      - event/develop: 動作確認済み最強版。
         - event/zytlebot/develop: 大会直前の動作確認済み最強版。
            - event/zytlebot/tuning: 大会直前の作業ブランチ。
         - event/開発者A/develop: 動作確認済みブランチ。
            - event/開発者A/機能a: 作業ブランチ。
            - event/開発者A/機能b
         - event/開発者B/develop
            - event/開発者B/機能c
            - event/開発者B/機能d
- foxy

# 開発フロー
## 作業ブランチ(event/開発者A/機能a)の作成
```
$ git clone -b event/develop https://github.com/tlk-emb/ZytleBot3_ROS2_SW.git
$ git branch event/開発者A/develop
$ git checkout event/開発者A/develop
$ git branch event/開発者A/機能a
$ git checkout event/開発者A/機能a
```

## event/開発者A/developブランチへのmerge手順
1. event/開発者A/developにmerge
```
$ git checkout event/開発者A/develop
$ git merge event/develop
$ git push origin event/開発者A/develop
```
2. ZytleBotで動作確認
```
$ git branch event/開発者A/develop origin/event/開発者A/develop
$ git checkout event/開発者A/develop
// 動作確認
```

## event/developへのmerge手順
1. merge作業することを開発チームに周知する。
2. 大会直前のevent/zytlebot/*への変更など優先順位が高い変更がある場合は、先にそちらをmergeする。
3. event/developへの変更を反映
```
$ git checkout event/develop
$ git pull origin event/develop
$ git checkout event/開発者A/develop
$ git merge event/develop
```
4. ZytleBotで動作確認
```
$ git branch event/開発者A/develop origin/event/開発者A/develop
$ git checkout event/開発者A/develop
// 動作確認
```
5. event/developにmergeする
```
$ git checkout event/develop
$ git merge event/開発者A/develop
```
6. merge作業の完了を通知
7. event/zytlebot/develop等に変更を反映
```
$ git checkout event/zytlebot/develop
$ git merge event/develoop
$ git checkout event/zytlebot/tuning
$ git merge event/develoop
```

## pushできないときの対処
### 通常時
1. バックアップのために手元の状態をcommitしておく。commitしたくない変更がある場合は、stashするなり新しくbranchを作成するなりしておく。
2. ブラウザでGitHubにアクセスしてコミットログを確認し、リモートで行われている変更が間違いではないか開発チームに確認する。
3. pull(fetch+merge)してコンフリクトに対処する。ここで失敗しても1.でcommitした地点に戻ればOK。
4. pushする。

### 緊急時
1. 開発チームに確認をとる。
2. リモートブランチのバックアップ用ブランチを作成
```
$ git branch remote_backup_branch origin/branch
$ git checkout backup/branch/日付
$ git push backup/branch/日付
$ git checkout branch
```
3. 手元のブランチを強制pushする
```
$ git push -f origin branch
```
4. バックアップブランチの変更をmergeする。
```
$ git merge backup/branch/日付
```

## コンフリクトへの対処
pullやmergeを行うとコンフリクトする場合がある。その場合はコンフリクトしたファイルの該当箇所がgitによって以下のように変更されているので、手動で修正する。
```
<<<<<<<<<< HEAD
aaaaa
==========
bbbbb
>>>>>>>>>> develop
```
