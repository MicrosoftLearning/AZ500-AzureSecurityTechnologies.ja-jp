---
lab:
  title: 02 - Azure Policy
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-02-azure-policy"></a>ラボ 02:Azure Policy
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to create a proof of concept showing how Azure policy can be used. Specifically, you need to:

- 特定のリージョンでのみリソースを作成を許可する場所ポリシーを作成します。
- リソースが許可された場所にのみ作成されることを確認するテスト

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次に挙げる演習を完了させます。

- 演習 1:Azure Policy を実装します。 

## <a name="azure-policy-diagram"></a>Azure Policy の図

![image](https://user-images.githubusercontent.com/91347931/157511920-19c1f06c-86bd-440d-80ac-d96aa27aefff.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-implement-azure-policy"></a>演習 1:Azure Policy の実装

#### <a name="estimated-timing-20-minutes"></a>推定時間:20 分

この演習では、次のタスクを実行します。

- タスク 1:Azure リソース グループを作成する。 
- タスク 2:許可されている場所ポリシーの割り当てを作成する。
- タスク 3:許可されている場所ポリシーの割り当てが機能していることを確認します。 

#### <a name="task-1-create-a-resource-group-for-the-lab"></a>タスク 1:ラボ用のリソース グループを作成する。 

このタスクでは、このラボ用のリソース グループを作成します。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

1. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

1. [Cloud Shell] ウィンドウ内の PowerShell セッションで、次のコマンドを実行してリソース グループを作成します (場所のパラメーターの値について講師に確認します)。

    ```powershell
    New-AzResourceGroup -Name AZ500LAB02 -Location 'East US'
    ```

1. [Cloud Shell] ウィンドウ内の PowerShell セッションで、次の手順を実行してリソース グループを一覧表示し、新しいリソース グループが作成されたことを確認します。

    ```powershell
    Get-AzResourceGroup | format-table
    ```

1. **Cloud Shell**を閉じます。

#### <a name="task-2-create-an-allowed-locations-policy-assignment"></a>タスク 2:許可されている場所ポリシーの割り当てを作成する。

このタスクでは、許可されている場所ポリシーの割り当てを作成し、ポリシーを使用できる Azure リージョンを指定します。 

1. Azure portal で、ページ上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスに「**ポリシー**」と入力し、**Enter** キーを押します。

1. **[ポリシー]** ブレードの **[作成]** セクションで、 **[定義]** を選択します。

1. Take a minute to browse the built-in definitions. Use the <bpt id="p1">**</bpt>Category<ept id="p1">**</ept> drop-down to filter the list of policies.

1. **[検索]** テキスト ボックスに「**許可されている場所**」と入力します。 

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The <bpt id="p2">**</bpt>Allowed locations<ept id="p2">**</ept> policy allows you to restrict location of resources, not resource groups. To restrict locations of resource groups, you can use the <bpt id="p1">**</bpt>Allowed locations for resource groups<ept id="p1">**</ept> policy.

1.  **[許可されている場所]**   ポリシー定義をクリックして、その詳細を表示します。 

   >Azure Policy の使用方法を示す概念実証を作成するように依頼されました。

1. **[許可されている場所]** ブレードで、 **[割り当て]** をクリックします。

1. **[許可されている場所]** ブレードの **[基本]** タブで 、 **[スコープ]** テキスト ボックスの横にある省略記号 (...) ボタンをクリックし、[**スコープ]** ブレードで次の設定を指定します。

   |設定|[値]|
   |---|---|
   |サブスクリプション|Azure サブスクリプションの名前|
   |Resource group|**AZ500LAB02**|

1. **[選択]** をクリックします。

1. **[許可されている場所]** ブレードの **[基本]** タブで、次の設定を指定します (その他の設定は既定値のままにします)。

   |設定|[値]|
   |---|---|
   |割り当て名|**AZ500LAB02 に英国南部を許可**|
   |説明|**AZ500LAB02 では英国南部のみでリソースを作成できるようにする**|
   |ポリシーの適用|**有効**|

1.  **[次へ]** をクリックします。

1. **[許可されている場所]** ブレードの **[パラメーター]** タブで、 **[許可されている場所]** ドロップダウン リストから、 **[英国南部]**   を唯一の許可されている場所として選択します。 

   >具体的には、次の操作が必要です。 

1.  **[Review + create]** をクリックしてから  **[作成]**   をクリックし、ポリシーの割り当てを作成します。 

   >**注**:割り当てが成功し、割り当てが完了するまでに約 30 分かかる場合があるという通知が表示されます。

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The reason the Azure policy assignment might take up to 30 minutes to take effect is that is has to replicate globally. Typically this takes only a few minutes.  If the next task fails, simply wait a few minutes and attempt its steps again.

#### <a name="task-3-test-the-allowed-locations-policy-assignment"></a>タスク 3:許可されている場所ポリシーの割り当てをテストする

このタスクでは、許可されている場所ポリシーのテストをします。 

1. Azure portal で、ページ上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスに「**仮想ネットワーク**」と入力し、**Enter** キーを押します。

1. **[Virtual Networks]** ブレードで、 **[+ 作成]** をクリックします。

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: First, you will try to create a virtual network in East US. Since this is not an allowed location, the request should be blocked. 

1. **[仮想ネットワークの作成]**   ブレードの **[基本]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    |設定|値|
    |---|---|
    |Resource group|**AZ500LAB02**|
    |名前|**myVnet**|
    |リージョン|**(米国) 米国東部**|

1.  **[確認と作成]** をクリックします。 

1. **[仮想ネットワークの作成]**   ブレードの **[確認と作成]** タブで、**検証に失敗しました** というメッセージを確認します。 

    > **注**: "**検証に失敗しました**" という警告が表示されない場合は、**[前へ]** をクリックして、さらに数分待ちます。

1. On the <bpt id="p1">**</bpt>Basics<ept id="p1">**</ept> tab, click the error message link to open the <bpt id="p2">**</bpt>Policy Assignment<ept id="p2">**</ept> blade. You will see the policy assignment that restricts the location.

1. **[ポリシーの割り当て]** ブレードを閉じ、 **[仮想ネットワークの作成]** ブレードの **[基本]** タブをクリックし、 **[地域]** ドロップダウン リストから **[(ヨーロッパ) 英国南部]** を選択します。

1.  **[確認と作成]** をクリックして検証が成功したことを確認し、 **[作成]** をクリックして、仮想ネットワークが正常に作成されたことを確認します。 

> 演習結果:この演習では、組み込みのポリシー定義を選択し、それをリソース グループに割り当てることで、Azure ポリシーを適用する方法について学習しました。

**リソースをクリーンアップする**

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。

1. これがクラスで使用するリージョンであることを講師に確認します。

1. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB02" -Force -AsJob
    ```
1.  **[Cloud Shell]** ペインを閉じます。 
  
1. Azure portal で、ページ上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスに「**ポリシー**」と入力し、**Enter** キーを押します。

1. [作成] セクションで **[割り当て]** を選択します。

1. 割り当ての一覧で、このラボで作成した **[許可されている場所]** ポリシーの名前を選択します。

1. ポリシーの割り当てで、 **[割り当ての削除]** を選択し、 **[はい]** を選択します。
