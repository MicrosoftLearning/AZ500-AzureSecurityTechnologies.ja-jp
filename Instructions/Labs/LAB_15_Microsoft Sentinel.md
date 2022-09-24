---
lab:
  title: 15 - Microsoft Sentinel
  module: Module 04 - Manage Security Operations
---

# <a name="lab-15-microsoft-sentinel"></a>ラボ 15:Microsoft Sentinel
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

**注:**  **Azure Sentinel** は **Microsoft Sentinel** に名前が変更されました 

You have been asked to create a proof of concept of Microsoft Sentinel-based threat detection and response. Specifically, you want to:

- Azure アクティビティと Microsoft Defender for Cloud からのデータ収集を開始する。
- 組み込みおよびカスタム アラートを追加する 
- プレイブックを使用してインシデントへの応答を自動化する方法を確認します。

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Microsoft Sentinel を実装する

## <a name="microsoft-sentinel-diagram"></a>Microsoft Sentinel の図

![image](https://user-images.githubusercontent.com/91347931/157538440-4953be73-90be-4edd-bd23-b678326ba637.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>ラボ ファイル：

- **\\Allfiles\\Labs\\15\\changeincidentseverity.json**

### <a name="exercise-1-implement-microsoft-sentinel"></a>演習 1:Microsoft Sentinel を実装する

### <a name="estimated-timing-30-minutes"></a>推定時間:30 分

この演習では、次のタスクを実行します。

- タスク 1:Microsoft Sentinel をオンボードする
- タスク 2:Azure アクティビティを Sentinel に接続する
- タスク 3:Azure アクティビティ データ コネクタを使用するルールを作成します。 
- タスク 4:プレイブックを作成する
- タスク 5:カスタム アラートを作成し、自動化された応答としてプレイブックを構成します。
- タスク 6:インシデントを発生させ、関連付けられているアクションを確認する。

#### <a name="task-1-on-board-azure-sentinel"></a>タスク 1:Azure Sentinel をオンボードにする

このタスクでは、Microsoft Sentinel にオンボードし、Log Analytics ワークスペースを接続します。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスに「**Microsoft Sentinel**」と入力し、**Enter** キーを押します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If this is your first attempt to action Microsoft Sentinel in the Azure dashboard complete the following step(s): In the Azure portal, in the <bpt id="p2">**</bpt>Search resources,  services, and docs<ept id="p2">**</ept> text box at the top of the Azure portal page, type <bpt id="p3">**</bpt>Microsoft Sentinel<ept id="p3">**</ept> and press the <bpt id="p4">**</bpt>Enter<ept id="p4">**</ept> key. Select <bpt id="p1">**</bpt>Microsoft Sentinel<ept id="p1">**</ept> from the <bpt id="p2">**</bpt>Services<ept id="p2">**</ept> view.
  
3. **[Microsoft Sentinel]** ブレードで、 **[+ 作成]** をクリックします。

4. **[ワークスペースへの Microsoft Sentinel の追加]** ブレードで、Azure Monitor ラボで作成した Log Analytics ワークスペースを選択し、 **[追加]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Microsoft Sentinel has very specific requirements for workspaces. For example, workspaces created by Microsoft Defender for Cloud can not be used. Read more at <bpt id="p1">[</bpt>Quickstart: On-board Azure Sentinel<ept id="p1">](https://docs.microsoft.com/en-us/azure/sentinel/quickstart-onboard)</ept>
    
#### <a name="task-2-configure-microsoft-sentinel-to-use-the-azure-activity-data-connector"></a>タスク 2:Azure アクティビティ データ コネクタを使用するように Microsoft Sentinel を構成します。 

このタスクでは、Azure アクティビティ データ コネクタを使用するように Sentinel を構成します。  

1. Azure portal の **[Microsoft Sentinel \| 概要]** ブレードで、 **[構成]** セクションの **[データ コネクタ]** をクリックします。 

2. **[Microsoft Sentinel \| データ コネクタ]** ブレードで、使用可能なコネクタのリストを確認し、検索バーに「**Azure**」と入力して、**Azure アクティビティ** コネクタを表すエントリを選択し (必要に応じて \<< を使用して左側のメニュー バーを非表示にします)、説明と状態を確認し、**[コネクタ ページを開く]** をクリックします。

3. On the <bpt id="p1">**</bpt>Azure Activity<ept id="p1">**</ept> blade the <bpt id="p2">**</bpt>Instructions<ept id="p2">**</ept> tab should be selected, note the <bpt id="p3">**</bpt>Prerequisites<ept id="p3">**</ept> and scroll down to the <bpt id="p4">**</bpt>Configuration<ept id="p4">**</ept>. Take note of the information describing the connector update. Your Azure Pass subscription never used the legacy connection method so you can skip step 1 (the <bpt id="p1">**</bpt>Disconnect All<ept id="p1">**</ept> button will be grayed out) and proceed to step 2.

4. 手順 2 の「**診断設定の新しいパイプラインでサブスクリプションを接続します**」で、「[Azure Policy の割り当て] ウィザードの起動と手順の実行」の手順を確認し、**[Azure ポリシー割り当てウィザードを起動する]\>** をクリックします。

5. On the <bpt id="p1">**</bpt>Configure Azure Activity logs to stream to specified Log Analytics workspace<ept id="p1">**</ept> (Assign Policy page) <bpt id="p2">**</bpt>Basics<ept id="p2">**</ept> tab, click the <bpt id="p3">**</bpt>Scope elipsis (...)<ept id="p3">**</ept> button. In the <bpt id="p1">**</bpt>Scope<ept id="p1">**</ept> page choose your Azure Pass subscription from the drop-down subscription list and click the <bpt id="p2">**</bpt>Select<ept id="p2">**</ept> button at the bottom of the page.

    >**注**:リソース グループを選択*しないでください*。

6. あなたは、Microsoft Sentinel ベースの脅威検出と応答の概念実証を作成するよう依頼されました。

7. 具体的には、次のことを行います。

8. **[パラメーター]** タブの下部にある **[次へ]** ボタンをクリックして、**非準拠メッセージ** タブに進みます。必要に応じて非準拠メッセージを入力し (これはオプションです)、**[非準拠メッセージ]** タブの下部にある **[確認と作成]** ボタンをクリックします。

9. Click the <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> button. You should observe three succeeded status messages: <bpt id="p1">**</bpt>Creating policy assignment succeeded, Role Assignments creation succeeded, and Remediation task creation succeeded<ept id="p1">**</ept>.

    >**注**:通知、ベル アイコンをチェックして、3 つの成功したタスクを確認できます。

10. **[Azure アクティビティ]** ペインに "**受信したデータ**" グラフが表示されていることを確認します (ブラウザー ページを更新する必要がある場合があります)。  

    >**注**: 状態に "接続済み" と表示され、グラフに受信したデータが表示されるまで、15 分以上かかる場合があります。

#### <a name="task-3-create-a-rule-that-uses-the-azure-activity-data-connector"></a>タスク 3:Azure アクティビティ データ コネクタを使用するルールを作成します。 

このタスクでは、Azure アクティビティ データ コネクタを使用するルールを確認し、作成します。 

1. **[Microsoft Sentinel \| 構成]** ブレードで、 **[分析]** をクリックします。 

2. **[Microsoft Sentinel \| 分析]** ブレードで、 **[ルール テンプレート]** タブをクリックします。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Review the types of rules you can create. Each rule is associated with a specific Data Source.

3. In the listing of rule templates, type <bpt id="p1">**</bpt>Suspicious<ept id="p1">**</ept> into the search bar form and click the <bpt id="p2">**</bpt>Suspicious number of resource creation or deployment<ept id="p2">**</ept> entry associated with the <bpt id="p3">**</bpt>Azure Activity<ept id="p3">**</ept> data source. And then, in the pane displaying the rule template properties, click <bpt id="p1">**</bpt>Create rule<ept id="p1">**</ept> (scroll to the right of the page if needed).

    >**注**:このルールの重大度は中程度です。 

4. **[分析ルール ウィザード - テンプレートから新しいルールを作成]** ブレードの **[全般]** タブで、既定の設定をそのまま使用し、 **[次へ:ルールのロジックを設定 >]** を選択します。

5. **[分析ルール ウィザード - テンプレートから新しいルールを作成する]** ブレードの **[ルール ロジックの設定]** タブで、既定の設定をそのまま使用し、 **[次へ:インシデントの設定 (プレビュー) >]** をクリックします。

6. **[分析ルール ウィザード - テンプレートから新しいルールを作成]** ブレードの **[インシデント設定]** タブで、既定の設定をそのまま使用し、 **[次へ:自動応答>]** を選択します。 

    >**注**:この機能を使用すると、ロジック アプリとして実装されたプレイブックをルールに追加して、問題の修復を自動化できます。

7. **[分析ルール ウィザード - テンプレートから新しいルールを作成]** ブレードの **[自動応答]** タブで、既定の設定をそのまま使用し、 **[次へ:確認>]** を選択します。 

8. **[分析ルール ウィザード - テンプレートから新しいルールを作成する]** ブレードの **[確認と作成]** タブで、**[作成]** をクリックします。

    >**注**:これで、アクティブなルールが作成されました。

#### <a name="task-4-create-a-playbook"></a>タスク 4:プレイブックを作成する

In this task, you will create a playbook. A security playbook is a collection of tasks that can be invoked by Microsoft Sentinel in response to an alert. 

1. Azure portal で Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**カスタム テンプレートをデプロイする**」と入力し、**Enter** キーを押します。

2. **[カスタム デプロイ]** ブレードで、**[エディターで独自のテンプレートをビルド]** オプションをクリックします。

3. **[テンプレートの編集]** ブレードで、 **[ファイルの読み込み]** をクリックし、 **\\Allfiles\\Labs\\15\\changeincidentseverity.json** ファイルを見つけて、 **[開く]** をクリックします。

    >**注**:サンプル プレイブックは、[https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) にあります。

4. **[テンプレートの編集]** ブレードで、**[保存]** をクリックします。

5. **[カスタム デプロイ]** ブレードで、次の設定が構成されていることを確認します (既定値を使用して他の設定を行います)。

    |設定|[値]|
    |---|---|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB131415**|
    |場所|**(米国) 米国東部**|
    |プレイブックの名前|**Change-Incident-Severity**|
    |[ユーザー名]|自分のメール アドレス|

6. **[Review + create]\(レビュー + 作成\)** をクリックし、 **[作成]** をクリックします。

    >**注**: デプロイが完了するまで待ちます。

7. Azure portal の、Azure portal ページの上部にある **[ソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**リソース グループ**」と入力し、**Enter** キーを押します。

8. **[リソース グループ]** ブレードのリソース グループの一覧で、 **[AZ500LAB131415]** エントリをクリックします。

9. **[AZ500LAB131415]** リソース グループ ブレードのリソースの一覧で、新しく作成された **Change-Incident-Severity** ロジック アプリを表すエントリをクリックします。

10. **[インシデントの重大度の変更]** ブレードで、**[編集]** をクリックします。

    >このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。

11. **[Logic Apps デザイナー]** ブレードで、最初の **[接続]** 手順をクリックします。

12. **[新規追加]** をクリックし、**[テナント]** ドロップダウン リストのエントリに Azure AD テナント名が含まれていることを確認し、**[サインイン]** をクリックします。

13. プロンプトが表示された場合は、このラボで使用する Azure サブスクリプションの所有者または共同作成者のロールを使用したユーザー アカウントでログインします。

14. 2 つ目の **[接続]** 手順をクリックし、接続の一覧で、前の手順で作成した接続を表す 2 つ目のエントリを選択します。

15. 残りの 2 つの **[接続]** 手順についても、前の手順を繰り返します。

    >**注**:いずれの手順にも警告が表示されていないことを確認します。

16. **[Logic Apps デザイナー]** ブレードで、**[保存]** をクリックして変更を保存します。

#### <a name="task-5-create-a-custom-alert-and-configure-a-playbook-as-an-automated-response"></a>タスク 5:カスタム アラートを作成し、自動化された応答としてプレイブックを構成する

1. Azure portal で、 **[Microsoft Sentinel \| 概要]** ブレードに戻ります。

2. **[Microsoft Sentinel \| 概要]** ブレードの **[構成]** セクションで、 **[分析]** をクリックします。

3. **[Microsoft Sentinel \| 分析]** ブレードで **[+ 作成]** をクリックし、ドロップダウン メニューの **[スケジュールされたクエリ ルール]** をクリックします。 

4. **[分析ルール ウィザード - 新しいルールの作成]** ブレードの **[全般]** タブで、次の設定を指定します (既定値を他のユーザーのままにします)。

    |設定|[値]|
    |---|---|
    |名前|**プレイブック デモ**|
    |方針|**初期アクセス**|

5. **[次へ: ルールのロジックを設定 >]** を選択します。

6. **[分析ルール ウィザード - 新しいルールの作成]** ブレードの **[ルール ロジックの設定]** タブで、**[ルール クエリ]** テキスト ボックスに次のルール クエリを貼り付けます。 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**注**:このルールは、Just In Time VM アクセス ポリシーの削除を識別します。

    >これがクラスで使用するリージョンであることを講師に確認します。 


7. **[分析ルール ウィザード - 新しいルールの作成]** ブレードの **[ルール ロジックの設定]** タブの **[クエリのスケジュール]** セクションで、**[クエリを実行する間隔]** を **[5 分ごとに実行]** に設定します。

8. **分析ルール ウィザード - 新しいルールの作成** ブレードの **ルール ロジックの設定** タブで、残りの設定の既定値をそのまま使用し、**次へ:インシデントの設定 >** をクリックします。

9. **[分析ルール ウィザード - 新しいルールの作成]** ブレードの **[インシデント設定]** タブで、既定の設定をそのまま使用し、 **[次へ:自動応答>]** を選択します。 

10. **[分析ルール ウィザード - 新しいルールの作成]** ブレードの **[自動応答]** タブで、 **[Alert automation (classic)]\(アラートのオートメーション (クラシック)\)** ドロップダウン リストで **[インシデントの重大度の変更]** エントリの横にあるチェックボックスをオンにして、 **[次へ:確認>]** をクリックします。 

11. **[分析ルール ウィザード - 新しいルールの作成]** ブレードの **[確認と作成]** タブで、**[作成]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You now have a new active rule called <bpt id="p2">**</bpt>Playbook Demo<ept id="p2">**</ept>. If an event identified by the rue logic occurs, it will result in a medium severity alert, which will generate a corresponding incident.

#### <a name="task-6-invoke-an-incident-and-review-the-associated-actions"></a>タスク 6:インシデントを発生させ、関連付けられているアクションを確認する。

1. Azure portal で、 **[Microsoft Defender for Cloud \| 概要]** ブレードに移動します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Check your secure score. By now it should have updated. 

2. **[Microsoft Defender for Cloud \| ワークロード保護]** ブレードで、 **[高度な保護]** の **[Just-In-Time VM アクセス]** セクションをクリックします。

3. **[Microsoft Defender for Cloud \| Just-In-Time VM アクセス]** ブレードで、**myVM** 仮想マシンを参照する行の右側で、**省略記号**ボタンをクリックし、 **[削除]** をクリックして、 **[はい]** をクリックします。

    ><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> If the VM is not listed in the <bpt id="p2">**</bpt>Just-in-time VMs<ept id="p2">**</ept>, navigate to <bpt id="p3">**</bpt>Virutal Machine<ept id="p3">**</ept> blade and click the <bpt id="p4">**</bpt>Configuration<ept id="p4">**</ept>, Click the <bpt id="p5">**</bpt>Enable the Just-in-time VMs<ept id="p5">**</ept> option under the <bpt id="p6">**</bpt>Just-in-time Vm's access<ept id="p6">**</ept>. Repeat the above step to navigate back to the <bpt id="p1">**</bpt>Microsoft Defender for Cloud<ept id="p1">**</ept> and refresh the page, the VM will appear.

4. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**アクティビティ ログ**」を入力し、**Enter** キーを押します。

5. **[アクティビティ ログ]** ブレードに移動し、**[JIT ネットワーク アクセス ポリシーの削除]** エントリをメモします。 

    >**注**:表示されるまでに数分かかることがあります。 

6. Azure portal で、 **[Microsoft Sentinel \| 概要]** ブレードに戻ります。

7. **[Microsoft Sentinel \| 概要]** ブレードでダッシュボードを確認し、Just In Time VM アクセス ポリシーの削除に対応するアラートが表示されることを確認します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It can take up to 5 minutes for alerts to appear on the <bpt id="p2">**</bpt>Microsoft Sentinel <ph id="ph1">\|</ph> Overview<ept id="p2">**</ept> blade. If you are not seeing an alert at that point, run the query rule referenced in the previous task to verify that the Just In Time access policy deletion activity has been propagated to the Log Analytics workspace associated with your Microsoft Sentinel instance. If that is not the case, re-create the Just in time VM access policy and delete it again.

8. **[Microsoft Sentinel \| 概要]** ブレードの **[脅威の管理]** セクションで、 **[インシデント]** をクリックします。

9. ブレードに中程度または高い重大度レベルのインシデントが表示されることを確認します。

    >**注**:インシデントが **[Microsoft Sentinel \| インシデント]** ブレードに表示されるまでに、最大 5 分かかる場合があります。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Review the <bpt id="p2">**</bpt>Microsoft Sentinel <ph id="ph1">\|</ph> Playbooks<ept id="p2">**</ept> blade. You will find there the count of succesfull and failed runs.

    >**注**:インシデントに別の重大度レベルとステータスを割り当てるオプションがあります。

> 結果:Microsoft Sentinel ワークスペースを作成し、Azure アクティビティ ログに接続し、Just In Time VM アクセス ポリシーの削除に応答してトリガーされるプレイブックとカスタム アラートを作成し、構成が有効であることを確認しました。

**リソースをクリーンアップする**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. **[Cloud Shell]** ペインを閉じます。
