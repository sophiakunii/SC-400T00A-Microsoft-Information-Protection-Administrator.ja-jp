---
lab:
  title: 演習 3 - DLP レポートを管理する
  module: Module 2 - Implement Data Loss Prevention
---

# ラボ 2 演習 3 - DLP レポートを管理する

あなたは Contoso Ltd. のコンプライアンス管理者、Joni Sherman です。データ損失防止の目的で、組織の Microsoft 365 テナントを構成する任務を負っています。 Contoso Ltd. は米国で自動車運転教習を行っている企業です。あなたには機密の顧客情報がこの組織から漏えいしないようにする役目があります。 新しいコンプライアンス責任者が DLP レポートの確認を支援することが通知されます。

## タスク 1 - DLP レポートに対するアクセスの許可

この演習では、新しいコンプライアンス責任者 Megan Bowen に対して、DLP レポートへのアクセスを許可します。 このコンプライアンス責任者は技術系のユーザーではないため、レポートに目を通すだけで、DLP の構成に変更は行いません。

1. Client 1 VM (LON-CL1) に **lon-cl1\admin** アカウントでログインします。

1. **Microsoft Edge** で、 **`https://compliance.microsoft.com`** に移動し、Microsoft Purview ポータルに **MOD 管理者** admin@WWLxZZZZZZ.onmicrosoft.com としてログインします (ZZZZZZ はラボ ホスティング プロバイダーから支給された固有のテナント ID)。 管理者のパスワードは、ラボ ホスティング プロバイダーから支給されます。

1. 左側のナビゲーション ウィンドウで、**[ロールとスコープ]** を展開し、**[アクセス許可]** を選びます。

1. **[アクセス許可]** ページの **[Microsoft Purview ソリューション]** で、**[ロール]** を選択します。

1. **[Microsoft Purview のロール グループ]** ページで、 **[Information Protection アナリスト]** のフィールドを選びます。

1. **[Information Protection アナリスト]** ポップアップ ページで、 **[編集]** を選びます。

1. **[ロール グループのメンバーの編集]** ページで、 **[+ ユーザーの選択]** を選択します。

1. **[ユーザーの選択]** で、**Megan Bowen** のアカウントの横にあるチェック ボックスをオンにし、 **[選択]** ボタンを選びます。

1. **[ロール グループのメンバーの編集]** ページで、Megan のアカウントが追加されていることを確認し、 **[次へ]** を選びます。

1. **[ロール グループを確認して完了]** で、 **[保存]** を選択します。

1. **[ロール グループが正常に更新されました]** ページで、 **[完了]** を選択します。

これで新しいコンプライアンス責任者に対して、Microsoft 365 コンプライアンス ポータルの DLP レポートに対するアクセスが許可されました。

## タスク 2 - アクティビティ エクスプローラーで DLP データを確認する

このタスクでは、タスク 1 で許可した DLP レポートに対するアクセスが正しく適用されているかテストします。

1. Client 2 VM (LON-CL2) に **lon-cl2\admin** アカウントでログインします。

1. **Microsoft Edge** で、 **`https://compliance.microsoft.com`** に移動し、Microsoft Purview ポータルに **Megan Bowen** MeganB@WWLxZZZZZZ.onmicrosoft.com としてログインします (ZZZZZZ はラボ ホスティング プロバイダーから支給された固有のテナント ID)。

1. 左側のナビゲーション ペインで、 **[データ損失防止]** のドロップダウンを選び、 **[アクティビティ エクスプローラー]** を選びます。

1. **[アクティビティ エクスプローラー]** ページで、 **[組み込みフィルター]** のドロップダウンを選択します。

1. **[アクティビティを検出した DLP ポリシー]** フィルターと **[アクティビティを検出した DLP ポリシー ルール]** フィルターを調べます。

これで、アクセスが構成されて、新しいコンプライアンス責任者が Purview ポータルのレポートを閲覧できることが確認されました。

## タスク 3 - PowerShell を使用して DLP データを調べる

このタスクでは、PowerShell を使用して DLP アクティビティを確認します。

1. 引き続き Client 1 VM (LON-CL1) に **lon-cl1\admin** アカウントでログインしている必要があります。

1. マウスの右ボタンで Windows ボタンを選択して昇格された PowerShell ウィンドウを開き、 **[Windows PowerShell (管理者)]** を選択します。

1. 次のように入力して、Information Protection PowerShell セッションに接続します。

   ``` powershell
   Connect-IPPSSession
   ```

1. **[別のアカウントを使用する]** を選び、**Megan Bowen** のアカウント MeganB@WWLxZZZZZZ.onmicrosoft.com (ここで、ZZZZZZ は、ラボ ホスティング プロバイダーによって提供される一意のテナント ID です) でログインします。

1. `Export-ActivityExplorerData` PowerShell コマンドレットを使用して、PowerShell からアクティビティ エクスプローラーを調べます。 次のスクリプトは、最近 1 週間のアクティビティのグリッド ビューを表示する例です。

   ``` powershell
   $StartTime = (Get-Date).AddDays(-7)
   $EndTime = (Get-Date).AddDays(1)
   $Results = Export-ActivityExplorerData -StartTime $StartTime -EndTime $EndTime -OutputFormat JSON
   ($Results.ResultData) | ConvertFrom-Json | select -ExpandProperty SyncRoot | ogv
   ```

1. これまでのラボから収集したデータを調べて、グリッド ビュー ウィンドウを閉じます。

1. 次のスクリプトは、Filter1 パラメーターを使用して、最近 1 週間のエンドポイント アクティビティを表示する例です。

   ``` powershell
   $StartTime = (Get-Date).AddDays(-7)
   $EndTime = (Get-Date).AddDays(1)
   $Results = Export-ActivityExplorerData -StartTime $StartTime -EndTime $EndTime -Filter1 @("Workload","Endpoint")-OutputFormat JSON
   ($Results.ResultData) | ConvertFrom-Json | select -ExpandProperty SyncRoot | ogv
   ```

1. これまでのラボから収集したデータを調べて、グリッド ビュー ウィンドウを閉じます。

新しいコンプライアンス責任者である Megan Bowen として DLP アクティビティを正常に確認しました。
