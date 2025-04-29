### TL;DR  
- **「main で直接編集・プッシュさせない」こと自体は *Azure DevOps Server のブランチ保護* で実現できます。**  
- **「main を最初にチェックアウトさせない」には *デフォルトブランチを develop に変更* します。**  
- **ローカル編集・コミット段階で強制的に止めるなら *Git Hooks (pre-commit／pre-push)* をチーム標準テンプレートとして配布** する方法があります。  
Visual Studio 単体には “main だから編集禁止” といったポップアップは現在ありませんが、上記 3 段構えで **実質的に main 直編集を防ぐ** 運用が可能です。

---

## 1. まず「develop を既定ブランチ」にしておく (最も効果が大きい)

> 新しいクローンを作ると Git は **既定ブランチ（HEAD）を最初にチェックアウト** します  ([既定のブランチの変更 - Azure Repos | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/devops/repos/git/change-default-branch?view=azure-devops))

1. **Azure DevOps Web** → **Repos ▸ Branches**  
2. `develop` の「…」メニュー → **Set as default (既定のブランチとして設定)**  
   これだけで **VS2022 でクローンした瞬間に develop が開く** ので、main を触る機会がぐっと減ります。

---

## 2. main ブランチを **ブランチポリシー＋ブランチセキュリティ** で守る

| 目的 | 設定場所 | 推奨値 |
| --- | --- | --- |
| **直接プッシュ禁止** | Branches ▸ main ▸ 「…」▸ *Branch security* | Developers グループの **Contribute = Deny** |
| **必ず Pull Request 経由** | Branches ▸ main ▸ *Branch policies* | “*Require a minimum number of reviewers*” を 1 以上、有効化 |
| **CI パイプライン合格必須** | 同上 | “*Build validation*” で対象パイプラインを選択 |
| **強制 push・履歴書き換え禁止** | Branch security | “*Force Push* = Deny” |

ポリシーが設定されたブランチは **「PR 必須」アイコンが付き、直接プッシュはサーバー側で拒否** されます  ([Git ブランチ ポリシーと設定 - Azure Repos | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/devops/repos/git/branch-policies?view=azure-devops), [Git ブランチのセキュリティとアクセス許可を設定する - Azure ...](https://learn.microsoft.com/ja-jp/azure/devops/repos/git/branch-permissions?view=azure-devops&utm_source=chatgpt.com))。  
ローカルではコミットできても **push 時に 403 エラーが出る** ため誤操作にすぐ気づけます。

---

## 3. VS2022 での “うっかり main 編集” を物理的に止める 2 つの手

### 3-1. **Git Hook (pre-commit / pre-push) をテンプレート配布**

> Git はローカルフックを実行するため **VS2022 経由のコミット／プッシュでもフックが発火** します。

1. リポジトリ直下に **`/git-hooks/prevent-main.sh`** を作成:

   ```bash
   #!/usr/bin/env bash
   branch=$(git symbolic-ref --short HEAD)
   if [ "$branch" = "main" ]; then
     echo "✖ main ブランチへの直接コミットは禁止です。develop に切り替えてください。" 1>&2
     exit 1
   fi
   ```

2. チームの PC で一度だけ

   ```powershell
   git config --global init.templateDir C:\git-template
   ```

   とし、`C:\git-template\.git\hooks` に上記フックを配置 → **`git clone` 時に自動コピー** される仕組みにしておく。

この方法は **Push 以前の “コミット” 自体をブロック** するので最強です  ([【GitHooks】mainブランチへのコミットを禁止する。【pre ...](https://qiita.com/MasaoSasaki/items/431a7f103780a01757b5?utm_source=chatgpt.com))。  
（Windows 環境なら `.cmd` 版を用意すると Git CMD でも動きます）

### 3-2. **Visual Studio 拡張機能**  
VS 拡張マーケットプレイスには「Commit Guard」や「Master Branch Warning」等、  
特定ブランチへのコミット・プッシュを検出して警告する拡張があります。  
組織ポリシー次第ですが、**全社 PC に共通拡張を配布できる場合**は手軽です。  
（拡張はサードパーティ製のため導入可否は要チェック）

---

## 4. 運用 Tips（トラブルシューティングを含む）

| シチュエーション | 挙動 / メッセージ | 対処 |
| --- | --- | --- |
| **main で編集→コミット→Push したら拒否された** | *”Permission denied – push not allowed on protected branch”* | `git switch develop` して修正を `cherry-pick` または `rebase` |
| **Hook をスキップしたい緊急時** | `git commit --no-verify` | 原則禁止。管理者のみ許可で十分 |
| **既存クローンが main のまま** | VS 下部ステータスバーでブランチ確認 | “main で作業中です。develop に切り替えてください” と周知徹底 |
| **他ツール(CLI)経由 push** | サーバーポリシーが拒否 | CLI でも同じエラーになるので安心 |

---

## まとめ

1. **既定ブランチを develop** にして “最初から main に入らない” 状態を作る。  
2. **Azure DevOps の Branch Policy / Security** で main への直接プッシュを禁止。  
3. **Git Hook または VS 拡張** でローカル段階でも「main 直コミット」をブロック。  

この 3 層ガードで **「main 開発しちゃった問題」をほぼゼロに**できます。運用開始前に設定を済ませ、チームメンバーには **「ブランチ名を必ず見るクセをつけよう」** と合わせて周知してください。