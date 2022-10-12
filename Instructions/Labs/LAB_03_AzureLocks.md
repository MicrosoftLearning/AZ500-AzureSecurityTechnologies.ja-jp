---
lab:
  title: 03 - Resource Manager ロック
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-03-resource-manager-locks"></a>ラボ 03:Resource Manager ロック
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ 

誤った削除や変更を防ぐために、リソース ロックの使用方法を示す概念実証を作成するように依頼されました。 具体的には、次の操作が必要です。

- 読み取り専用ロックを作成する

- 削除ロックを作成する

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 
 
## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Resource Manager ロック

## <a name="resource-manager-locks-diagram"></a>Resource Manager ロックの図

![image](https://user-images.githubusercontent.com/91347931/157514986-1bf6a9ea-4c7f-4487-bcd7-542648f8dc95.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-resource-manager-locks"></a>演習 1:Resource Manager ロック

#### <a name="estimated-timing-20-minutes"></a>推定時間:20 分

この演習では、次のタスクを実行します。

- タスク 1:ストレージ アカウントを使用してリソース グループを作成します。
- タスク 2:ストレージ アカウントに読み取り専用ロックを追加します。 
- タスク 3:読み取り専用ロックをテストします。 
- タスク 4:読み取り専用ロックを解除し、削除ロックを作成します。
- タスク 5:削除ロックをテストします。

#### <a name="task-1-create-a-resource-group-with-a-storage-account"></a>タスク 1:ストレージ アカウントを使用してリソース グループを作成します。

このタスクでは、ラボのリソース グループとストレージ アカウントを作成します。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

1. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** を選択します。

1. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

1. [Cloud Shell] ウィンドウ内の PowerShell セッションで、次のコマンドを実行してリソース グループを作成します (場所のパラメーターの値について講師に確認します)。

    ```powershell
    New-AzResourceGroup -Name AZ500LAB03 -Location 'EastUS'
    ```

1. [Cloud Shell] ウィンドウ内の PowerShell セッションで、次の手順を実行して、新しく作成したリソース グループにストレージ アカウントを作成します。
    
    ```powershell
    New-AzStorageAccount -ResourceGroupName AZ500LAB03 -Name (Get-Random -Maximum 999999999999999) -Location  EastUS -SkuName Standard_LRS -Kind StorageV2 
    ```

   >**注**:ストレージ アカウントが作成されるまで待ちます。 これには数分間かかることがあります。 

1. [Cloud Shell] ペインを閉じます。

#### <a name="task-2-add-a-readonly-lock-on-the-storage-account"></a>タスク 2:ストレージ アカウントに読み取り専用ロックを追加します。 

このタスクでは、読み取り専用ロックをストレージ アカウントに追加します。 リソースが誤って削除されたり変更されたりすることから保護します。 

1. Azure portal の、Azure portal ページの上部にある **[ソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**リソース グループ**」と入力し、**Enter** キーを押します。

1. **[リソース グループ]** ブレードで、**AZ500LAB03** リソース グループ エントリを選択します。

1. [**AZ500LAB03** リソース グループ] ブレードのリソースの一覧で、新しいストレージ アカウントを選択します。 

1. **[設定]** セクションで、"ロック" アイコンをクリックします。

1. **[+ 追加]** をクリックし、次の設定を指定します。

   |設定|値|
   |---|---|
   |ロック名|**読み取り専用ロック**|
   |ロックの種類|**読み取り専用**|

1. **[OK]** をクリックします。 

   >**注**:ストレージ アカウントは、誤って削除および変更されないように保護されるようになりました。

#### <a name="task-3-test-the-readonly-lock"></a>タスク 3:読み取り専用ロックをテストする 

1. ストレージ アカウント ブレードの **設定** セクションで、**構成** をクリックします。

1. **[セキュアな転送が必須]** オプションを **[無効]** に設定し、 **[保存]** をクリックします。

1. **ストレージ アカウントの更新に失敗した**ことを示す通知を見つけることができます。

1. Azure portal の上部にあるツール バーの **[通知]** アイコンをクリックし、次のテキストのような通知を確認します。 

    > **"ストレージ アカウント 'xxxxxxxx' を更新できませんでした。Error:次のスコープがロックされているため、スコープ 'xxxxxxxx' は書き込み操作を実行できません。'/subscriptions/xxxxx-xxx-xxxx-xxxx-xxxxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'。ロックを解除して、もう一度やり直してください"**

1. ストレージ アカウントの **[構成]** ブレードに戻り、 **[破棄]** をクリックします。 

1. ストレージ アカウント ブレードで **概要** を選択し、**概要** ブレードで **削除** をクリックします。

1. **ストレージ アカウントの削除** ブレードで、ストレージ アカウントの名前を入力して、続行することを確認し、**削除** をクリックします。

1. 次のテキストのように、新しく生成された通知を確認します。 

    > **"ストレージ アカウント 'xxxxxxx' を削除できませんでした。Error:次のスコープがロックされているため、スコープ 'xxxxxxx' は削除操作を実行できません。'/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'。ロックを解除して、もう一度やり直してください。"**

   >**注**:読み取り専用ロックによってリソースの誤った削除と変更が停止することを確認しました。

#### <a name="task-4-remove-the-readonly-lock-and-create-a-delete-lock"></a>タスク 4:読み取り専用ロックを解除し、削除ロックを作成します。

このタスクでは、ストレージ アカウントから読み取り専用ロックを削除し、削除ロックを作成します。 

1. Azure portal に、新しく作成したストレージ アカウントのプロパティを表示しているブレードに移動します。

1. **[設定]** セクションで、 **[ロック]** を選択します。  

1. **[ロック]** ブレードで、 **[読み取り専用ロック]** エントリの右端にある **[削除]** アイコンをクリックします。

1. **[+ 追加]** をクリックし、次の設定を指定します。

   |設定|値|
   |---|---|
   |ロック名|**ロックの削除**|
   |ロックの種類|**削除**|

1. **[OK]** をクリックします。 

#### <a name="task-5-test-the-delete-lock"></a>タスク 5:削除ロックをテストします。

このタスクでは削除ロックをテストします。 ストレージ アカウントは変更できますが、削除することはできません。 

1. ストレージ アカウント ブレードの **設定** セクションで、**構成** をクリックします。

1. **[セキュアな転送が必須]** オプションを **[無効]** に設定し、 **[保存]** をクリックします。

   >**注**:今回は、変更が成功するはずです。

1. ストレージ アカウント ブレードで **概要** を選択し、**概要** ブレードで **削除** をクリックします。

1. **ストレージ アカウントの削除** ブレードで、ストレージ アカウントの名前を入力して、続行することを確認し、**削除** をクリックします。

1. 次のテキストに似た通知を確認します。 

    > **'xxxxxx' は、このリソースまたはその親に削除ロックがあるため、削除できません。このリソースを削除する前に、ロックを削除する必要があります"**

   >**注**:**削除**ロックによって構成の変更が許可され、誤って削除されないことが確認されました。

   >**注**:リソース ロックを使用することにより、最も重要なリソースの偶発的または悪意のある変更や削除に対する追加の防御ラインを実装できます。 **所有者**ロールを持つユーザーは、リソース ロックを削除できますが、その場合は意識的な作業が必要です。 ロックはロール ベースの Access Control を補足します。 

> 結果:この演習では、Resource Manager のロックを使用して、リソースを変更や誤って削除しないようにする方法について学習しました。

**リソースをクリーンアップする**

> 新規に作成し、使用しなくなったすべての Azure リソースを削除することを忘れないでください。 使用していないリソースを削除することで、予期しないコストが発生しなくなります。

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、 **[再接続]** をクリックします。

1. [Cloud Shell] ウィンドウ内の PowerShell セッションで、次の操作を実行して削除ロックを削除します。

    ```powershell
    $storageaccountname = (Get-AzStorageAccount -ResourceGroupName AZ500LAB03).StorageAccountName

    $lockName = (Get-AzResourceLock -ResourceGroupName AZ500LAB03 -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).Name

    Remove-AzResourceLock -LockName $lockName -ResourceName $storageAccountName  -ResourceGroupName AZ500LAB03 -ResourceType Microsoft.Storage/storageAccounts -Force
    ```

1.  [Cloud Shell] ウィンドウ内の PowerShell セッションで、次の手順を実行してリソース グループを削除します。

    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB03" -Force -AsJob
    ```

1.  **[Cloud Shell]** ペインを閉じます。 
