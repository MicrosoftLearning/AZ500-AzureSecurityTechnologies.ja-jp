---
lab:
  title: 13 - Azure Monitor
  module: Module 04 - Manage security operations
---

# <a name="lab-13-azure-monitor"></a>ラボ 13:Azure Monitor
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

仮想マシンのパフォーマンスを監視するという概念実証を作成するよう依頼されました。 具体的には、次のことを行います。

- テレメトリとログを収集できるように仮想マシンを構成します。
- 収集できるテレメトリとログを表示します。
- データの使用方法とクエリ方法を示します。 

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

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

2. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** を選択します。

3. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

4. [Cloud Shell] ペイン内の PowerShell セッションで、次の手順を実行して、このラボで使用するリソース グループを作成します。
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**注**:このリソース グループは、ラボ 13、14、および 15 に使用されます。 

5. [Cloud Shell] ペイン内の PowerShell セッションで、次の手順を実行して、新しい Azure 仮想マシンを作成します。 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  資格情報の入力を求められた場合:

    |設定|値|
    |---|---|
    |User |**localadmin**|
    |Password|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

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
    |リージョン|**米国東部**|

4. **[Review + create](レビュー + 作成)** を選択します。

5. **[Log Analytics ワークスペースの作成]** ブレードの **[確認および作成]** タブで、 **[作成]** を選択します。

#### <a name="task-3-enable-the-log-analytics-virtual-machine-extension"></a>タスク 3:Log Analytics 仮想マシン拡張機能を有効にする

このタスクでは、Log Analytics 仮想マシン拡張機能を有効にします。 この拡張機能は、Windows および Linux Virtual Machines に Log Analytics エージェントをインストールします。 このエージェントは、仮想マシンからデータを収集し、指定した Log Analytics ワークスペースに転送します。 エージェントがインストールされると、自動的にアップグレードされ、常に最新の機能と修正プログラムが提供されます。 

1. Azure portal で、**[Log Analytics ワークスペース]** ブレードに戻り、ワークスペースの一覧で、前のタスクで作成したワークスペースを表すエントリをクリックします。

2. [Log Analytics ワークスペース] ブレードの **[概要]** ページの **[データ ソースの接続]** セクションで、 **[Azure Virtual Machines (VM)]** エントリをクリックします。

    >**注**:エージェントを正常にインストールするには、仮想マシンが実行されている必要があります。

3. 仮想マシンの一覧で、この演習の最初のタスクでデプロイした Azure VM **myVM** を表すエントリを見つけ、そのエントリが **[未接続]** と表示されていることに注意してください。

4. **[myVM]** エントリをクリックし、次に **[myVM]** ブレードで **[接続]** をクリックします。 

5. 仮想マシンが Log Analytics ワークスペースに接続するまで待ちます。

    >**注**:これには数分かかることがあります。 **[myVM]** ブレードに表示される **[状態]** は、**[接続中]** から **[このワークスペース]** に変わります。 

#### <a name="task-4-collect-virtual-machine-event-and-performance-data"></a>タスク 4:仮想マシン イベントおよびパフォーマンス データを収集する

このタスクでは、Windows システム ログのコレクションといくつかの一般的なパフォーマンス カウンターを構成します。 また、使用可能な他のソースも確認します。

1. Azure portal で、この演習で前に作成した Log Analytics ワークスペースに戻ります。

2. [Log Analytics ワークスペース] ブレードの **[設定]** セクションで、 **[レガシ エージェントの管理]** をクリックします。

3. **[エージェント構成]** ブレードで、Windows イベント ログ、Windows パフォーマンス カウンター、Linux パフォーマンス カウンター、IIS ログ、Syslog を含む構成可能な設定を確認します。 

4. **[Windows イベント ログ]** が選択されていることを確認し、**[+ Windows イベント ログの追加]** をクリックして、イベント ログの種類の一覧で **[システム]** を選択します。

    >**注**:これは、ワークスペースにイベント ログを追加する方法です。 その他の選択肢としては、**ハードウェア イベント** や **キー管理サービス** などがあります。  

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

    >**注**:**仮想マシンで使用可能なメモリ** というクエリから始めることができます。 結果が出ない場合は、スコープが仮想マシンに設定されていることを確認してください

6. クエリは、[新しいクエリ] タブで自動的に開きます。 

    >**注**:Log Analytics では、Kusto クエリ言語が使用されます。 既存のクエリをカスタマイズすることも、独自のクエリを作成することもできます。 

    >**注**:選択したクエリの結果は、クエリ ペインの下に自動的に表示されます。 クエリを再実行するには、**[実行]** をクリックします。

    >**注**:この仮想マシンは作成されたばかりなので、まだデータが存在しない可能性があります。 

    >**注**:データを異なる形式で表示するオプションがあります。 また、クエリの結果に基づいてアラート ルールを作成することもできます。

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
       
    4. Log Analytics ブレードに切り替えて、クエリを再実行します。 データが収集されるまで数分待って、再度クエリを実行する必要があるかもしれません。

> 結果:Log Analytics ワークスペースを使用して、データ ソースとクエリ ログを構成しました。 

**リソースをクリーンアップする**

>**注**:Azure Security Center ラボおよび Azure Sentinel ラボに必要なリソースは、このラボから削除しないでください。
 
