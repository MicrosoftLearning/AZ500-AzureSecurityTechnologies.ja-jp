---
lab:
  title: 01 - ロールベースのアクセス制御
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: 9ddbfd416d9897bcdf891c9c9265d8205277c176
ms.sourcegitcommit: 79ca7b110859fe71a3849a28fdc781cad95d1567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2022
ms.locfileid: "146381342"
---
# <a name="lab-01-role-based-access-control"></a>ラボ 01:ロールベースのアクセス制御
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Azure ユーザーとグループの作成方法を示す概念実証を作成するように依頼されました。 また、ロールベースのアクセス制御を使用してグループにロールを割り当てる方法も説明します。 具体的には、次の操作が必要です。

- ジョセフ・プライスのユーザー アカウントをメンバーとして含むシニア管理者グループを作成します。
- メンバーとして、イザベル・ガルシアのユーザー アカウントを含むジュニア管理者グループを作成します。
- ディラン・ウィリアムズのユーザー アカウントをメンバーとして含むサービスデスクグループを作成します。
- 仮想マシン共同作成者ロールをサービス デスク グループに割り当てます。 

> このラボのすべてのリソースについて、**米国東部** リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:ユーザー アカウントの Joseph Price をメンバーとして (Azure portal)、上級管理者グループを作成します。 
- 演習 2:ユーザー アカウントのメンバー (PowerShell) として、そのユーザー アカウントの管理者グループを作成します。
- 演習 3:ユーザーのディラン・ウィリアムズをメンバーにしてサービス デスク グループを作成します (Azure CLI)。 
- 演習 4:仮想マシン共同作成者ロールをサービス デスク グループに割り当てます。

## <a name="role-based-access-control-architecture-diagram"></a>ロールベースのアクセス制御アーキテクチャの図

![image](https://user-images.githubusercontent.com/91347931/157751243-5aa6e521-9bc1-40af-839b-4fd9927479d7.png)

## <a name="instructions"></a>手順

### <a name="exercise-1-create-the-senior-admins-group-with-the-user-account-joseph-price-as-its-member"></a>演習 1:ユーザー アカウント Joseph Price をメンバーとして使用して、上級管理者グループを作成します。 

#### <a name="estimated-timing-10-minutes"></a>推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:Azure portal を使用して、Joseph Price のユーザー アカウントを作成します。
- タスク 2:Azure portal を使用してシニア管理者グループを作成し、Joseph Price のユーザー アカウントをグループに追加します。

#### <a name="task-1-use-the-azure-portal-to-create-a-user-account-for-joseph-price"></a>タスク 1:Azure portal を使用して、Joseph Price のユーザー アカウントを作成する 

このタスクでは、Joseph Price のユーザー アカウントを作成します。 

1. ブラウザー セッションを開始し、Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションの所有者または共同作成者のロールと、そのサブスクリプションに関連付けられている Azure AD テナントのグローバル管理者の役割を持つアカウントを使用して、Azure portal にサインインします。

2. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**Azure Active Directory**」と入力し、**Enter** キーを押します。

3. Azure Active Directory テナントの **[概要]** ブレードの **[管理]** セクションで、**[ユーザー]** を選択し、次に **[+ 新しいユーザー]** を選択します。

4. **[新しいユーザー]** ブレードで、**[ユーザーの作成]** オプションが選択されていることを確認し、次の設定を指定します。

   |設定|値|
   |---|---|
   |ユーザー名|**Joseph**|
   |名前|**Joseph Price**|

5. **[ユーザー名]** の横にあるコピー アイコンをクリックして、完全なユーザーをコピーします。

6. パスワードの **[自動生成]** が選択されていることを確認し、**[パスワードを表示]** チェックボックスを選択して、自動生成されたパスワードを特定します。 このパスワードは、ユーザー名と共に Joseph に提供する必要があります。 

7. **Create** をクリックしてください。

8. **[ユーザー \| すべてのユーザー]** ブレードを更新し、新しいユーザーが Azure AD テナントに作成されたことを確認します。

#### <a name="task2-use-the-azure-portal-to-create-a-senior-admins-group-and-add-the-user-account-of-joseph-price-to-the-group"></a>タスク 2:Azure portal を使用してシニア管理者グループを作成し、Joseph Price のユーザー アカウントをグループに追加します。

このタスクでは、*上級管理者* グループを作成し、Joseph Price のユーザー アカウントをグループに追加して、グループ所有者として構成します。

1. Azure portal で、Azure Active Directory テナントを表示しているブレードに戻ります。 

2. **[管理]** セクションで、**[グループ]** をクリックし、**[+ 新しいグループ]** を選択します。
 
3. **[新しいグループ]** ブレードで、次の設定を指定します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |グループの種類|**Security**|
   |グループ名|**上級管理者**|
   |メンバーシップの種類|**割り当て済み**|
    
4. **[所有者が選択されていません]** リンクをクリックし、**[所有者を追加]** ブレードで **[Joseph Price]** を選択して、**[選択]** をクリックします。

5. **[メンバーが選択されていません]** リンクをクリックし、**[メンバーを追加]** ブレードで、**[Joseph Price]** を選択して、**[選択]** をクリックします。

6. **[新しいグループ]** ブレードに戻り、**[作成]** をクリックします。

> 結果:Azure portal を使用してユーザーとグループを作成し、ユーザーをグループに割り当てました。 

### <a name="exercise-2-create-a-junior-admins-group-containing-the-user-account-of-isabel-garcia-as-its-member"></a>演習 2:メンバーとして、イザベル・ガルシアのユーザー アカウントを含むジュニア管理者グループを作成します。

#### <a name="estimated-timing-10-minutes"></a>推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:PowerShell を使用して、Isabel Garcia のユーザー アカウントを作成します。
- タスク 2:PowerShell を使用してジュニア管理者グループを作成し、Isabel Garcia のユーザー アカウントをグループに追加します。 

#### <a name="task-1-use-powershell-to-create-a-user-account-for-isabel-garcia"></a>タスク 1:PowerShell を使用して、Isabel Garcia のユーザー アカウントを作成します。

このタスクでは、PowerShell を使用して Isabel Garcia のユーザー アカウントを作成します。

1. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** を選択します。

2. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

   >**注**: コピーしたテキストを Cloud Shell に貼り付けるには、ペイン ウィンドウ内で右クリックして、**[貼り付け]** を選択します。 または、**Shift + Insert** キーの組み合わせを使用できます。

3. Cloud Shell ペイン内の PowerShell セッションで、次を実行してパスワード プロファイル オブジェクトを作成します。

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

4. Cloud Shell ペイン内の PowerShell セッションで次を実行して、プロファイル オブジェクト内のパスワードの値を設定します。
    ```powershell
    $passwordProfile.Password = "Pa55w.rd1234"
    ```

5. Cloud Shell ペイン内の PowerShell セッションで、次のコマンドを実行して Azure Active Directory に接続します。

    ```powershell
    Connect-AzureAD
    ```
      
6. Cloud Shell ペイン内の PowerShell セッションで次を実行して、Azure AD テナントの名前を識別します。 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

7. Cloud Shell ペイン内の PowerShell セッションで次を実行して、Isabel Garcia のユーザー アカウントを作成します。 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

8. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、Azure AD ユーザーを一覧表示します (Joseph と Isabel のアカウントが一覧に表示されます)。 

    ```powershell
    Get-AzureADUser 
    ```

#### <a name="task2-use-powershell-to-create-the-junior-admins-group-and-add-the-user-account-of-isabel-garcia-to-the-group"></a>タスク 2:PowerShell を使用してジュニア管理者グループを作成し、Isabel Garcia のユーザー アカウントをグループに追加します。

このタスクでは、ジュニア管理者グループを作成し、PowerShell を使用してグループに Isabel Garcia のユーザー アカウントを追加します。

1. Cloud Shell ペイン内の同じ PowerShell セッションで次を実行して、ジュニア管理者という名前の新しいセキュリティ グループを作成します。
    
    ```powershell
    New-AzureADGroup -DisplayName 'Junior Admins' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
    ```

2. Cloud Shell ペイン内の PowerShell セッションで、次のコマンドを実行して、Azure AD テナント内のグループを一覧表示します (一覧には、上級管理者グループとジュニア管理者グループが含まれている必要があります)。

    ```powershell
    Get-AzureADGroup
    ```

3. Cloud Shell ペイン内の PowerShell セッションで次を実行して、Isabel Garcia のユーザー アカウントへの参照を取得します。

    ```powershell
    $user = Get-AzureADUser -Filter "MailNickName eq 'Isabel'"
    ```

4. Cloud Shell ペイン内の PowerShell セッションで、次のコマンドを実行して、Isabel のユーザー アカウントをジュニア管理者グループに追加します。
    
    ```powershell
    Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```

5. Cloud Shell ペイン内の PowerShell セッションで次を実行して、ジュニア管理者グループに Isabel のユーザー アカウントが含まれていることを確認します。

    ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins"
    ```

> 結果:PowerShell を使用してユーザーとグループ アカウントを作成し、ユーザー アカウントをグループ アカウントに追加しました。 


### <a name="exercise-3-create-a-service-desk-group-containing-the-user-account-of-dylan-williams-as-its-member"></a>演習 3:ディラン・ウィリアムズのユーザー アカウントをメンバーとして含むサービスデスクグループを作成します。

#### <a name="estimated-timing-10-minutes"></a>推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:Azure CLI を使用して、Dylan Williams のユーザー アカウントを作成します。
- タスク 2:Azure CLI を使用してサービス デスク グループを作成し、グループに Dylan のユーザー アカウントを追加します。 

#### <a name="task-1-use-azure-cli-to-create-a-user-account-for-dylan-williams"></a>タスク 1:Azure CLI を使用して、Dylan Williams のユーザー アカウントを作成します。

このタスクでは、Dylan Williams のユーザー アカウントを作成します。

1. Cloud Shell ペインの左上隅にあるドロップダウン メニューで、**[Bash]** を選択し、プロンプトが表示されたら、**[確認]** をクリックします。 

2. Cloud Shell ペイン内の Bash セッションで次を実行して、Azure AD テナントの名前を確認します。

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. Cloud Shell ペイン内の Bash セッションで次を実行して、ユーザーの Dylan Williams を作成します。 *yourdomain* を使用します。
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. Cloud Shell ペイン内の Bash セッションで、次を実行して Azure AD ユーザー アカウントを一覧表示します (一覧には、Joseph、Isabel、および Dylan のユーザー アカウントが含まれます)
    
    ```cli
    az ad user list --output table
    ```

#### <a name="task-2-use-azure-cli-to-create-the-service-desk-group-and-add-the-user-account-of-dylan-to-the-group"></a>タスク 2:Azure CLI を使用してサービス デスク グループを作成し、グループに Dylan のユーザー アカウントを追加します。 

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


### <a name="exercise-4-assign-the-virtual-machine-contributor-role-to-the-service-desk-group"></a>演習 4:仮想マシン共同作成者ロールをサービス デスク グループに割り当てます。

#### <a name="estimated-timing-10-minutes"></a>推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:リソース グループを作成します。 
- タスク 2:リソース グループに Service Desk Virtual Machine Contributor のアクセス許可を割り当てます。  

#### <a name="task-1-create-a-resource-group"></a>タスク 1: リソース グループを作成する

1. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**リソース グループ**」と入力し、**Enter** キーを押します。

2. **[リソース グループ]** ブレードで、**[+ 新規]** をクリックして、次の設定を指定します。

   |設定|値|
   |---|---|
   |サブスクリプション名|お使いの Azure サブスクリプションの名前|
   |リソース グループ名|**AZ500Lab01**|
   |Location|**米国東部**|

3. **[確認および作成]** 、 **[作成]** の順にクリックします。

   >**注**:リソース グループがデプロイされるまで待ちます。 **通知** アイコン (右上) を使用して、デプロイの状態の進行状況を追跡します。

4. **[リソース グループ]** ブレードに戻り、ページを更新して、新しいリソース グループがリソース グループの一覧に表示されることを確認します。


#### <a name="task-2-assign-the-service-desk-virtual-machine-contributor-permissions"></a>タスク 2:Service Desk Virtual Machine Contributor のアクセス許可を割り当てます。 

1. **[リソース グループ]** ブレードで、**AZ500LAB01** リソース グループのエントリをクリックします。

2. **[AZ500Lab01]** ブレードの中央のペインで、 **[アクセス制御 (IAM)]** をクリックします。

3. **[AZ500Lab01 \| アクセス制御 (IAM)]** ブレードで、 **[+ 追加]** をクリックし、ドロップダウン メニューで **[ロール割り当ての追加]** をクリックします。

4. **[ロール割り当ての追加]** ブレードで、次の設定を指定し、各手順の後に **[次へ]** をクリックします。

   |設定|値|
   |---|---|
   |検索タブのロール|**Virtual Machine Contributor**|
   |アクセスの割り当て先 ([メンバー] ペインの下)|**ユーザー、グループ、またはサービス プリンシパル**|
   |選択 ([+ メンバーの選択])|**サービス デスク**|

5. **[レビューと割り当て]** を 2 回クリックして、ロールの割り当てを作成します。

6. **[Access Control (IAM)]** ブレードで、**[ロールの割り当て]** を選択します。

7. **[AZ500Lab01 \| アクセス制御 (IAM)]** ブレードの **[アクセス権の確認]** タブで、**[名前またはメール アドレスで検索]** テキスト ボックスに「**Dylan Williams**」と入力します。

8. 検索結果の一覧で、Dylan Williams のユーザー アカウントを選択し、**[Dylan Williams の割り当て - AZ500Lab01]** ブレードで、新しく作成された割り当てを表示します。

9. **[Dylan Williams の割り当て - AZ500Lab01]** ブレードを閉じます。

10. 最後の 2 つの同じ手順を繰り返して、**Joseph Price** のアクセス権を確認します。 

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
