---
lab:
  title: 01 - ロールベースのアクセス制御
  module: Module 01 - Manage Identity and Access
---


# ラボ 01:ロールベースのアクセス制御
# 受講生用ラボ マニュアル

## ラボのシナリオ

Azure ユーザーとグループの作成方法を示す概念実証を作成するように依頼されました。 また、ロールベースのアクセス制御を使用してグループにロールを割り当てる方法も説明します。 具体的には、次の操作が必要です。

- ジョセフ・プライスのユーザー アカウントをメンバーとして含むシニア管理者グループを作成します。
- メンバーとして、イザベル・ガルシアのユーザー アカウントを含むジュニア管理者グループを作成します。
- ディラン・ウィリアムズのユーザー アカウントをメンバーとして含むサービスデスクグループを作成します。
- 仮想マシン共同作成者ロールをサービス デスク グループに割り当てます。 

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1:ユーザー アカウントの Joseph Price をメンバーとして (Azure portal)、上級管理者グループを作成します。 
- 演習 2:ユーザー アカウントのメンバー (PowerShell) として、そのユーザー アカウントの管理者グループを作成します。
- 演習 3:ユーザーのディラン・ウィリアムズをメンバーにしてサービス デスク グループを作成します (Azure CLI)。 
- 演習 4:仮想マシン共同作成者ロールをサービス デスク グループに割り当てます。

## ロールベースのアクセス制御アーキテクチャの図

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/506cde9c-5242-4438-a793-f88a5434a2b2)

## 手順

### 演習 1:ユーザー アカウント Joseph Price をメンバーとして使用して、上級管理者グループを作成します。 

#### 推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:Azure portal を使用して、Joseph Price のユーザー アカウントを作成します。
- タスク 2:Azure portal を使用してシニア管理者グループを作成し、Joseph Price のユーザー アカウントをグループに追加します。

#### タスク 1:Azure portal を使用して、Joseph Price のユーザー アカウントを作成する 

このタスクでは、Joseph Price のユーザー アカウントを作成します。 

1. ブラウザー セッションを開始し、Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**: このラボで使用する Azure サブスクリプションの所有者または共同作成者のロールと、そのサブスクリプションに関連付けられている Microsoft Entra テナントのグローバル管理者のロールを持つアカウントを使用して、Azure portal にサインインします。

2. Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**Microsoft Entra ID**」と入力し、**Enter** キーを押します。

3. Microsoft Entra ID テナントの **[概要]** ブレードの **[管理]** セクションで、**[ユーザー]** を選択し、次に **[+ 新しいユーザー]** を選択します。

4. **[新しいユーザー]** ブレードで、**[ユーザーの作成]** オプションが選択されていることを確認し、次の設定を指定します。

   |設定|値|
   |---|---|
   |ユーザー名|**Joseph**|
   |名前|**Joseph Price**|

5. **[ユーザー名]** の横にあるコピー アイコンをクリックして、完全なユーザーをコピーします。

6. パスワードの **[自動生成]** が選択されていることを確認し、**[パスワードを表示]** チェックボックスを選択して、自動生成されたパスワードを特定します。 このパスワードは、ユーザー名と共に Joseph に提供する必要があります。 

7. **Create** をクリックしてください。

8. **[ユーザー \| すべてのユーザー]** ブレードを更新し、新しいユーザーが Azure AD テナントに作成されたことを確認します。

#### タスク 2:Azure portal を使用してシニア管理者グループを作成し、Joseph Price のユーザー アカウントをグループに追加します。

このタスクでは、*Senior Admins*グループを作成し、Joseph Price のユーザー アカウントをグループに追加して、グループ所有者として構成します。

1. Azure portal で、Microsoft Entra ID テナントを表示しているブレードに戻ります。 

2. **[管理]** セクションで、**[グループ]** をクリックし、**[+ 新しいグループ]** を選択します。
 
3. **[新しいグループ]** ブレードで、次の設定を指定します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |グループの種類|**Security**|
   |グループ名|**Senior Admins**|
   |メンバーシップの種類|**割り当て済み**|
    
4. **[所有者が選択されていません]** リンクをクリックし、**[所有者を追加]** ブレードで **[Joseph Price]** を選択して、**[選択]** をクリックします。

5. **[メンバーが選択されていません]** リンクをクリックし、**[メンバーを追加]** ブレードで、**[Joseph Price]** を選択して、**[選択]** をクリックします。

6. **[新しいグループ]** ブレードに戻り、**[作成]** をクリックします。

> 結果:Azure portal を使用してユーザーとグループを作成し、ユーザーをグループに割り当てました。 

### 演習 2:メンバーとして、イザベル・ガルシアのユーザー アカウントを含むジュニア管理者グループを作成します。

#### 推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:PowerShell を使用して、Isabel Garcia のユーザー アカウントを作成します。
- タスク 2:PowerShell を使用してジュニア管理者グループを作成し、Isabel Garcia のユーザー アカウントをグループに追加します。 

#### タスク 1:PowerShell を使用して、Isabel Garcia のユーザー アカウントを作成します。

このタスクでは、PowerShell を使用して Isabel Garcia のユーザー アカウントを作成します。

1. Azure portal の右上隅にある **Cloud Shell アイコン**をクリックして、**Cloud Shell を開きます**。

2. **メッセージが表示されたら、ストレージ アカウントを作成して Cloud Shell を設定します**。 これは、Cloud Shell を**初めて起動する場合にのみ**必要です。

3. Cloud Shell 画面の左上隅にあるドロップダウン メニューから **[PowerShell] が選択されていることを確認します**。

   >**注**: コピーしたテキストを Cloud Shell に貼り付けるには、ペイン ウィンドウ内で右クリックして、**[貼り付け]** を選択します。 または、**Shift + Insert** キーの組み合わせを使用できます。

4. Cloud Shell ペイン内の PowerShell セッションで、次を実行してパスワード プロファイル オブジェクトを作成します。

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

5. Cloud Shell ペイン内の PowerShell セッションで次を実行して、プロファイル オブジェクト内のパスワードの値を設定します。
    ```powershell
    $passwordProfile.Password = "Pa55w.rd1234"
    ```

6. Cloud Shell ペイン内の PowerShell セッションで、次を実行して Microsoft Entra ID に接続します。

    ```powershell
    Connect-AzureAD
    ```
      
7. Cloud Shell ペイン内の PowerShell セッションで次を実行して、Microsoft Entra テナントの名前を識別します。 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

8. Cloud Shell ペイン内の PowerShell セッションで次を実行して、Isabel Garcia のユーザー アカウントを作成します。 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

9. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、Microsoft Entra ID ユーザーを一覧表示します (Joseph と Isabel のアカウントが一覧に表示されます)。 

    ```powershell
    Get-AzureADUser -All $true | Where-Object {$_.UserPrincipalName -like "*43846135@LOD*"} 
    ```

#### タスク 2:PowerShell を使用してジュニア管理者グループを作成し、Isabel Garcia のユーザー アカウントをグループに追加します。

このタスクでは、ジュニア管理者グループを作成し、PowerShell を使用してグループに Isabel Garcia のユーザー アカウントを追加します。

1. Cloud Shell ペイン内の同じ PowerShell セッションで次を実行して、Junior Admins という名前の**新しいセキュリティ グループを作成**します。
   
   ```powershell
   New-AzureADGroup -DisplayName 'Junior Admins43846135' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
   ```
   
2. Cloud Shell ペイン内の PowerShell セッションで、次のコマンドを実行して、Microsoft Entra テナント内の**グループを一覧表示**します (一覧には、Senior Admins グループと Junior Admins グループが表示されます)。
   
   ```powershell
   Get-AzureADGroup
   ```

3. Cloud Shell ペイン内の PowerShell セッションで次を実行して、Isabel Garcia のユーザー アカウントへの**参照を取得**します。

   ```powershell
   $user = Get-AzureADUser -Filter "UserPrincipalName eq 'Isabel-43846135@LODSPRODMCA.onmicrosoft.com'"
   ```

4. Cloud Shell 画面内の PowerShell セッションで、次のコマンドを実行して、Junior Admins43846135 グループに Isabel のユーザー アカウントを追加します。
   ```powershell
   Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins43846135"
   ```

5. Cloud Shell 画面内の PowerShell セッションで、次を実行して、Junior Admins43846135 グループに Isabel のユーザー アカウントが含まれていることを確認します。
   
   ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins43846135"
    ```
   
> 結果:PowerShell を使用してユーザーとグループ アカウントを作成し、ユーザー アカウントをグループ アカウントに追加しました。 

### 演習 3:Dylan Williams のユーザー アカウントをメンバーとして含むサービスデスクグループを作成します。

#### 推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:Azure CLI を使用して、Dylan Williams のユーザー アカウントを作成します。
- タスク 2:Azure CLI を使用してサービス デスク グループを作成し、グループに Dylan のユーザー アカウントを追加します。 

#### タスク 1:Azure CLI を使用して、Dylan Williams のユーザー アカウントを作成します。

このタスクでは、Dylan Williams のユーザー アカウントを作成します。

1. Cloud Shell ペインの左上隅にあるドロップダウン メニューで、**[Bash]** を選択し、プロンプトが表示されたら、**[確認]** をクリックします。 

2. Cloud Shell ペイン内の Bash セッションで次を実行して、Microsoft Entra テナントの名前を特定します。

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. Cloud Shell ペイン内の Bash セッションで次を実行して、ユーザーの Dylan Williams を作成します。 *yourdomain* を使用します。
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. Cloud Shell ペイン内の Bash セッションで、次を実行して  Microsoft Entra ID ユーザー アカウントを一覧表示します (一覧には、Joseph、Isabel、および Dylan のユーザー アカウントが含まれます)
    
    ```cli
    az ad user list --output table
    ```

#### タスク 2:Azure CLI を使用してサービス デスク グループを作成し、グループに Dylan のユーザー アカウントを追加します。 

このタスクでは、サービス デスク グループを作成し、グループに Dylan を割り当てます。 

1. Cloud Shell ペイン内の同じ Bash セッションで、次の手順を実行して、サービス デスクという名前の新しいセキュリティ グループを作成します。

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
2. Cloud Shell ペイン内の Bash セッションで次を実行して、Azure AD グループを一覧表示します (一覧にはサービス デスク、シニア管理者、およびジュニア管理者グループが含まれます)。

    ```cli
    az ad group list -o table
    ```

3. Cloud Shell ペイン内の Bash セッションで次を実行して、Dylan Williams のユーザー アカウントの参照を取得します。 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

4. Cloud Shell ペイン内の Bash セッションで次の手順を実行して、Dylan Williams のユーザー アカウントの objectId プロパティを取得します。 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].id' | tr -d '"')
    ```

5. Cloud Shell ペイン内の Bash セッションで次の手順を実行して、Dylan のユーザー アカウントをサービス デスク グループに追加します。 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

6. Cloud Shell ペイン内の Bash セッションで次のコマンドを実行して、サービス デスク グループのメンバーを一覧表示し、Dylan のユーザー アカウントが含まれていることを確認します。

    ```cli
    az ad group member list --group "Service Desk"
    ```

7. [Cloud Shell] ペインを閉じます。

> 結果:Azure CLI を使用して、ユーザー アカウントとグループ アカウントを作成し、そのユーザー アカウントをグループに追加しました。 


### 演習 4:仮想マシン共同作成者ロールをサービス デスク グループに割り当てます。

#### 推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:リソース グループを作成します。 
- タスク 2:リソース グループに Service Desk Virtual Machine Contributor のアクセス許可を割り当てます。  

#### タスク 1: リソース グループを作成する

1. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**リソース グループ**」と入力し、**Enter** キーを押します。

2. **[リソース グループ]** ブレードで、**[+ 新規]** をクリックして、次の設定を指定します。

   |設定|値|
   |---|---|
   |サブスクリプション名|お使いの Azure サブスクリプションの名前|
   |リソース グループ名|**AZ500Lab01**|
   |Location|**米国東部**|

3. **[確認および作成]** 、 **[作成]** の順にクリックします。

   >**注**:リソース グループがデプロイされるまで待ちます。 **通知**アイコン (右上) を使用して、デプロイの状態の進行状況を追跡します。

4. **[リソース グループ]** ブレードに戻り、ページを更新して、新しいリソース グループがリソース グループの一覧に表示されることを確認します。


#### タスク 2:Service Desk Virtual Machine Contributor のアクセス許可を割り当てます。 

1. **[リソース グループ]** ブレードで、**AZ500LAB01** リソース グループのエントリをクリックします。

2. **[AZ500Lab01]** ブレードの中央のペインで、 **[アクセス制御 (IAM)]** をクリックします。

3. **[AZ500Lab01 \| アクセス制御 (IAM)]** ブレードで、 **[+ 追加]** をクリックし、ドロップダウン メニューで **[ロール割り当ての追加]** をクリックします。

4. **[ロール割り当ての追加]** ブレードで、次のそれぞれの設定を完了してから、[次へ] をクリックします。

   **注:** すべての手順を完了したら、**[次へ]** をクリックします。

   |設定|値|
   |---|---|
   |検索タブのロール|**Virtual Machine Contributor**|
   |アクセスの割り当て先 ([メンバー] ペインの下)|**ユーザー、グループ、またはサービス プリンシパル**|
   |選択 ([+ メンバーの選択])|**サービス デスク**|

6. **[レビューと割り当て]** を 2 回クリックして、ロールの割り当てを作成します。

7. **[Access Control (IAM)]** ブレードで、**[ロールの割り当て]** を選択します。

8. **[AZ500Lab01 \| アクセス制御 (IAM)]** ブレードの **[アクセス権の確認]** タブで、**[名前またはメール アドレスで検索]** テキスト ボックスに「**Dylan Williams**」と入力します。

9. 検索結果の一覧で、Dylan Williams のユーザー アカウントを選択し、**[Dylan Williams の割り当て - AZ500Lab01]** ブレードで、新しく作成された割り当てを表示します。

10. **[Dylan Williams の割り当て - AZ500Lab01]** ブレードを閉じます。

11. 最後の 2 つの同じ手順を繰り返して、**Joseph Price** のアクセス権を確認します。 

> 結果:RBAC 権限を割り当てて確認しました。 

**リソースをクリーンアップする**

> 新規に作成し、使用しなくなったすべての Azure リソースを削除することを忘れないでください。 使用していないリソースを削除することで、予期しないコストが発生しなくなります。

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、Cloud Shell を開きます。 

2. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** を選択し、メッセージが表示されたら **[確認]** をクリックします。 

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

4.  **[Cloud Shell]** ペインを閉じます。 
