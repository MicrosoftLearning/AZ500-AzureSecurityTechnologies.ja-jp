---
lab:
  title: 10 - Key Vault (Always Encrypted を設定して、セキュアなデータを実装する)
  module: Module 03 - Secure Data and Applications
---

# <a name="lab-10-key-vault-implementing-secure-data-by-setting-up-always-encrypted"></a>ラボ 10:Key Vault (Always Encrypted を設定して、セキュアなデータを実装する)
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Azure SQL データベースの Always Encrypted 機能のサポートを利用する概念実証アプリケーションの作成を依頼されました。 このシナリオで使用されるすべてのシークレットとキーは、Key Vault に格納する必要があります。 アプリケーションは、セキュリティの状態を強化するために、Azure Active Directory (Azure AD) に登録する必要があります。 これらの目的を達成するには、概念実証に以下を含める必要があります。

- Azure Key Vault を作成し、キーとシークレットをコンテナーに格納します。
- Always Encrypted を使用して、SQL Database を作成し、データベース テーブル内の列の内容を暗号化します。

>**注**:このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

この概念実証の構築に関連する Azure のセキュリティの側面に焦点を当て続けるために、自動化された ARM テンプレートの展開から開始し、Visual Studio 2019 と SQL Server Management Studio 2018 を使用して仮想マシンをセットアップします。

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:ARM テンプレートから基本インフラストラクチャをデプロイする
- 演習 2:キーとシークレットを使用して Key Vault のリソースを構成する
- 演習 3:Azure SQL データベースとデータ駆動型アプリケーションを構成する
- 演習 4:Azure SQL データベースの暗号化における Azure Key Vault の使用方法を示す

## <a name="key-vault-diagram"></a>Key Vault の図

![image](https://user-images.githubusercontent.com/91347931/157532938-c724cc40-f64f-4d69-9e91-d75344c5e0a2.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>ラボ ファイル：

- **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **\\Allfiles\\Labs\\10\\program.cs**

### <a name="total-lab-time-estimate-60-minutes"></a>予想合計ラボ時間：60 分

### <a name="exercise-1-deploy-the-base-infrastructure-from-an-arm-template"></a>演習 1:ARM テンプレートから基本インフラストラクチャをデプロイする

この演習では、次のタスクを実行します。

- タスク 1:Azure VM と Azure SQL データベースをデプロイする

#### <a name="task-1-deploy-an-azure-vm-and-an-azure-sql-database"></a>タスク 1:Azure VM と Azure SQL データベースをデプロイする

このタスクでは、Azure VM をデプロイします。これにより、デプロイの一部として Visual Studio 2019 と SQL Server Management Studio 2018 が自動的にインストールされます。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal で Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**カスタム テンプレートをデプロイする**」と入力し、**Enter** キーを押します。

3. **[カスタム デプロイ]** ウィンドウで、**[エディターで独自のテンプレートを作成する]** オプションをクリックします。

4. **[テンプレートの編集]** ブレードで、 **[ファイルの読み込み]** をクリックし、 **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** ファイルを見つけて、 **[開く]** をクリックします。

5. **[テンプレートの編集]** ウィンドウで、**[保存]** をクリックします。

6. **[カスタム デプロイ]** ウィンドウの **[デプロイ スコープ]** で、次の設定が構成されていることを確認します (既定値を使用して他の設定を行います)。

   |設定|値|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|**[新規作成]** をクリックして、「**AZ500LAB10**」という名前を入力します|
   |場所|**(米国) 米国東部**|
   |管理ユーザー名|**学生**|
   |管理パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|
   
    >**注**:仮想マシンへのログオンに使用される管理資格情報を変更できますが、変更する必要はありません。

    >**注**:Azure VM をプロビジョニングできる Azure リージョンを特定するには、[ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/) を参照してください

7. **[確認と作成]** ボタンをクリックし、**[作成]** ボタンをクリックしてデプロイを確認します。 

    >**注**:これにより、このラボに必要な Azure VM と Azure SQL データベースのデプロイが開始されます。 

    >**注**:ARM テンプレートのデプロイが完了するのを待たずに、次の演習に進んでください。 デプロイには約 **20～25 分間**かかります。 

### <a name="exercise-2-configure-the-key-vault-resource-with-a-key-and-a-secret"></a>演習 2:キーとシークレットを使用して Key Vault のリソースを構成する

>**注**:このラボのすべてのリソースに対して、**東部 (米国)** リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

この演習では、次のタスクを実行します。

- タスク 1:Key Vault を作成して構成する
- タスク 2:Key Vault にキーを追加する
- タスク 3:Key Vault にシークレットを追加する

#### <a name="task-1-create-and-configure-a-key-vault"></a>タスク 1:Key Vault を作成して構成する

このタスクでは、Azure Key Vault リソースを作成します。 Azure Key Vault のアクセス許可も構成します。

1. Azure portal の右上にある最初のアイコン（検索バーの横）をクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** を選択します。

2. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

3. [Cloud Shell] ペイン内の PowerShell セッションで次のように実行して、リソース グループ **AZ500LAB10** に Azure Key Vault を作成します。 （タスク 1 でラボのリソース グループに別の名前を使用した場合は、このタスクでもその名前を使用してください）。 Key Vault 名は一意である必要があります。 選択した名前を覚えておいてください。 このラボ全体で必要になります。  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >**注**:最後のコマンドの出力には、コンテナー名とコンテナーURIが表示されます。 コンテナー URI の形式は `https://<vault_name>.vault.azure.net/` です

4. [Cloud Shell] ペインを閉じます。 

5. Azure portal ページの上部にある **[ソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**リソース グループ**」と入力し、**Enter** キーを押します。

6. **[リソース グループ]** ウィンドウのリソース グループのリストで、**AZ500LAB10** (または以前にリソース グループに選択した他の名前) エントリをクリックします。

7. リソース グループ ブレードで、新しく作成された Key Vault を表すエントリをクリックします。 

8. Key Vault ブレードの **[概要]** セクションで、 **[アクセス ポリシー]** をクリックし、 **[アクセス ポリシーの追加]** をクリックします。

9. **[アクセス ポリシーの追加]** ウィンドウで、次の設定を指定します (他の設定はすべて既定値のままにします)。 

    |設定|値|
    |----|----|
    |テンプレートから構成する (オプション)|**キー、シークレット、および証明書の管理**|
    |キーのアクセス許可|**[すべて選択]** をクリックすると、アクセス許可が **17 個選択されます** ( **[ポリシーのローテーション操作]** のアクセス許可が**オフになっている**ことを確認します) |
    |シークレットのアクセス許可|**[すべて選択]** をクリックして、合計 **8 個**のアクセス許可を選択します|
    |証明書のアクセス許可|**[すべて選択]** をクリックして、合計 **16 個**の権限を選択します|
    |プリンシパルの選択|**[選択なし]** をクリックし、 **[プリンシパル]** ブレードでユーザー アカウントを選択して、 **[選択]** をクリックします|

10. **[アクセス ポリシーの追加]** ウィンドウに戻り、**[追加]** をクリックしてアクセス ポリシーを追加します。次に、キー コンテナーの [アクセス ポリシー] ウィンドウに戻り、**[保存]** をクリックして変更を保存します。 

#### <a name="task-2-add-a-key-to-key-vault"></a>タスク 2:Key Vault へのキーの追加

このタスクでは、Key Vault にキーを追加し、キーに関する情報を表示します。 

1. Azure portal の [Cloud Shell] ペインで PowerShell セッションを開きます。

2. Cloud Shell ペインの左上のドロップダウン メニューで、「**PowerShell**」が選択されていることを確認します。

3. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、ソフトウェアで保護されたキーを Key Vault に追加します。 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**注**:キーの名前は **MyLabKey** です。

4. Cloud Shell ペイン内の PowerShell セッションで次を実行して、キーが作成されたことを確認します。

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

5. Cloud Shell ペイン内の PowerShell セッションで次を実行して、キー識別子を表示します。

    ```powershell
    $key.key.kid
    ```

6. Cloud Shell　ペインを最小化します。 

7. Azure portal に戻り、キー コンテナーのウィンドウの **[設定]** セクションで、**[キー]** をクリックします。

8. キーの一覧、**MyLabKey** エントリをクリックし、**MyLabKey** のウィンドウで、キーの現在のバージョンを表すエントリをクリックします。

    >**注**:作成したキーに関する情報を調べます。

    >**注**:キー識別子を使用して、任意のキーを参照できます。 最新バージョンを入手するには、`https://<key_vault_name>.vault.azure.net/keys/MyLabKey` を参照します。または、次で特定のバージョンを取得します: `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### <a name="task-3-add-a-secret-to-key-vault"></a>タスク 3:Key Vault にシークレットを追加する

1. Cloud Shell ペインに切り替えます。

2. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、安全な文字列値を持つ変数を作成します。

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

3.  Cloud Shell ペイン内の PowerShell セッションで、次を実行してシークレットをコンテナーに追加します。

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**注**:シークレットの名前は SQLPassword です。 

4.  Cloud Shell ペイン内の PowerShell セッションで次を実行して、シークレットが作成されたことを確認します。

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

5. Cloud Shell　ペインを最小化します。 

6. Azure portal でキー コンテナーのウィンドウに戻り、**[設定]** セクションで **[シークレット]** をクリックします。

7. シークレットのリストで、**SQLPassword** エントリをクリックし、**SQLPassword** ブレードで、シークレットの現在のバージョンを表すエントリをクリックします。

    >**注**:作成したシークレットに関する情報を調べます。

    >**注**:シークレットの最新バージョンを入手するには、`https://<key_vault_name>.vault.azure.net/secrets/<secret_name>` を参照します。特定のバージョンを入手するには、`https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>` を参照します


### <a name="exercise-3-configure-an-azure-sql-database-and-a-data-driven-application"></a>演習 3:Azure SQL データベースとデータ駆動型アプリケーションを構成する

この演習では、次のタスクを実行します。

- タスク 1:クライアント アプリケーションを有効にして Azure SQL データベース サービスにアクセスできるようにする。
- タスク 2:アプリケーションからキー コンテナーへのアクセスを許可するポリシーを作成する。
- タスク 3:SQL Azure データベースの ADO.NET 接続文字列を取得する 
- タスク 4:Visual Studio 2019 および SQL Management Studio 2018 を実行している Azure VM にログオンします
- タスク 5:SQL Database でテーブルを作成し、暗号化するデータ列を選択する


#### <a name="task-1-enable-a-client-application-to-access-the-azure-sql-database-service"></a>タスク 1:クライアント アプリケーションを有効にして Azure SQL データベース サービスにアクセスできるようにする。 

このタスクでは、クライアント アプリケーションを有効にして Azure SQL データベース サービスにアクセスできるようにします。 これは、必要な認証を設定し、アプリケーションの認証に必要なアプリケーション ID とシークレットを取得することにより行われます。

1. Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索]** テキスト ボックスに「**アプリの登録**」と入力して、**Enter** キーを押します。

2. **[アプリの登録]** ウィンドウで、**[+ 新規登録]** をクリックします。 

3. **[アプリケーションの登録]** ウィンドウで、次の設定を指定します (他の設定はすべて既定値のままにします)。

    |設定|値|
    |----|----|
    |名前|**sqlApp**|
    |リダイレクト URI (省略可能)|**Web** および **https://sqlapp**|

4. **[アプリケーションの登録]** ウィンドウで、**[登録]** をクリックします。 

    >**注**: 登録が完了すると、ブラウザーは自動的に **sqlApp** のウィンドウにリダイレクトされます。 

5. **sqlApp** ウィンドウで、**[アプリケーション (クライアント) ID]** の値を確認します。 

    >**注**:この値は記録しておいてください。 これは、次のタスクで必要になります。

6. **sqlApp** ウィンドウの **[管理]** セクションで、**[証明書とシークレット]** をクリックします。

7. **[sqlApp | 証明書とシークレット]** ブレードの **[クライアント シークレット]** セクションで、 **[+新しいクライアント シークレット]** をクリックします

8. **[クライアント シークレットの追加]** ウィンドウで、次の設定を指定します。

    |設定|値|
    |----|----|
    |説明|**Key1**|
    |Expires|**12 か月**|
    
9. **[追加]** をクリックして、アプリケーションの認証資格情報を更新します。

10. **[sqlApp | 証明書とシークレット]** ブレードで、**Key1** の値を特定します。

    >**注**:この値は記録しておいてください。 これは、次のタスクで必要になります。 

    >**注**:ブレードから移動する*前*に、必ず値をコピーしてください。 コピーすると、クリア テキスト値を取得することができなくなります。


#### <a name="task-2-create-a-policy-allowing-the-application-access-to-the-key-vault"></a>タスク 2:アプリケーションからキー コンテナーへのアクセスを許可するポリシーを作成する。

このタスクでは、新しく登録されたアプリに、Key Vault に格納されているシークレットにアクセスするためのアクセス許可を付与します。

1. Azure portal の [Cloud Shell] ペインで PowerShell セッションを開きます。

2. Cloud Shell ペインの左上のドロップダウン メニューで、「**PowerShell**」が選択されていることを確認します。

3. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、前のタスクで記録した**アプリケーション (クライアント) ID** を格納する変数を作成します (`<Azure_AD_Application_ID>` プレースホルダーを**アプリケーション (クライアント) ID** の値に置き換えます)。
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
4. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、Key Vault 名を格納する変数を作成します。
    ```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

5. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、前のタスクで登録したアプリケーションに Key Vault の権限を付与します。

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

6. [Cloud Shell] ペインを閉じます。 


#### <a name="task-3-retrieve-sql-azure-database-adonet-connection-string"></a>タスク 3:SQL Azure データベースの ADO.NET 接続文字列を取得する 

演習 1 の ARM テンプレートのデプロイでは、Azure SQL Server インスタンスと、**medical** という名前の Azure SQL データベースをプロビジョニングしました。 空のデータベース リソースを新しいテーブル構造で更新し、暗号化するデータ列を選択する

1. Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索]** テキスト ボックスに「**SQL データベース**」と入力し、**Enter** キーを押します。

2. SQL データベースの一覧で、 **[medical(<randomsqlservername>)]** エントリをクリックします。

    >**注**:データベースが見つからない場合は、演習1で開始したデプロイがまだ完了していない可能性があります。 これを検証するには、Azure リソース グループ "AZ500LAB10" (または他の選んだ名前) を参照し、[設定] ウィンドウから **[デプロイ]** を選択します。  

3. SQL データベースのウィンドウの **[設定]** セクションで、**[接続文字列]** をクリックします。 

    >**注**:このインターフェイスには、ADO.NET、JDBC、ODBC、PHP、および Go の接続文字列が含まれています。 
   
4. **ADO.NET 接続文字列**を記録します。 この情報は後で必要になります。

    >**注**:接続文字列を使用する場合は、`{your_password}` プレースホルダーを、演習 1 のデプロイで構成したパスワードに置き換えてください。

#### <a name="task-4-log-on-to-the-azure-vm-running-visual-studio-2019-and-sql-management-studio-2018"></a>タスク 4:Visual Studio 2019 および SQL Management Studio 2018 を実行している Azure VM にログオンします

このタスクでは、演習 1 で開始した Azure VM にログオンします。 この Azure VM は、Visual Studio 2019 と SQL Server Management Studio 2018 をホストします。

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. Azure portal ページの上部の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスに「**仮想マシン**」と入力し、**Enter** キーを押します。

2. 表示されている仮想マシンの一覧で、**az500-10-vm1**のエントリを選択します。 **az500-10-vm1** ウィンドウの **[要点]** ウィンドウで、**[パブリック IP アドレス]** を記録します。 これは後で使用します。 

#### <a name="task-5-create-a-table-in-the-sql-database-and-select-data-columns-for-encryption"></a>タスク 5:SQL Database でテーブルを作成し、暗号化するデータ列を選択する

このタスクでは、SQL Server Management Studio を使用して SQL データベースに接続し、テーブルを作成します。 次に、Azure Key Vault から自動生成されたキーを使用して 2 つのデータ列を暗号化します。 

1. Azure portal で、**医療用** SQL データベースのブレードに移動し、 **[必須事項]** セクションで **[サーバー名]** を特定 (クリップボードにコピー) してから、ツールバーで **[サーバー ファイアウォールの設定]** をクリックします。  

    >**注**:サーバー名を記録します。 このタスクの後半でサーバー名が必要になります。

2. **[ファイアウォール設定]** ウィンドウで、**[規則名]** まで下にスクロールし、次の設定を指定します。 

    |設定|値|
    |---|---|
    |規則の名前|**Mgmt VM を許可する**|
    |開始 IP|az500-10-vm1 のパブリック IP アドレス
終了IP|
    |終了 IP|az500-10-vm1 のパブリック IP アドレス
終了IP|

3. **[保存]** と **[OK]** をクリックして変更を保存し、確認ウィンドウを閉じます。 

    >**注**:これにより、サーバーファイアウォールの設定が変更され、このラボでデプロイした Azure VM のパブリック IP アドレスから医療用データベースに接続できるようになります。

4. **az500-10-vm1** のウィンドウに戻り、**[概要]** をクリックし、次に **[接続]** をクリックして、ドロップダウン メニューで **[RDP]** をクリックします。 

5. **[RDP ファイルのダウンロード]** をクリックし、それを使用して、リモート デスクトップ経由で **az500-10-vm1** Azure VM に接続します。 認証を求められたら、次の資格情報を入力します。

    |設定|値|
    |---|---|
    |ユーザー名|**学生**|
    |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

    >**注**:リモート デスクトップ セッションと **サーバー マネージャー** が読み込まれるまで待ちます。 サーバー マネージャーを閉じます。 

    >**注**:このラボの残りの手順は、**az500-10-vm1** Azure VM へのリモート デスクトップ セッション内で実行されます。 

6. **[スタート]** をクリックし、**[スタート]** メニューの **[Microsoft SQL Server ツール 18]** フォルダーを展開して、**[Micosoft SQL Server Management Studio]** メニュー項目をクリックします。

7. **[サーバーに接続]** ダイアログ ボックスで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |サーバー タイプ|**データベース エンジン**|
    |サーバー名|このタスクで前に指定したサーバー名|
    |認証|**SQL Server 認証**|
    |ログイン|**学生**|
    |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

8. **[サーバーに接続]** ダイアログ ボックスで、**[接続]** をクリックします。

9. **SQL Server Management Studio** コンソールの **[オブジェクト エクスプローラー]** ペインで、**[データベース]** フォルダーを展開します。

10. **[オブジェクト エクスプローラー]** ペインで、**medical** データベースを右クリックし、**[新しいクエリ]** をクリックします。

11. 次のコードをクエリ ウィンドウに貼り付け、**[実行]** をクリックします。 これにより、**Patients** テーブルが作成されます。

     ```sql
     CREATE TABLE [dbo].[Patients](
        [PatientId] [int] IDENTITY(1,1),
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NULL,
        [LastName] [nvarchar](50) NULL,
        [MiddleName] [nvarchar](50) NULL,
        [StreetAddress] [nvarchar](50) NULL,
        [City] [nvarchar](50) NULL,
        [ZipCode] [char](5) NULL,
        [State] [char](2) NULL,
        [BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
12. テーブルが正常に作成されたら、**[オブジェクト エクスプローラー]** ペインで、**medical** データベース ノード、**[テーブル]** ノードを展開します。**dbo.Patients** ノードを右クリックして **[列を暗号化する]** をクリックします。 

    >**注**:これにより、**Always Encrypted** ウィザードが起動します。

13. **[説明]** ページで **[次へ]** をクリックします。

14. **[列の選択]** ページで、**SSN** 列と **Birthdate** 列を選択し、**SSN** 列の **[暗号化の種類]** を **[決定論的]** に、**Birthdate** 列を **[ランダム化]** に設定し、**[次へ]** をクリックします。

    >**注**:暗号化の実行中に、**Rotary(Microsoft.SQLServer.Management.ServiceManagement)** に関連する "**呼び出しのターゲットが例外をスローしました**" のようなエラーがスローされる場合は、 **[ポリシーのローテーション操作]** の **[キーのアクセス許可]** の値が **[オフ]** であることを確認します。そうでない場合は、Azure portal で **[Key Vault]**  >>  **[アクセス ポリシー]**  >>  **[キーのアクセス許可]** に移動し、 **[ポリシーのローテーション操作]** ですべての値をオフにし、 **[特権キー操作]** で **[リリース]** をオフにします。

15. **[マスター キーの構成]** ページで **[Azure Key Vault]** を選択し、**[サインイン]** をクリックします。求められたら、このラボで行った Azure Key Vault インスタンスのプロビジョニングで使用したのと同じユーザー アカウントを使って認証します。**[Azure Key Vault の選択]** ドロップダウン リストにその Key Vault が表示されるのを確認し、**[次へ]** をクリックします。

16. **[実行設定]** ページで **[次へ]** をクリックします。
    
17. **[概要]** ページで、**[完了]** をクリックして暗号化を続行します。 要求されたら、このラボで Azure Key Vault インスタンスをプロビジョニングするために使用したのと同じユーザー アカウントを使用して、再度サインインします。

18. 暗号化プロセスが完了したら、**[結果]** ページで **[閉じる]** をクリックします。

19. **SQL Server Management Studio** コンソールで、**[オブジェクト エクスプローラー]** ペインの **medical** ノードの下にある **[セキュリティ]** および **[Always Encrypted のキー]** サブノードを展開します。 

    >**注**: **[Always Encrypted のキー]** サブノードには、**[列マスター キー]** と **[列暗号化キー]** サブフォルダーが含まれています。


### <a name="exercise-4-demonstrate-the-use-of-azure-key-vault-in-encrypting-the-azure-sql-database"></a>演習 4:Azure SQL データベースの暗号化における Azure Key Vault の使用方法を示す

この演習では、次のタスクを実行します。

- タスク 1:データ駆動型アプリケーションを実行して、Azure SQL データベースの暗号化における Azure Key Vault の使用法を示す

#### <a name="task-1-run-a-data-driven-application-to-demonstrate-the-use-of-azure-key-vault-in-encrypting-the-azure-sql-database"></a>タスク 1:データ駆動型アプリケーションを実行して、Azure SQL データベースの暗号化における Azure Key Vault の使用法を示す

Visual Studio を使用して暗号化列にデータを読み込むコンソール アプリケーションを作成し、Key Vault のキーにアクセスする接続文字列を使用してそのデータに安全にアクセスします。

1. RDP セッションから **az500-10-vm1** まで、**スタート メニュー**から **Visual Studio 2019** を起動します。

2. Visual Studio 2019 のウェルカム メッセージを表示するウィンドウに切り替え、**[サインイン]** ボタンをクリックし、プロンプトが表示されたら、このラボで使用している Azure サブスクリプションへの認証に使用した資格情報を入力します。

3. **[はじめに]** ページで、**[新しいプロジェクトの作成]** をクリックします。 

4. プロジェクト テンプレートの一覧で、**[コンソール アプリ (.NET Framework)]** を検索し、結果の一覧から **[C#]** の **[コンソール アプリ (.NET Framework)]** をクリックして **[次へ]** をクリックします。

5. **[新しいプロジェクトの構成]** ページで、次の設定を指定し (他の設定は既定値のままにします)、**[作成]** をクリックします。

    |設定|[値]|
    |---|---|
    |プロジェクト名|**OpsEncrypt**|
    |ソリューション名|**OpsEncrypt**|
    |フレームワーク|**.NET Framework 4.7.2**|

6. Visual Studio コンソールで、**[ツール]** メニューをクリックし、ドロップダウン メニューで **[NuGet パッケージ マネージャー]** をクリックし、カスケード メニューの **[パッケージ マネージャー コンソール]** をクリックします。

7. **[パッケージ マネージャー コンソール]** ペインで、次を実行して、最初に必要な **NuGet** パッケージをインストールします。

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. **[パッケージ マネージャー コンソール]** ペインで、次を実行して、2 番目に必要な **NuGet** パッケージをインストールします。

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
9. Azure 仮想マシンへの RDP セッションを最小限に抑え、 **\\Allfiles\\Labs\\10\\program.cs** に移動し、メモ帳で開き、その内容をクリップボードにコピーします。

10. RDP セッションに戻り、Visual Studio コンソールの **[ソリューション エクスプローラー]** ウィンドウで **[Program.cs]** をクリックし、その内容をクリップボードにコピーしたコードに置き換えます。

11. Visual Studio ウィンドウにある **Program.cs** ペインの 15 行目で、`<connection string noted earlier>` プレースホルダーを、このラボで前に記録した Azure SQL データベース **ADO.NET** の接続文字列に置き換えます。 接続文字列で、`{your_password}` プレースホルダーを `Pa55w.rd1234` に置き換えます。 ラボ コンピューターに文字列を保存した場合は、RDP セッションを終了して ADO 文字列をコピーしてから、Azure 仮想マシンに戻って貼り付ける必要がある場合があります。

12. Visual Studio ウィンドウにある **Program.cs** ペインの 16 行目で、`<client id noted earlier>` プレースホルダーを、このラボで前に記録した、登録済みアプリの**アプリケーション (クライアント) ID** の値に置き換えます。 

13. Visual Studio ウィンドウにある **Program.cs** ペインの 17 行目で、`<key value noted earlier>` プレースホルダーを、このラボで前に記録した、登録済みアプリの **Key1** の値に置き換えます。 

14. Visual Studio コンソールで、**[開始]** ボタンをクリックして、コンソール アプリケーションの作成を開始します。

15. アプリケーションはコマンド プロンプト ウィンドウを起動します。 パスワードの入力を求められたら、演習 1 のデプロイで指定したパスワードを入力して、Azure SQL Database に接続します。 

16. コンソール アプリを実行したまま、**SQL Management Studio** コンソールに切り替えます。 

17. **[オブジェクト エクスプローラー]** ペインで、**医療用データベース**を右クリックし、右クリック メニューで、 **[新しいクエリ]** をクリックします。

18. クエリ ウィンドウから次のクエリを実行して、コンソール アプリからデータベースに読み込まれたデータが暗号化されていることを確認します。

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. 有効な SSN の入力を求められるコンソール アプリに切り替えます。 これにより、データの暗号化された列がクエリされます。 コマンド プロンプトで、次のように入力し、Enter キーを押します。

    ```cmd
    999-99-0003
    ```

    >**注**:クエリによって返されたデータが暗号化されていないことを確認します。

20. コンソール アプリを終了するには、Enter キーを押します。

**リソースをクリーンアップする**

> 新規に作成し、使用しなくなったすべての Azure リソースを削除することを忘れないでください。 使用していないリソースを削除することで、予期しないコストが発生しなくなります。

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、 Cloud Shell を開きます。 

2. [Cloud Shell] ペインの左上のドロップダウン メニューで **[PowerShell]** を選択し、メッセージが表示されたら **[確認]** をクリックします。

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  **[Cloud Shell]** ペインを閉じます。 
