承知しました！これから、Visual Studio 2022を使ったAzure DevOps Server上でのGit運用に関する、初心者向けマニュアルをMarkdown形式で作成します。

- TortusSVNとの比較を交えて「フェッチ」「プル」「プッシュ」をわかりやすく解説
- 基本ブランチ運用（main, develop, bugfix）ルールの説明
- 手元にクローンがある場合・ない場合のケーススタディ
- バグ修正、リリース作業時などの追加ケーススタディも盛り込みます

しばらくお待ちください！

# Git初心者向け運用マニュアル

本マニュアルは、**TortoiseSVNの日常操作に慣れた初心者向け**に、Gitの基本概念とVisual Studio 2022 (Windows 11) 上でのGit運用手順をまとめたものです。Azure DevOps Server 2020上のGitリポジトリを対象に、**Gitコマンドを使わずGUI操作**で行う方法を解説します。SVNとの違いや、初心者が不安になりやすいポイントについても補足しています。

## 1. GitとSVNの基本概念の比較

- **集中型 vs 分散型:** SVN（Apache Subversion）は中央サーバー上のリポジトリでバージョン管理を行う**集中型**システムです ([Apache Subversion - Wikipedia](https://ja.wikipedia.org/wiki/Apache_Subversion#:~:text=Apache%20Subversion%EF%BC%88%E3%82%A2%E3%83%91%E3%83%83%E3%83%81%E3%83%BB%E3%82%B5%E3%83%96%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%3B%20SVN%EF%BC%89%E3%81%AF%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0%E3%81%AE%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AA%E3%81%A9%E3%82%92%E7%AE%A1%E7%90%86%E3%81%99%E3%82%8B%E9%9B%86%E4%B8%AD%E5%9E%8B%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E7%AE%A1%E7%90%86%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%81%AE%E4%B8%80%E3%81%A4%E3%80%82%20%E5%85%83%E3%80%85%E3%81%AF%E3%80%81CollabNet%E3%81%8C%E9%96%8B%E7%99%BA%E3%81%97%E3%81%A6%E3%81%84%E3%81%9F%E3%81%8C%E3%80%812009%E5%B9%B411%E6%9C%887%E6%97%A5%E3%81%ABApache%20Incubator%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%AE%E3%81%B2%E3%81%A8%E3%81%A4%E3%81%A8%E3%81%AA%E3%82%8A%E3%80%81,2010%E5%B9%B42%E6%9C%8817%E6%97%A5%E3%82%88%E3%82%8AApache%E3%81%AE%E3%83%88%E3%83%83%E3%83%97%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%A8%E3%81%AA%E3%81%A3%E3%81%9F%E3%80%82%E3%83%A9%E3%82%A4%E3%82%BB%E3%83%B3%E3%82%B9%E3%81%AFApache%20License%E3%81%AB%E6%BA%96%E3%81%98%E3%81%9F%E3%82%82%E3%81%AE%E3%81%A8%E3%81%AA%E3%81%A3%E3%81%A6%E3%81%84%E3%82%8B%E3%80%82))。これに対し、Gitは各開発者がリポジトリの完全なコピーを手元に保持する**分散型**バージョン管理システムです ([Git - Wikipedia](https://ja.wikipedia.org/wiki/Git#:~:text=Git%E3%81%AF%E5%88%86%E6%95%A3%E5%9E%8B%E3%81%AE%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%89%E7%AE%A1%E7%90%86%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%81%A7%E3%81%82%E3%82%8B%E3%81%9F%E3%82%81%E3%80%81%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%88%E3%82%B5%E3%83%BC%E3%83%90%E7%AD%89%E3%81%AB%E3%81%82%E3%82%8B%E4%B8%AD%E5%BF%83%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%E3%81%AE%E5%AE%8C%E5%85%A8%E3%81%AA%E3%82%B3%E3%83%94%E3%83%BC%E3%82%92%E6%89%8B%E5%85%83%EF%BC%88%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E7%92%B0%E5%A2%83%EF%BC%89%E3%81%AB%E4%BD%9C%E6%88%90%E3%81%97%E3%81%A6%E3%80%81%E3%81%9D%E3%81%AE%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%E3%82%92%20%E4%BD%BF%E3%81%A3%E3%81%A6%E4%BD%9C%E6%A5%AD%E3%82%92%E8%A1%8C%E3%81%86%E3%80%82))。つまり**SVN**では常にサーバーと通信して更新・コミットしますが、**Git**ではオフラインでも全履歴を持ったローカルリポジトリで作業できます。

- **リポジトリの取得:** SVNではプロジェクトを利用開始する際に「**チェックアウト**」(Checkout)して作業コピーを取得します。一方、Gitでは「**クローン**」(Clone)を行い、**リモートリポジトリの全履歴を含むコピー**をローカルに作成します。クローン後は、ローカルリポジトリ内で自由に履歴を閲覧したり過去に戻したりできます（SVNのチェックアウトでは最新版+αしか取得しない点が異なります）。

- **コミットの扱い:** SVNでは「**コミット**」すると即座に中央リポジトリに変更が反映され、リビジョン番号が振られます。Gitでは**コミット**はローカルリポジトリへの履歴記録であり、他のメンバーと変更を共有するには後述の「プッシュ」を行う必要があります。SVNのコミット = Gitのローカルコミット+プッシュと考えると分かりやすいです。ローカルでコミットを積み重ね、まとまったところでまとめてプッシュできるため、**細かなコミットを気軽に行える**点がGitの利点です。逆にプッシュしない限り他人には影響しないので、**ローカルで安心して試行錯誤ができます**。

- **ブランチとマージ:** SVNではブランチを作成する際、リポジトリ内にディレクトリをコピーして作るのが一般的です（例: `/branches/新ブランチ名` を作成）。このため**ブランチの作成・統合(SVNのマージ)**には手間がかかりました。Gitではブランチはリポジトリ内の**ポインタ**のようなものです。ローカルで瞬時に作成・切り替えができ、ブランチ間の**マージ**（統合）も高速かつシンプルです。例えば、実験用にブランチを切って作業し、問題なければメインのブランチにマージするといった運用が**日常的**に行われます。SVNより**ブランチ運用が軽量**なので、積極的に活用できる点がGitの特徴です。

> **ポイント:** GitとSVNの最大の違いは「**中央サーバと常に同期するか**」という部分です。SVNでは更新(Update)しなければ他人の変更を取り込めず、コミットしなければ共有できませんでした。Gitではローカルで自由にコミットでき、後からプッシュで共有・プルで他人の変更を取得します。この違いにより、Gitでは**手元での履歴管理**や**並行作業**が柔軟になります。

## 2. 「フェッチ」「プル」「プッシュ」の意味と役割

Gitで他者との変更を同期する際に登場する主な操作が「**フェッチ** (Fetch)」「**プル** (Pull)」「**プッシュ** (Push)」です。SVNで言う「更新(Update)」や「コミット(Commit)」に相当する処理ですが、Gitでは役割が分かれているため、それぞれの意味を理解しましょう。

- **フェッチ (Fetch):** リモートリポジトリ上の、自分のローカルにまだ取り込まれていないコミットを**ダウンロード**します。ただし取得した変更はローカルの作業ブランチには反映されません（履歴として受信するだけで適用はしない） ([Get started with Git and Visual Studio - Azure Repos | Microsoft Learn](https://docs.microsoft.com/en-us/azure/devops/repos/git/gitquickstart?view=azure-devops-2020&tabs=visual-studio#:~:text=,commits%20into%20your%20local%20branch))。言い換えると「**サーバー上の最新履歴を手元に持ってくる**」操作です。フェッチ後、手元のGitツールで差分や履歴を確認し、必要に応じて後述のマージ操作を行えます。  
  ※SVNにはフェッチに相当する直接の操作はありません。SVNの更新は後述のプルに近く、フェッチは「更新情報の取得だけ行い適用はしない」というGit特有の操作です。

- **プル (Pull):** リモートリポジトリから最新のコミットを取得し、**現在チェックアウト中のローカルブランチにマージ**（統合）します ([Get started with Git and Visual Studio - Azure Repos | Microsoft Learn](https://docs.microsoft.com/en-us/azure/devops/repos/git/gitquickstart?view=azure-devops-2020&tabs=visual-studio#:~:text=,commits%20into%20your%20local%20branch))。一般的には「フェッチ＋自動マージ」をまとめて行う操作と考えてください。SVNの「更新(Update)」に近い動作ですが、**ローカルの履歴にマージコミットが追加される**点が異なります（SVNではマージの概念が薄いですが、Gitのプルでは内部的にマージ処理が発生します）。プル実行後は、リモートと同じ最新コードが手元のブランチに反映されます。  

  > **注意:** プル時に、ローカルの変更とリモートの変更が**同じ箇所を編集していた場合**コンフリクト（衝突）が発生します。この場合Visual Studio上でマージエディタ（競合解決画面）が表示され、自分の変更とリモートの変更のどちらを採用するかを選ぶ必要があります。これはSVNの更新時にコンフリクトが起きる場合と同様です。落ち着いて変更点を比較し、必要な方を選択してマージしましょう。コンフリクトを解消してコミットすればプル処理が完了します。

- **プッシュ (Push):** ローカルのコミット履歴をリモートリポジトリに**送信**し、リモート（Azure DevOps上のGitリポジトリ）を更新します ([Get started with Git and Visual Studio - Azure Repos | Microsoft Learn](https://docs.microsoft.com/en-us/azure/devops/repos/git/gitquickstart?view=azure-devops-2020&tabs=visual-studio#:~:text=,a%20Pull%20then%20a%20Push))。SVNの「コミット」に相当する操作で、プッシュにより他の開発者がプルしてあなたのコミットを取り込めるようになります。**注意点として、プッシュはリモートの最新状態を自分が反映していないと実行できません。**自分のローカルが古いままプッシュしようとするとエラーになり、その場合は一度プルして最新状態を取り込んでから再度プッシュします（他人の変更と競合した場合に上書きしてしまうのを防ぐためです）。

> **ポイント:** Gitで他者と変更を共有する基本サイクルは、「**プル（他人の変更を取得）→ 作業・コミット（自分の変更を記録）→ プッシュ（自分の変更を共有）**」の繰り返しです ([Git - Wikipedia](https://ja.wikipedia.org/wiki/Git#:~:text=1,%E6%9B%B4%E6%96%B0%E3%81%95%E3%82%8C%E3%81%9F%E4%B8%AD%E5%BF%83%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%EF%BC%88%E4%BB%96%E8%80%85%E3%81%AE%E4%BD%9C%E6%A5%AD%E5%86%85%E5%AE%B9%E3%82%82%E7%B5%B1%E5%90%88%E3%81%95%E3%82%8C%E3%81%A6%E3%81%84%E3%82%8B%EF%BC%89%E3%82%92%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%AE%E8%A4%87%E8%A3%BD%E3%81%AB%E3%82%82%E5%8F%8D%E6%98%A0%E3%81%99%E3%82%8B%20%28git%20pull%29%E3%80%82%E3%81%93%E3%82%8C%E3%81%AB%E3%82%88%E3%82%8A%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E7%92%B0%E5%A2%83%E3%81%AE%E3%82%B3%E3%83%BC%E3%83%89%E3%82%82%E6%9C%80%E6%96%B0%E3%81%AE%E5%86%85%E5%AE%B9%E3%81%AB%E3%81%AA%E3%82%8B%E3%81%AE%E3%81%A7%E3%80%81%E6%94%B9%E3%82%81%E3%81%A6%E3%82%B9%E3%83%86%E3%83%83%E3%83%972%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%82%92%E8%A1%8C%E3%81%86%E3%80%82))。SVNで言う「Update → 編集 → Commit」に対し、Gitではコミットと共有が分離している点が大きな違いです。このため、作業中はこまめにローカルコミットし、適切なタイミングでプルやプッシュを行う運用になります。最初は手順が増えるように感じますが、**ローカルで履歴を保持できる安心感**と**柔軟な同期タイミング**が得られるため、慣れると効率的に感じられるでしょう。

## 3. 基本のブランチ運用ルール（main / develop / bugfix）

Gitでは複数のブランチを活用することで、並行作業やリリース準備を円滑に行えます。ここでは本プロジェクトで採用する**基本的なブランチ運用ルール**を説明します。ブランチは大きく「main」「develop」「bugfix」の3種類を使用します。

- **mainブランチ:** 製品リリース用の**メインブランチ**です。SVNでいう「trunk」に近い立ち位置ですが、本運用では常にリリース可能な安定コードのみを含みます。リリース版のソースコードを管理し、基本的にこのブランチ上で直接開発作業は行いません。変更は後述するdevelopブランチからのマージや、緊急のバグ修正(bugfixブランチ経由)のみとし、常にデプロイ可能な状態を保ちます。

- **developブランチ:** 開発作業の**デフォルトブランチ**です。新機能の追加や改良、リファクタリングなど日常的な開発作業はこのdevelopブランチ上で行います。開発中の最新コードが集約されるブランチで、十分にテストされたらmainブランチにマージしてリリースします（後述）。SVNには明確な開発用メインラインの概念はありませんでしたが、Gitではこのように開発用ブランチを設けて、リリースブランチ(main)と分けて管理する運用が一般的です。

- **bugfixブランチ:** 本番リリース中のバージョンに対する**緊急のバグ修正**に使用する一時的なブランチです。運用上、mainブランチでリリースした後に重大な不具合が見つかった場合など、mainから派生させてbugfixブランチを作成し、修正を行います。修正が完了したらmainブランチにマージし、その修正を含む新しいリリースを行います。また、同じ修正をdevelopブランチにも適用して、次回リリースに修正内容が取り込まれるようにします。バグ修正が完了し各ブランチに反映したら、bugfixブランチは不要になるため削除します（履歴はmainおよびdevelopに残るので安心してください）。

> **補足:** 開発規模によっては、機能ごとに**フィーチャーブランチ**(feature branch)を作成して作業し、完了後にdevelopへマージするケースもあります。本プロジェクトでは基本的に小規模な機能追加は直接developブランチ上で行いますが、大きな機能実装時に作業用ブランチを切っても構いません。その場合も、最終的にはdevelopブランチへマージして統合する運用となります。

## 4. ケーススタディ：具体的な作業例

ここからは、実際の作業シナリオに沿ってGitの運用手順を見ていきます。**Visual Studio 2022のGUI**を使用した手順を中心に、スクリーンショット挿入予定箇所を示しながら説明します。

### 4.1 既存アプリの機能追加（手元にクローンがない場合）

**状況:** 新たに既存アプリに機能追加の開発を始めるにあたり、まだ手元にそのリポジトリのクローンがないケースです。まずリポジトリをクローンし、developブランチ上で開発を行います。

**手順:**

1. **リポジトリのクローン:** Visual Studio 2022を起動し、メニューバーから **[Git] -> [リポジトリのクローン]** を選択します。  
   （ここにスクリーンショットを挿入：VSの「リポジトリのクローン」ダイアログ）

2. **クローン先の指定:** 「リポジトリのクローン」ウィンドウで、クローンしたい**リポジトリのURL**を入力し、ローカルに保存するフォルダを指定します。Azure DevOps Server 2020上のプロジェクトの場合、**[Azure DevOps]** を選択して組織名/プロジェクトからリポジトリをブラウズすることも可能です。入力できたら**[クローン]** ボタンをクリックします。  
   （ここにスクリーンショットを挿入：URLを入力した状態のダイアログ）

3. **ソリューションのオープン:** クローン処理が完了すると、リポジトリがローカルに作成されます。Visual Studioで自動的にプロジェクトが開かない場合は、クローンしたフォルダから `.sln` ファイル（ソリューションファイル）を開いてください。開いたら、Visual Studio上部の**Gitブランチ表示**（現在チェックアウト中のブランチ名が表示されている部分）を確認します。おそらくデフォルトでは`main`ブランチになっているため、開発用の`develop`ブランチに切り替えます。

4. **developブランチへ切り替え:** Visual Studioの画面下部（ステータスバー）や上部のGitメニューから現在のブランチ名をクリックすると、ブランチ一覧が表示されます。その中から**develop**ブランチを選択します（もし表示されていない場合は「リモートブランチを表示/フェッチ」等の操作を行い取得します）。developブランチを選ぶと、ローカルにそのブランチがチェックアウトされます。  
   （ここにスクリーンショットを挿入：ブランチ一覧からdevelopを選択しているところ）

   - **Tip:** 初回クローン直後であれば、リモートのdevelopブランチを初めてチェックアウトする際にローカルブランチが作成されます。Visual Studioが自動で案内してくれる場合もありますが、表示されない場合は**Gitリポジトリウィンドウ**（旧Team Explorerのブランチ管理画面）で`origin/develop`を右クリックして**[チェックアウト]**することもできます。

5. **最新状態の取得（プル）:** 開発を始める前に、念のためリモートの最新状態を取得します。メニューから **[Git] -> [プル]** を実行し、現在のdevelopブランチにリモート（origin/develop）の最新コミットを取り込みます。クローン直後で他に更新が無ければ「最新です」等のメッセージが出るだけですが、他の人が直前にコミットしていた場合その変更が反映されます。  
   （ここにスクリーンショットを挿入：プル実行後の結果メッセージ or プル操作ボタン）

6. **機能追加の実装:** developブランチ上で、目的の機能を実装します。ソースコードを編集し、ファイルを追加・変更します。変更内容は**ソリューションエクスプローラー**上で「+」「~」などのアイコンが付き、Gitで追跡されていることがわかります。適宜ビルドやテストを行い、機能が正しく動作することを確認します。  
   （ここにスクリーンショットを挿入：コード編集後、変更が生じたファイル一覧（Git変更ウィンドウ））

7. **ローカルでコミット:** 機能実装が完了したら、変更を**コミット**します。Visual Studioの **Git変更** ウィンドウ（既定では画面右に表示。表示されていなければ **[Git] -> [Git の変更]** から開く）で、変更内容を確認します。問題なければ上部のテキストボックスに**コミットメッセージ**を入力します（どのような変更か簡潔に記述します）。メッセージ例: *"〇〇機能を追加しました"* など。メッセージを入力後、**[すべてコミット]** ボタン（✔ アイコン）をクリックします。これで変更がローカルリポジトリにコミットされました。  
   （ここにスクリーンショットを挿入：コミットメッセージ入力とコミット実行ボタン）

   - **補足:** Visual Studioの設定によっては「コミットとプッシュを同時に行う」オプションが有効になっている場合があります。その場合、✔ボタンの横に小さな矢印があり、「コミットしてプッシュ」のようなメニューが選べます。初心者のうちはコミットとプッシュを分けて確実に操作する方が安心なので、ここではコミット単体を行いました。

8. **リモートへプッシュ:** コミットした変更をチームと共有するため、リモートに**プッシュ**します。Git変更ウィンドウ上部にある **[同期]** アイコン（🔄）または **[プッシュ]** ボタンをクリックしてください。これにより、先ほどのコミットがAzure DevOpsのリポジトリ（origin/develop）に送信されます。プッシュが成功すると、Azure DevOps上でdevelopブランチにコミットが反映され、他のメンバーはプルすることでこの変更を取得できるようになります。  
   （ここにスクリーンショットを挿入：プッシュ実行後のステータス表示（例：「プッシュが完了しました」メッセージ））

   - **注意:** プッシュ時にエラーが出た場合、リモートに自分のローカルにないコミットが存在する可能性があります。その場合はプッシュの前に**プル**を行ってローカルを最新状態にしてから再度プッシュしてください（既に手順5でプル済みなら通常問題ありません）。

以上で、新機能の追加作業（クローンが無い状態からの開始）は完了です。あとは必要に応じてAzure DevOps上でプルリクエストを作成し、レビューや本番マージのプロセスを進めます（小規模チームではそのままdevelopに溜めておき、リリース時にまとめてmainにマージする運用でもOKです）。

### 4.2 既存アプリの機能追加（手元にクローンがある場合）

**状況:** すでにローカルにリポジトリをクローン済みで、過去に作業したことがあるプロジェクトに機能追加を行うケースです。最新コードを取り込みつつ、developブランチ上で作業します。

**手順:**

1. **最新コードの取得:** Visual Studioで該当プロジェクトのソリューションを開きます（既にクローン済みのフォルダから `.sln` をダブルクリック）。開いたら、まず**現在のブランチ**を確認します。前回作業時のブランチのままになっている可能性があります。**developブランチで作業する**ので、もし別のブランチ（例: main）になっていたらdevelopに切り替えてください。続いて、リモートから最新の変更を取り込むため **[Git] -> [プル]** を実行します。これにより現在のブランチ(develop)に他のメンバーがコミットした内容があれば反映されます（「最新です」と表示された場合、既に最新です）。  
   （ここにスクリーンショットを挿入：developブランチをプルして最新化しているところ）

2. **新機能の実装:** ローカルのdevelopブランチが最新化できたら、追加する機能の実装を行います。手順4.1のステップ6と同様にコードを編集し、ビルド・テストで動作確認します。他のメンバーの変更内容も取り込んだ状態なので、安心して開発を進められます。

3. **コミットとプッシュ:** 実装ができたら変更をコミットし、リモートにプッシュします。手順4.1のステップ7〜8と同様です。**Git変更**ウィンドウで変更を確認し、メッセージを添えてコミット、続いてプッシュを実行します。  
   （ここにスクリーンショットを挿入：コミットメッセージとプッシュ実行ボタン（既出の場合省略可））

   - **メッセージの例:** 変更内容が小さい場合でも、後から履歴を見て分かりやすいように「〇〇機能のUI調整」など具体的なコミットメッセージを付けましょう。コミットを細かく分けている場合は、それぞれ意味のある単位でメッセージを書く習慣をつけると良いです。

4. **開発継続:** 以上で機能追加の変更がリポジトリに反映されました。以降、他の作業者が同じdevelopブランチ上で別機能を開発している場合は、適宜プルして最新状態を取り込みながら作業を継続してください。複数人で同時にdevelopブランチを更新する場合、こまめなプルとプッシュが衝突を防ぐコツです。

### 4.3 バグ修正作業を行う場合（bugfixブランチの作成〜完了まで）

**状況:** 本番リリース中のバージョンに不具合が見つかり、緊急に修正を適用するケースです。現在リリースされているコードはmainブランチで管理されているため、mainから派生したbugfixブランチを作成して修正を行い、修正完了後にmainへマージします。また、その修正をdevelopブランチにも取り込みます。

**手順:**

1. **mainブランチへ切り替え:** Visual Studioで該当リポジトリを開き、**mainブランチ**をチェックアウトします。現在developで作業中の場合は一旦コミットしてからブランチを切り替えます（コミットせずブランチを変えると変更を一時退避する必要があり初心者には難しいため、作業中の変更がある場合は先にコミット推奨）。画面下部のブランチ名をクリックして**main**を選択します。  
   （ここにスクリーンショットを挿入：developからmainブランチに切り替える操作）

2. **最新リリース状態の取得:** mainブランチに切り替えたら、リモートの最新状態を取り込みます。**[Git] -> [プル]** を実行し、リモートのmainブランチ上のコミットをローカルに反映します。これにより、直近のリリース以降にmain上で行われた更新（例えば過去のバグフィックスなど）があれば取り込まれます。プル後のmainブランチがリリース中のコードのベースとなります。

3. **bugfixブランチの作成:** mainブランチ上でバグ修正用のブランチを切り出します。Visual Studioの **Git** メニューから **[新しいブランチの作成]** を選択します。ダイアログが表示されたら、ブランチ名を入力します。命名規則はチームで決めている場合に従いますが、例として「`bugfix/fix-login-error`」のように`bugfix/`プレフィックス＋内容で命名します。**ブランチの作成元**が`main`になっていることを確認し、**[ブランチを作成してチェックアウト]**（作成後そのブランチに切り替えるオプション）を有効にして作成を実行します。  
   （ここにスクリーンショットを挿入：新しいブランチ作成ダイアログにbugfixブランチ名を入力しているところ）

   - **確認:** Visual Studioのウィンドウ下部に表示されるブランチ名が、新しく作成したbugfixブランチ名に切り替わっていることを確認してください。これで以降のコミットはbugfixブランチ上に記録されます。

4. **バグの修正:** bugfixブランチ上で問題の修正を行います。該当するコードを編集し、不具合が解消されるよう変更します。修正箇所が複数ファイルにわたる場合は漏れがないよう注意します。修正後、ビルド・テストを実施し、不具合が解消されたことを確認してください。  

5. **修正内容をコミット:** 修正が完了したら、変更をローカルリポジトリにコミットします。手順4.1で説明したのと同様にGit変更ウィンドウで変更を確認し、**コミットメッセージ**を入力してコミットします。メッセージは不具合の内容と対策がわかるように「Fix: ○○のバグを修正」等とすると履歴上後で追跡しやすくなります。  
   （ここにスクリーンショットを挿入：バグ修正の変更をコミットするところ）

6. **リモートへプッシュ（必要に応じて）:** バグ修正を他のチームメンバーと共有するため、必要であればbugfixブランチを**プッシュ**します。プッシュするとAzure DevOps上にも同名のbugfixブランチが作成され、共同作業者がその修正を取り込んだりレビューしたりできるようになります。ただし、緊急修正の場合自分だけで作業を完結させることも多いので、この段階のプッシュは省略しても構いません（後でmainにマージすれば結果的に共有されます）。  
   （ここにスクリーンショットを挿入：プッシュ実行（bugfixブランチ））

7. **mainブランチへのマージ:** 修正をリリースに反映するため、bugfixブランチの変更をmainブランチにマージします。まず**mainブランチをチェックアウト**します（Gitメニューのブランチ一覧からmainを選択）。次に、Visual Studioで**マージ操作**を行います。Gitメニューから **[ブランチの管理]**（またはGitリポジトリウィンドウ）を開き、修正を行ったbugfixブランチを選択して右クリックし、**「現在のブランチにマージ」** を実行します。現在のブランチ(メイン)にbugfixブランチのコミットが取り込まれます。  
   （ここにスクリーンショットを挿入：mainブランチ上でbugfixブランチをマージしている操作画面）

   - **マージコミットの確認:** マージが成功すると、Git変更ウィンドウに自動生成された**マージコミット**のメッセージが表示されます（fast-forwardマージの場合は表示されないこともあります）。必要に応じてメッセージを編集し、**コミット**ボタンを押してマージコミットを確定します。コンフリクトが発生した場合は、VS上で解決してからコミットします。

8. **mainブランチのプッシュ（リリース適用）:** mainブランチに修正が取り込まれたら、その状態をリモートにプッシュします。**[Git] -> [プッシュ]** を実行し、Azure DevOps上のmainブランチを更新します。これで本番リポジトリに修正が反映され、リリース済みの環境に対してはデプロイやパッチ適用の準備ができました（デプロイ手順は本マニュアルの範囲外とします）。  

9. **developブランチへの修正反映:** mainで修正したバグは、並行して進めている開発ブランチ（develop）にも取り込んでおく必要があります。修正を反映しないと、次回リリース時に同じ不具合が復活してしまう恐れがあるためです。修正の取り込み方法は2通りありますが、ここでは**bugfixブランチをdevelopにマージ**する方法を取ります。  

   - Visual Studioで**developブランチ**をチェックアウトします。  
   - **マージ操作**にて、先ほどのbugfixブランチを現在のブランチ(develop)にマージします（Gitメニューの[ブランチの管理]からbugfixブランチを右クリック -> 「現在のブランチ(develop)にマージ」）。  
   - 必要に応じてコンフリクトを解消し、マージコミットを作成してコミットします。  
   - developブランチを**プッシュ**してリモート(origin/develop)を更新します。

   以上により、developブランチにもバグ修正が取り込まれました。これで今後の開発にも修正内容が反映され、mainとdevelopのコード差異が埋ままります。

10. **不要になったブランチの削除（任意）:** 修正に使用したbugfixブランチは役目を終えたら削除して構いません。ローカルのGitリポジトリでは **[Git] -> [ブランチの管理]** からbugfixブランチを右クリックし、**[ブランチの削除]** を選択します。また、リモートにプッシュしていた場合はAzure DevOps上でもブランチを削除できます（Pull Requestを作成してマージ完了した場合は自動削除オプションで消えることもあります）。**ブランチを削除しても、その中のコミット履歴はマージ先のブランチ（mainやdevelop）に残っている**ため安心してください。

### 4.4 リリース作業時の流れ（develop → mainへのマージ）

**状況:** 開発中の機能が一通り完成し、次のリリースを行う段階になりました。ここでは、日々の開発が行われていたdevelopブランチの内容を、本番用のmainブランチにマージしてリリース準備をする流れを説明します。

**手順:**

1. **最終確認とブランチの最新化:** 現在のdevelopブランチの内容がリリース可能な状態か確認します。必要ならコードのレビューやテストを完了させます。また、mainブランチに過去入ったバグ修正（hotfix）がdevelopに取り込まれているか確認します。もしdevelopに取り込まれていない修正がmainにあれば、事前にdevelopブランチ上で**mainブランチをマージ**して差分を反映させておきます（こうすることで、リリース時に修正漏れがなくなります）。準備が整ったら、Visual Studioでdevelopブランチの最新コミットをプッシュしておき、リモートとも同期した状態にします。

2. **mainブランチへの切り替え:** リリース作業を行うため**mainブランチ**をチェックアウトします（Visual StudioのGitメニューからmainを選択）。リモートのmainも念のためプルして最新にしておきます。続いて、**developブランチをmainにマージ**します。

3. **developブランチのマージ:** Visual Studio上で **[Git] -> [ブランチの管理]** を開き、**developブランチ**を右クリックして **「現在のブランチにマージ」** を実行します。現在のブランチ(メイン)に開発内容がすべて取り込まれます。コミット履歴上、developの全コミットがmainに反映され、１つのマージコミットが作られるか（またはfast-forwardでそのまま進む）形になります。  
   （ここにスクリーンショットを挿入：mainブランチ上でdevelopをマージしているところ）

   - **コンフリクトの対処:** もしこの時点でコンフリクトが発生した場合、Visual Studioの指示に従い解消します。通常、事前にdevelopを最新化していれば大きなコンフリクトは起きませんが、ファイルの削除やフォーマットの差異などで軽微な衝突が起こる可能性があります。各差分を確認し、正しい最終コードになるようマージしてください。

4. **リリースコミットの確定:** developの内容がmainに取り込まれたら、**コミット**して確定させます（マージコミットメッセージはそのままでも、必要に応じて「Release vX.X 統合」などに編集しても構いません）。これでmainブランチ上にリリース用の最新コードが揃いました。

5. **リモートへのプッシュ:** mainブランチの最新状態をリモート（origin/main）にプッシュします。**[Git] -> [プッシュ]** を実行し、Azure DevOps上のmainブランチを更新します。プッシュが成功すると、リポジトリのmainがリリース準備完了のコードに置き換わります。他のメンバーには通知するか、必要に応じてタグ付けなどを行いましょう。

6. **タグの作成（任意）:** リリースのバージョン識別用に、mainブランチの最新コミットに**タグ(Tag)**を付与することができます。タグはビルド番号やバージョン名を付けると後から参照しやすくなります。Visual Studioでは **[Git] -> [タグの作成]** から現在チェックアウト中のコミットにタグ付けできます（例: `v1.2.0` 等）。タグを作成するとリモートにプッシュする際にタグ情報も共有され、Azure DevOps上でもタグ一覧に表示されます。

7. **リリースの実施:** Git上の準備が整ったので、あとはリリースのプロセス（ビルド/デプロイ）に移ります。Azure DevOpsのパイプラインを利用している場合は、mainブランチの更新をトリガーにCI/CDが走り、新バージョンがデプロイされるでしょう。手動デプロイの場合も、mainブランチのコードを元にリリース用のビルドを行います。

8. **次の開発サイクルへ:** リリースが完了したら、引き続き新しい開発はdevelopブランチ上で行います。必要ならば`develop`ブランチのバージョン番号やREADMEを更新し、次リリースに向けた開発サイクルを開始します。基本的にmainブランチは次のリリースまで変更されない状態となり、開発は再びdevelop上で進めていきます。

> **注意:** リリースマージ時は**マージする方向**を間違えないよう注意してください。必ず「mainブランチにdevelopブランチをマージ」します（develop上でmainをマージしてしまうと、開発中の変更がmainに反映されず大変危険です）。Visual Studioで操作する際も、**現在mainブランチにチェックアウトしていること**を確認してからdevelopのマージを実行しましょう。

---

以上、Git初心者向けに基本概念と日常運用手順、ケース別の対応方法を説明しました。最初はSVNとの違いに戸惑うかもしれませんが、**Visual StudioのGUI操作**と**ブランチ運用ルール**に慣れれば、Gitでも安心して開発を進められるようになります。困ったときはこのマニュアルを見直し、落ち着いて対処してください。Gitは強力なツールですが、手順を踏めば怖がる必要はありません。ぜひ積極的に活用していきましょう！