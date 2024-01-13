---
lab:
  title: 演習 3 - 情報バリアを構成する
  module: Module 5 - Manage insider and privacy risk in Microsoft 365
---

# ラボ 5 - 演習 3 - 情報バリアを構成する

Contoso Ltd. のコンプライアンス管理者である Joni は、Microsoft 365 で情報バリアを構成および管理する責任を負っています。 情報バリアは、明確な境界を維持し、組織内の特定のグループまたは個人間の不正な通信を防止するうえで重要な役割を果たします。 情報バリアを実装すると、規制の準拠、機密情報の保護、利益相反の最小化を確保します。 このセットアップにより、セキュリティで保護された作業環境が作成され、データの機密性が保護され、Contoso Ltd. のコンプライアンスへのコミットメントがサポートされます。

## タスク 1: Microsoft Teams で名前による検索が有効になっていることを確認する

このタスクでは、Microsoft Teams で **[名前で検索**] 機能が有効になっていることを確認します。 この機能を使用すると、ユーザーは組織内の特定の個人を簡単に検索して見つけることができます。 提供されている手順に従って、この機能を構成してアクティブ化し、コラボレーションを強化し、組織の Microsoft Teams 環境内でのコミュニケーションを効率化します。

1. Client 1 VM (LON-CL1) に **lon-cl1\admin** アカウントでログインします。

1. 今も Joni のアカウントでサインインしている場合は、このアカウントからサインアウトし、すべてのブラウザー ウィンドウを閉じてください。

1. **Microsoft Edge** で、 **`https://admin.teams.microsoft.com`** に移動し、Microsoft Purview ポータルに MOD 管理者 admin@WWLxZZZZZZ.onmicrosoft.com としてログインしてください (ZZZZZZ はラボ ホスティング プロバイダーから支給された一意のテナント ID です)。

1. 左側のナビゲーション ウィンドウの **[Teams]** ドロップダウンで、 **[Teams の設定]** を選択してください

1. **[名前で検索]** まで下にスクロールしてください。 この機能が [オフ] に設定されている場合は、この機能を **[オン]** に切り替え、 **[保存]** を選択してこの設定を保存してください。

1. **[変更が有効になるまでに時間がかかります]** ポップアップで、 **[確認]** を選択してください

    >**注:** この変更が有効になるには数時間かかる場合があります。

## タスク 2: Microsoft Teams で情報バリアの管理同意を有効にする

このタスクでは、Microsoft Teams で情報バリア (IB) の管理同意を有効にします。 この構成では、Teams チャネルなどのグループから IB に準拠していないユーザーを削除できるようにすることで、コンプライアンスを確保します。

1. マウスの右ボタンで Windows ボタンを選択して昇格された PowerShell ウィンドウを開き、 **[Windows PowerShell (管理者)]** を選択します。

1. **[ユーザー アカウント制御]** ウィンドウで、**[はい]** を選択して確定します。

1. 次のコマンドレットを入力して、最新バージョンの Azure AD モジュールをインストールしてください。

    ```powershell
    Install-Module AzureAD
    ```

1. NuGet プロバイダー セキュリティ ダイアログで、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。 この処理は、完了するまでに数秒かかる場合があります。

1. 信頼されていないレポジトリ セキュリティ ダイアログで、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。  この処理は、完了するまでに数秒かかる場合があります。

1. 次の PowerShell コマンドレットを実行します。

    ````powershell
    Connect-AzureAD -Tenant "WWLxZZZZZZ"
    $appId="bcf62038-e005-436d-b970-2a472f8c1982" 
    $sp=Get-AzureADServicePrincipal -Filter "appid eq '$($appid)'"
    if ($sp -eq $null) { New-AzureADServicePrincipal -AppId $appId }
    Start-Process  "https://login.microsoftonline.com/common/adminconsent?client_id=$appId"
    ````

    >**注:** 必ず ZZZZZZ を更新してください。 ZZZZZZ はラボ ホスティング プロバイダーから支給された一意のテナント ID です。

1. ダイアログが表示されたら、MOD 管理者アカウントでログインしてください

1. **[要求されたアクセス許可]** ダイアログボックスで情報を確認し、 **[同意する]** を選択してください。

Azure AD モジュールを正常にインストールし、必要なアクセス許可を付与し、要求されたアクセス許可を受け入れたので、PowerShell を使用して構成を続行できます。

## タスク 3: 組織内のユーザーをセグメント化する

このタスクでは、PowerShell を使用して Security & Compliance モジュールに接続し、**Legal** および **Marketing** 部門の組織セグメントを作成します。

1. 管理者特権の PowerShell ウィンドウは引き続き開いているはずです。

1. **[PowerShell]** ウィンドウで、Security & Compliance PowerShell に接続するコマンドレットを入力してください

    ````powershell
    Connect-IPPSSession
    ````

    **Joni Sherman** JoniS@WWLxZZZZZZ.onmicrosoft.com としてサインインします (ここで ZZZZZZ はラボ ホスティング プロバイダーから支給された一意のテナント ID です)。  Joni のパスワードは、ラボ ホスティング プロバイダーから支給されます。

1. **UserGroupFilter** パラメーターを指定して **New-OrganizationSegment** コマンドレットを実行して **Legal** セグメントを作成してください。

    ````powershell
    New-OrganizationSegment -Name "Legal" -UserGroupFilter "Department -eq 'Legal'"
    ````

1. **New-OrganizationSegment** コマンドレットをもう一度実行して **Marketing** セグメントを作成してください。

    ````powershell
    New-OrganizationSegment -Name "Marketing" -UserGroupFilter "Department -eq 'Marketing'"
    ````

1. **Get-OrganizationSegment** コマンドレットを実行して、作成されたセグメントを表示してください。

    ````powershell
    Get-OrganizationSegment
    ````

Security & Compliance PowerShell に正常に接続し、Legal および Marketing 部門の組織セグメントを作成し、Get-OrganizationSegment コマンドレットを使用してセグメントを表示しました。

## タスク 4: 情報バリア ポリシーを作成する

このタスクでは、PowerShell を使用して情報バリア ポリシーを作成し、Legal と Marketing 部門間の通信をブロックします。

1. 管理者特権の PowerShell ウィンドウは引き続き開いているはずです。

1. **SegmentsBlocked** パラメーターを指定して **New-InformationBarrierPolicy** コマンドレットを実行し、新しい **Legal-Marketing** ポリシーを作成してください。

    ````powershell
    New-InformationBarrierPolicy -Name "Legal-Marketing" -AssignedSegment "Legal" -SegmentsBlocked "Marketing" -State Active
    ````

1. PowerShell の種類 **Y** に情報バリア ポリシーの警告に関する通知が表示されたら、**Enter** キーを押してポリシーの作成に進みます。

1. 次に、反対方向にブロックする情報バリア ポリシーを作成する必要があります。 **SegmentsBlocked** パラメーターを指定して **New-InformationBarrierPolicy** コマンドレットを実行し、新しい **Marketing-Legal** ポリシーを作成してください。

    ````powershell
    New-InformationBarrierPolicy -Name "Marketing-Legal" -AssignedSegment "Marketing" -SegmentsBlocked "Legal" -State Active
    ````

1. **Get-InformationBarrierPolicy** コマンドレットを実行して、作成されたポリシーを表示してください。

    ````powershell
    Get-InformationBarrierPolicy
    ````

    >**注:** 作成されたポリシーを確認するときに、ポリシーが **[アクティブ]** としてマークされていることを確認してください。 情報バリア ポリシーは、適用する前にアクティブにする必要があります。

PowerShell を使用して Legal-Marketing と Marketing-Legal 情報バリア ポリシーを正常に作成し、Get-InformationBarrierPolicy コマンドレットを実行して、その状態がアクティブであることを確認しました。

## タスク 5: 情報バリア ポリシーを適用する

このタスクでは、PowerShell を使用してアクティブな情報バリア ポリシーを適用し、アプリケーションの状態をチェックします。

1. 管理者特権の PowerShell ウィンドウは引き続き開いているはずです。

1. **Start-InformationBarrierPoliciesApplication** コマンドレットを実行して、アクティブな情報バリア ポリシーを適用してください。

    ````powershell
    Start-InformationBarrierPoliciesApplication
    ````

1. **Get-InformationBarrierPoliciesApplicationStatus** コマンドレットを実行して、適用されたポリシーを表示してください。

    ````powershell
    Get-InformationBarrierPoliciesApplicationStatus
    ````

    >**注:** システムがポリシーの適用を開始するには、30 分かかります。

1. ポリシーが適用されると、**状態**は **NotStarted** から **Completed** に更新されます。

正常に、Start-InformationBarrierPoliciesApplication コマンドレットを実行して情報バリア ポリシーを適用し、Get-InformationBarrierPoliciesApplicationStatus コマンドレットを使用してアプリケーションの状態を確認できました。
