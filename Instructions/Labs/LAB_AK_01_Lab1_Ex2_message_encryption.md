---
lab:
  title: '演習 2: Microsoft Purview メッセージ暗号化を管理する'
  module: Module 1 - Implement Information Protection
ms.openlocfilehash: ef12c02196f9da1f7093790a0341143703e413b4
ms.sourcegitcommit: 53488624251b6cf8f79f2d1ff561e3f334764821
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2022
ms.locfileid: "147694955"
---
# <a name="lab-1---exercise-2---manage-office-365-message-encryption"></a>ラボ 1 - 演習 2 - Office 365 Message Encryption を管理する

Joni Sherman がパイロット チームで構成しテストする必要がある最初の設定は、Microsoft 365 の組み込みの Office 365 Message Encryption (OME) です。 この目的のために、彼女は既定のテンプレートを変更し、パイロット ユーザーの 1 人に割り当てられる新しいブランド テンプレートを作成します。 次に、パイロット ユーザーは各自のアカウントで OME 機能をテストします。

### <a name="task-1--verify-azure-rms-functionality"></a>タスク 1 – Azure RMS の機能を確認する

このタスクでは、Exchange Online PowerShell モジュールをインストールし、前回の演習でコンプライアンス管理者のロールが割り当てられた Joni Sherman として、自分のテナントの Azure RMS の機能が正しいことを確認します。

1. Client 1 VM (LON-CL1) に **lon-cl1\admin** アカウントでログインしておきます。

1. マウスの右ボタンで Windows ボタンを選択して昇格された PowerShell ウィンドウを開き、 **[Windows PowerShell (管理者)]** を選択します。

1. **[ユーザー アカウント制御]** ウィンドウで、**[はい]** を選択して確定します。

1. 次のコマンドレットを入力して、Exchange Online PowerShell モジュールの最新版をインストールします。

    ```powershell
    Install-Module ExchangeOnlineManagement
    ```

1. NuGet プロバイダー セキュリティ ダイアログで、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。 この処理は、完了するまでに数秒かかる場合があります。

1. 信頼されていないレポジトリ セキュリティ ダイアログで、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。  この処理は、完了するまでに数秒かかる場合があります。

1. 次のコマンドレットを入力して実行ポリシーを変更し、**Enter** キーを押します。

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

1. [実行ポリシーの変更] で、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。 

1. PowerShell ウィンドウを閉じます。

1. マウスの右ボタンで Windows ボタンを選択して通常の PowerShell ウィンドウ (昇格されていない) を開き、 **[Windows PowerShell]** を選択します。

1. 次のコマンドレットを入力して Exchange Online PowerShell モジュールを使用し、テナントに接続します。

    ```powershell
    Connect-ExchangeOnline
    ```

1. **[サインイン]** ウィンドウが表示されたら、JoniS@WWLxZZZZZZ.onmicrosoft.com としてサインインします (ZZZZZZ はラボ ホスティング プロバイダーから支給された固有のテナント ID)。  Joni のパスワードは、ラボ ホスティング プロバイダーから支給されます。

1. 次のコマンドレットを使用して、テナントで Azure RMS および IRM がアクティブ化されていることを確認し、**Enter** キーを押します。

    ```powershell
    Get-IRMConfiguration | fl AzureRMSLicensingEnabled
    ```

1. **AzureRMSLicensingEnabled** の結果が **True** の場合、テナントに対して Azure RMS がアクティブになります。 次の手順に進みます。 

1. 次のコマンドレットを使用して、Office 365 メッセージ暗号化に使用される Azure RMS テンプレートを他のパイロット ユーザー **Megan Bowen** に対してテストし、**Enter** キーを押します。

    ```powershell
    Test-IRMConfiguration -Sender MeganB@contoso.com -Recipient MeganB@contoso.com
    ```

    ![IRM 検証スクリプトの結果。 ](../Media/IRMvalidationl.png)

1. すべてのテストが PASS のステータスであり、エラーが表示されていないことを確認します。

1. PowerShell ウィンドウは開いたままにします。

Exchange Online PowerShell モジュールがインストールされ、テナントに接続し、Azure RMS が正しく機能していることを確認しました。

### <a name="task-2--modify-default-ome-template"></a>タスク 2 - 既定の OME テンプレートを修正する

組織からは Google や Facebook などの ID プロバイダーへの信頼を制限する要件が出されています。 既定では、このようなソーシャル ID は OME で保護されたメッセージにアクセスするためにアクティブ化されるので、組織内のすべてのユーザーに対してソーシャル ID の利用を非アクティブ化する必要があります。

1. Client 1 VM (LON-CL1) には **lon-cl1\admin** アカウントでログインし、Exchange Online が接続された状態の PowerShell ウィンドウが開いている必要があります。

1. 次のコマンドレットを実行して、既定の OME 構成を閲覧します。

    ```powershell
    Get-OMEConfiguration -Identity "OME Configuration" |fl
    ```

1. 設定を確認して、SocialIdSignIn のパラメーターが [True] に設定されていることを確認します。

1. 次のコマンドレットを実行し、OME で保護されたテナントからのメッセージにアクセスするためのソーシャル ID の利用を制限します。

    ```powershell
    Set-OMEConfiguration -Identity "OME Configuration" -SocialIdSignIn:$false
    ```

1. 既定のテンプレートをカスタマイズすることに関する警告メッセージを、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。

1. 既定の構成をもう一度確認し、SocialIdSignIn のパラーメーターが [False] に設定されていることを確認します。

    ```powershell
    Get-OMEConfiguration -Identity "OME Configuration" |fl
    ```

1. 結果には、SocialIDSignIn が False に設定されていることが示されていることに注意してください。 PowerShell ウィンドウとクライアントは開いたままにします。

Office 365 Message Encryption での Google、Facebook などの海外の ID プロバイダーの利用を非アクティブ化しました。

### <a name="task-3--test-default-ome-template"></a>タスク 3 - 既定の OME テンプレートをテストする

テナントのユーザーから Office 365 Message Encryption で保護されたメッセージを受信するときに、ソーシャル ID ダイアログが外部の受信者に対して表示されていないことを確認する必要があります。暗号化されたコンテンツにアクセスするときはいつでも OTP を使用する必要があります。

1.  Client 1 VM (LON-CL1) をそのまま開いておき、Client 2 VM (LON-CL2) に **lon-cl2\admin** アカウントでログインします。

1. 使用可能なすべての Windows 更新がインストールされていることを確認します。更新プログラムのインストールを完了するためにクライアントの再起動は必要ありません。

[//]: <> (最新の OS 更新プログラムをインストールすると、このラボを実行するために必要な新しい Chromium バージョンに Edge ブラウザーも更新されます。)

1. タスク バーから **Microsoft Edge** を開き、 **[Microsoft Edge へようこそ]** ウィンドウが表示されたら、 **[データなしで開始]** を選択し、もう一度 **[このデータなしで続行]** を選び、 **[確認して参照を開始]** を選択します。

1. ウェルカム メッセージが欠落している場合は、 https://microsoft.com/edge に移動し、 **[Windows のダウンロード]** と **[Windows 10]** を選択します。 **[同意してダウンロード]** と **[実行]** を選択して、最新バージョンの Edge ブラウザーをインストールします。 これが完了したら、前の手順を行います。

1. **Microsoft Edge** で、 **https://outlook.office.com** に移動し、Outlook on the web に LynneR@WWLxZZZZZZ.onmicrosoft.com としてログインします (ZZZZZZ はラボ ホスティング プロバイダーから支給された固有のテナント ID)。 Lynne Robin のパスワードは、ラボ ホスティング プロバイダーから支給されます。 ヒント: 通常、ラボ テナントの MOD 管理者のパスワードと同じです。

1. **[サインインの状態を維持しますか?]** ダイアログボックスで、**[今後このメッセージを表示しない]** チェックボックスを選択し、**[いいえ]** を選択します。

1. **[パスワードを保存]** ダイアログで、**[保存]** を選択し、ブラウザーにパイロット ユーザーのパスワードを保存します。

1. **[翻訳元の言語]** ウィンドウが表示されたら、下向きの矢印を選択し、**[...からは翻訳しない]** を選択します。

1. Outlook on the web の左上部から **[新規メッセージ]** を選択します。

1. **[To]** に、テナント ドメインには存在しない、個人用またはその他サード パーティのメール アドレスを入力します。 「**秘密のメッセージ**」と件名に、「**私の重大な秘密のメッセージ。**」 と本文に入力します。

1. 上部のウィンドウから、**[暗号化]** を選択し、メッセージを暗号化します。 メッセージが正常に暗号化されると、"このメッセージは暗号化されています。 受信者は暗号化を削除できません" のような通知が表示されるはずです。

1. **[送信]** を選択してメッセージを送信します。

1. 個人用のメール アカウントにサインインし、Lynne Robbins からのメッセージを開きます。 Microsoft アカウント（@outlook.com など）にこのメールを送った場合、暗号化は自動的に行われる可能性があり、メッセージが自動的に表示されます。 メールを他のメール サービス (@google.com など) に送信した場合、暗号化を処理し、メッセージを読むために、次の手順を踏む必要がある可能性があります。

    **注:** 迷惑メール フォルダーに Lynne Robbins からのメッセージがあるかどうかを確認する必要があるかもしれません。

1. **[メッセージを読む]** を選択します。

1. ソーシャル ID をアクティブ化しなければ、Google アカウントで認証するボタンはありません。

1. **[ワンタイム パスコードを使用してサインイン]** を選択して、制限時間付きパスコードを受け取ります。

1. 個人用のメール ポータルを開き、**"メッセージを表示するためのワンタイム パスコード"** という件名のメッセージを開きます。

1. パスコードをコピーして、OME ポータルにペーストし、**[続行]** を選択します。

1. 暗号されたメッセージを確認します。

1. クライアント 1 VM (LON-CL1) は開いたままにしておきます。

修正された、既定の OME テンプレートを非アクティブ化したソーシャル ID でテストしました。

### <a name="task-4--create-custom-branding-template"></a>タスク 4 – カスタム ブランド テンプレートを作成する

組織の財務部が送信する、保護されたメッセージには、カスタマイズした導入や本文、フッターの免責事項のリンクなど、特別なブランド化が必要です。 財務のメッセージはまた、7 日経過した後期限切れとします。 このタスクでは、OME 構成を新しくカスタマイズし、財務部が送信するすべてのメールに対しその OME 構成を適用する転送ルールを作成します。

1. Client 1 VM (LON-CL1) に **lon-cl1\admin** アカウントとしてログインします。Exchange Online が接続された状態の PowerShell ウィンドウを開いたままにしておく必要があります。

1. 次のコマンドレットを実行して、新しい OME 構成を作成します。

    ```powershell
    New-OMEConfiguration -Identity "Finance Department" -ExternalMailExpiryInDays 7
    ```

1. テンプレートのカスタマイズに関する警告メッセージを、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。 

1. 導入のテキストメッセージを次のコマンドレットで変更します。

    ```powershell
    Set-OMEConfiguration -Identity "Finance Department" -IntroductionText " from Contoso Ltd. finance department has sent you a secure message."
    ```

1. テンプレートのカスタマイズに関する警告メッセージを、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。

1. メッセージの本文メールテキストを次のコマンドレットで変更します。

    ```powershell
    Set-OMEConfiguration -Identity "Finance Department" -EmailText "Encrypted message sent from Contoso Ltd. finance department. Handle the content responsibly."
    ```

1. テンプレートのカスタマイズに関する警告メッセージを、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。

1. 免責事項 URL を変更し、Contoso のプライバシーに関する声明のサイトを指し示すようにします。

    ```powershell
    Set-OMEConfiguration -Identity "Finance Department" -PrivacyStatementURL "https://contoso.com/privacystatement.html"
    ```

1. テンプレートのカスタマイズに関する警告メッセージを、[はい] を示す **[Y]** を選択して確定し、**Enter** キーを押します。

1. 次のコマンドレットを使用し、メール フロー ルールを作成します。このメール フロー ルールはカスタム OME テンプレートを財務チームが送信するメッセージすべてに適用します。  この処理は、完了するまでに数秒かかる場合があります。

    ```powershell
    New-TransportRule -Name "Encrypt all mails from Finance team" -FromScope InOrganization -FromMemberOf "Finance Team" -ApplyRightsProtectionCustomizationTemplate "Finance Department" -ApplyRightsProtectionTemplate Encrypt
    ```

1. 次のコマンドレットを入力して変更を確認します。
    
    ```powershell
    Get-OMEConfiguration -Identity "Finance Department" | Format-List
    ```
    
1. PowerShell は開いたままにします。

財務部のメンバーが外部の受信者にメッセージを送信する際に、カスタム OME テンプレートが自動的に適用される転送ルールが新しく作成しました。

### <a name="task-5--test-the-custom-branding-template"></a>タスク 5 – カスタム ブランド テンプレートをテストする

新しい、カスタム OME 構成を検証するため、財務チームのメンバーである Lynne Robbins のアカウントをもう一度利用する必要があります。

1. Client 2 VM (LON-CL2) に **lon-cl2\admin** アカウントでログインします。 

1. タスク バーから **[Microsoft Edge]** を選択します。 [Outlook on the web] タブはまだ開いているはずです。**Lynne Robbins** としてログインする必要があります。 

1. Outlook on the web の左上部から **[新規メッセージ]** を選択します。

1. **[To]** に、テナント ドメインには存在しない、個人用またはその他サード パーティのメール アドレスを入力します。 「*財務レポート*"」と件名に、「"*秘密の財務情報。*"」 と本文に入力します。

1. **[送信]** を選択してメッセージを送信します。

1. 個人用のメール アカウントにサインインし、Lynne Robbins からのメッセージを開きます。

1. 以下の画像のような Lynne Robbins からのメッセージとなるはずです。  **[メッセージを読む]** を選択します。

    ![Lynne Robbins からの暗号化されたメールの例 ](../Media/EncryptedEmail.png)

1. 両方のオプションが利用可能となり、カスタマイズした OME 構成でソーシャル ID がアクティブになります。 **[ワンタイム パスコードを使用してサインイン]** を選択して、制限時間付きパスコードを受け取ります。

1. 個人用のメール ポータルを開き、**"メッセージを表示するためのワンタイム パスコード"** という件名のメッセージを開きます。

1. パスコードをコピーして、OME ポータルにペーストし、**[続行]** を選択します。

1. カスタム ブランドの暗号化されたメッセージを確認します。

新しくカスタマイズされた OME テンプレートがテストされました。

# <a name="proceed-to-lab-1---exercise-3"></a>ラボ 1 - 演習 3 に進む
