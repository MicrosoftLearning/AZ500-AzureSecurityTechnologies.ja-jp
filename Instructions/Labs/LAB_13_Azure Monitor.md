---
lab:
  title: 13 - Azure Monitor
  module: Module 04 - Manage security operations
---

# <a name="lab-13-azure-monitor"></a>ラボ 13:Azure Monitor
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to create a proof of concept of monitoring virtual machine performance. Specifically, you want to:

- テレメトリとログを収集できるように仮想マシンを構成します。
- 収集できるテレメトリとログを表示します。
- データの使用方法とクエリ方法を示します。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Azure Monitor を使用して Azure Virtual Machine からデータを収集する

## <a name="azure-monitor"></a>Azure Monitor

![image](https://user-images.githubusercontent.com/91347931/157536648-0a286514-a7e2-4058-9dea-e42da21eef76.png)

## <a name="instructions"></a>手順

### <a name="exercise-1-collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>演習 1:Azure Monitor を使用して Azure Virtual Machine からデータを収集する

### <a name="exercise-timing-20-minutes"></a>演習のタイミング:20 分

この演習では、次のタスクを実行します。 

- タスク 1: Azure 仮想マシンをデプロイする 
- タスク 2:Log Analytics ワークスペースを作成する
- タスク 3:Log Analytics 仮想マシン拡張機能を有効にする
- タスク 4:仮想マシン イベントおよびパフォーマンス データを収集する
- タスク 5:収集したデータの表示と照会を行う 

#### <a name="task-1-deploy-an-azure-virtual-machine"></a>タスク 1: Azure 仮想マシンをデプロイする

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

3. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

4. [Cloud Shell] ペイン内の PowerShell セッションで、次の手順を実行して、このラボで使用するリソース グループを作成します。
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**注**:このリソース グループは、ラボ 13、14、および 15 に使用されます。 

5. [Cloud Shell] ペイン内の PowerShell セッションで、次の手順を実行して、新しい Azure 仮想マシンを作成します。 

    ><bpt id="p1">**</bpt>Attention<ept id="p1">**</ept>: The New-AzVm command doesn't work in the Azure CLI version 4.24 and Microsoft is currently investigating for resolution.  The work around in this lab is to install and revert back to Az.Compute version 4.23.0, which is unaffected by this issue.
   
    >**手順**:Az.Compute バージョン 4.23.0 に戻す 
  
   #### <a name="step-1-download-the-working-version-of-the-module-4230-into-your-cloud-shell-session"></a>ステップ 1: モジュールの動作バージョン (4.23.0) を Cloud Shell セッションにダウンロードする 
   **種類**: Install-Module -Name Az.Compute -Force -RequiredVersion 4.23.0

   #### <a name="step-2-start-a-new-powershell-session-that-will-allow-the-azcompute-assembly-version-to-be-loaded"></a>ステップ 2: Az.Compute アセンブリ バージョンの読み込みを可能にする新しい PowerShell セッションを開始する 
   **種類**: pwsh

   #### <a name="step-3-verify-that-version-4230-is-loaded"></a>ステップ 3: バージョン 4.23.0 が読み込まれていることを確認する
   **種類**: Get-Module -Name Az.Compute
   
    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -OpenPorts 80,3389
    ```

6.  資格情報の入力を求められた場合:

    |設定|値|
    |---|---|
    |User |**localadmin**|
    |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

    >**注**: デプロイが完了するまで待ちます。 

7. [Cloud Shell] ペイン内の PowerShell セッションで、次のコマンドを実行して、**myVM** という名前の仮想マシンが作成され、その **[ProvisioningState]** が **[成功]** であることを確認します。

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. [Cloud Shell] ペインを閉じます。 

#### <a name="task-2-create-a-log-analytics-workspace"></a>タスク 2:Log Analytics ワークスペースを作成する

このタスクでは、Log Analytics ワークスペースを作成します。 

1. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**Log Analytics ワークスペース**」と入力し、**Enter** キーを押します。

2. **[Log Analytics ワークスペース]** ブレードで、 **[+ 作成]** をクリックします。

3. **[Log Analytics ワークスペースの作成]** ブレードの **[基本]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    |設定|値|
    |---|---|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB131415**|
    |名前|グローバルにユニークな任意の名前|
    |リージョン|**(米国) 米国東部**|

4. **[Review + create](レビュー + 作成)** を選択します。

5. **[Log Analytics ワークスペースの作成]** ブレードの **[確認および作成]** タブで、 **[作成]** を選択します。

#### <a name="task-3-enable-the-log-analytics-virtual-machine-extension"></a>タスク 3:Log Analytics 仮想マシン拡張機能を有効にする

In this task, you will enable the Log Analytics virtual machine extension. This extension installs the Log Analytics agent on Windows and Linux virtual machines. This agent collects data from the virtual machine and transfers it to the Log Analytics workspace that you designate. Once the agent is installed it will be automatically upgraded ensuring you always have the latest features and fixes. 

1. Azure portal で、**[Log Analytics ワークスペース]** ブレードに戻り、ワークスペースの一覧で、前のタスクで作成したワークスペースを表すエントリをクリックします。

2. [Log Analytics ワークスペース] ブレードの **[概要]** ページの **[データ ソースの接続]** セクションで、 **[Azure Virtual Machines (VM)]** エントリをクリックします。

    >**注**:エージェントを正常にインストールするには、仮想マシンが実行されている必要があります。

3. 仮想マシンの一覧で、この演習の最初のタスクでデプロイした Azure VM **myVM** を表すエントリを見つけ、そのエントリが **[未接続]** と表示されていることに注意してください。

4. **[myVM]** エントリをクリックし、次に **[myVM]** ブレードで **[接続]** をクリックします。 

5. 仮想マシンが Log Analytics ワークスペースに接続するまで待ちます。

    >仮想マシンのパフォーマンスを監視するという概念実証を作成するよう依頼されました。 

#### <a name="task-4-collect-virtual-machine-event-and-performance-data"></a>タスク 4:仮想マシン イベントおよびパフォーマンス データを収集する

具体的には、次のことを行います。

1. Azure portal で、この演習で前に作成した Log Analytics ワークスペースに戻ります。

2. [Log Analytics ワークスペース] ブレードの **[設定]** セクションで、 **[レガシ エージェントの管理]** をクリックします。

3. **[エージェント構成]** ブレードで、Windows イベント ログ、Windows パフォーマンス カウンター、Linux パフォーマンス カウンター、IIS ログ、Syslog を含む構成可能な設定を確認します。 

4. **[Windows イベント ログ]** が選択されていることを確認し、**[+ Windows イベント ログの追加]** をクリックして、イベント ログの種類の一覧で **[システム]** を選択します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is how you add event logs to the workspace. Other choices include, for example, <bpt id="p1">**</bpt>Hardware events<ept id="p1">**</ept> or <bpt id="p2">**</bpt>Key Management Service<ept id="p2">**</ept>.  

5. **[情報]** チェックボックスの選択を解除し、**[エラー]** チェックボックスと **[警告]** チェックボックスは選択したままにします。

6. **[Windows パフォーマンス カウンター]** をクリックし、**[+ パフォーマンス カウンターの追加]** をクリックして、使用可能なパフォーマンス カウンターの一覧を確認し、次のものを追加します。

    - Memory(\*)\Available Memory Mbytes
    - Process(\*)\\% Processor Time
    - Event Tracing for Windows\Total Memory Usage --- Non-Paged Pool
    - Event Tracing for Windows\Total Memory Usage --- Paged Pool

    >**注**:カウンターが追加され、60 秒の収集サンプル間隔で構成されます。
  
7. **[エージェント構成]** ブレードで **[適用]** をクリックします。

#### <a name="task-5-view-and-query-collected-data"></a>タスク 5:収集したデータの表示と照会を行う

このタスクでは、データ コレクションに対してログ検索を実行します。 

1. Azure portal で、この演習で前に作成した Log Analytics ワークスペースに戻ります。

2. [Log Analytics ワークスペース] ブレードの **[全般]** セクションで、**[ログ]** をクリックします。

3. 必要に応じて、**[Log Analysis へようこそ]** ウィンドウを閉じます。 

4. **[クエリ]** ペインの **[すべてのクエリ]** 列で、リソースの種類の一覧を一番下までスクロールして、**[仮想マシン]** をクリックします
    
5. 定義済みクエリの一覧を確認し、**[メモリと CPU 使用率]** を選択し、対応する **[実行]** ボタンをクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can start with the query <bpt id="p2">**</bpt>Virtual machine available memory<ept id="p2">**</ept>. If you don't get any results check the scope is set to virtual machine

6. クエリは、[新しいクエリ] タブで自動的に開きます。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Log Analytics uses the Kusto query language. You can customize the existing queries or create your own. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The results of the query you selected are automatically displayed below the query pane. To re-run the query, click <bpt id="p1">**</bpt>Run<ept id="p1">**</ept>.

    >**注**:この仮想マシンは作成されたばかりなので、まだデータが存在しない可能性があります。 

    >このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。

    >**注**:以下の手順で、このラボで前にデプロイした Azure VM に追加の負荷を生成することができます。

    1. Azure VM ブレードに移動します。
    2. Azure VM ブレードの **[操作]** セクションで、 **[コマンドを実行]** を選択し、 **[RunPowerShellScript]** ブレードで次のスクリプトを入力し、 **[実行]** をクリックします。
    3. 
       ```cmd
       cmd
       :loop
       dir c:\ /s > SWAP
       goto loop
       ```
       
    4. これがクラスで使用するリージョンであることを講師に確認します。

> 結果:Log Analytics ワークスペースを使用して、データ ソースとクエリ ログを構成しました。 

**リソースをクリーンアップする**

>**注**:Azure Security Center ラボおよび Azure Sentinel ラボに必要なリソースは、このラボから削除しないでください。
 
