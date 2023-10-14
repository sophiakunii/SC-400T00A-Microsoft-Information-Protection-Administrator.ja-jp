---
lab:
  title: 'デモのセットアップ: 管理のための環境の準備'
  module: Module 0 - Lab Setup
---

## WWL テナント - 使用条件

講師による指導付きトレーニング配信の一環としてテナントを提供されている場合は、講師による指導付きトレーニングでハンズオン ラボをサポートする目的でテナントを利用できることに注意してください。

テナントを共有したり、ハンズオン ラボ以外の目的で使用したりしないでください。 このコースで使われるテナントは試用版テナントであり、クラスが終了し、拡張機能の対象となっていない場合は、使用したりアクセスしたりすることはできません。

テナントを有料サブスクリプションに変換することはできません。 このコースの一環として取得したテナントは Microsoft Corporation の財産のままであり、当社はいつでもアクセス権とリポジトリを取得する権利を留保します。

# ラボのセットアップ: 管理のための環境の準備

このラボでは、管理タスク用に環境を構成して準備します。 提供された手順に従って、重要な機能と設定が事前に有効になっていることを確認し、今後のラボ アクティビティでの学習エクスペリエンスを容易にすることができます。 この準備には、必要な機能のアクティブ化、管理アクセス許可の設定、key 要素の適切な構成の確保が含まれます。

## タスク - Microsoft Purview ポータルで監査を有効にする

このタスクでは、Microsoft Purview コンプライアンス ポータルで監査を有効にします。 この追跡機能により、ポータルのアクティビティを監視して可視性とアカウンタビリティを確保できます。

1. 引き続き、Client 1 VM (LON-CL1) には **lon-cl1\admin** アカウントでログインし、Microsoft 365 には MOD 管理者アカウントでログインしている必要があります。

1. **Microsoft Edge** で、 **https://compliance.microsoft.com** に移動します。

1. 左側のナビゲーション ウィンドウで **[監査]** を選びます。

1. **[監査]** ページで、 **[ユーザーと管理者のアクティビティの記録を開始する]** を選択して、監査ログをアクティブにします。

## タスク - 秘密度ラベルのサポートを有効にする

このタスクでは、MSOnline モジュールと SharePoint Online PowerShell モジュールをインストールし、テナントでの秘密度ラベルのサポートを有効にします。

1. Client 1 VM (LON-CL1) に **lon-cl1\admin** アカウントでログインします。

1. マウスの右ボタンでスタート メニューを選択して管理者特権で PowerShell ウィンドウを開き、**Windows PowerShell** を選択して管理者として実行します。

1. **[ユーザー アカウント制御]** ウィンドウで **[はい]** をクリックして確認し、Enter キーを押します。

1. 次のコマンドレットを入力して、最新の MS Online PowerShell モジュール バージョンをインストールします。

   ```powershell
   Install-Module -Name MSOnline
   ```

1. NuGet セキュリティ ダイアログと信頼されていないレポジトリ セキュリティ ダイアログを **Y** (はい) で確認し、Enter キーを押します。  この処理が完了するまでしばらく時間がかかる場合があります。

1. 次のコマンドレットを入力して、最新の SharePoint Online PowerShell モジュール バージョンをインストールします。

    ```powershell
    Install-Module -Name Microsoft.Online.SharePoint.PowerShell
    ```

1. 信頼されていないリポジトリ セキュリティ ダイアログで "はい" の **Y** を入力して確認し、Enter キーを押します。

1. 次のコマンドレットを入力して、MS Online サービスに接続します。

    ```powershell
    Connect-MsolService
    ```

1. **[Sign into your account] (アカウントにサインイン)** フォームで、**MOD 管理者** admin@WWLxZZZZZZ.onmicrosoft.com としてサインインします (ZZZZZZ はラボ ホスティング プロバイダーから提供された一意のテナント ID)。  Joni のパスワードは、ラボ ホスティング プロバイダーから支給されます。

1. サインイン後、PowerShell ウィンドウを選択します。

1. 次のコマンドレットを入力して、ドメインを取得します。

    ```powershell
    $domain = get-msoldomain
    ```

1. 次のコマンドレットを入力して、SharePoint 管理 URL を作成します。

    ```powershell
    $adminurl = "https://" + $domain.Name.split('.')[0] + "-admin.sharepoint.com"
    ```

1. 次のコマンドレットを入力して、SharePoint Online 管理センターにサインインします。

    ```powershell
    Connect-SPOService -url $adminurl
    ```

1. **[アカウントにサインイン]** フォームで、**MOD 管理者** としてサインインします。 admin@WWLxZZZZZZ.onmicrosoft.com (ZZZZZZ はラボ ホスティング プロバイダーから支給された固有のテナント ID)。  管理者のパスワードは、ラボ ホスティング プロバイダーから支給されます。

1. サインイン後、PowerShell ウィンドウを選択します。

1. 次のコマンドレットを入力して、秘密度ラベルのサポートを有効にします。

    ```powershell
    Set-SPOTenant -EnableAIPIntegration $true
    ```

1. "はい" の **Y** を入力して確認し、Enter キーを押します。

1. PowerShell ウィンドウを閉じます。

これで、Teams および SharePoint サイトでの秘密度ラベルのサポートが有効になりました。