---
lab:
  title: 07 - ネットワーク セキュリティ グループとアプリケーション セキュリティ グループ
  module: Module 02 - Implement Platform Protection
---

# <a name="lab-07-network-security-groups-and-application-security-groups"></a>ラボ 07:ネットワーク セキュリティ グループとアプリケーション セキュリティ グループ
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to implement your organization's virtual networking infrastructure and test to ensure it is working correctly. In particular:

- 組織には、次の 2 つのサーバー グループがあります。Web サーバーと管理サーバー。
- サーバーの各グループは、独自のアプリケーション セキュリティ グループに含める必要があります。 
- 管理サーバーに RDP を実行することはできますが、Web サーバーには RDP を実行することはできません。
- インターネットからアクセスすると、Web サーバーに IIS Web ページが表示されます。 
- ネットワーク セキュリティ グループの規則を使用して、ネットワーク アクセスを制御する必要があります。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:仮想ネットワーク インフラストラクチャを作成する
- 演習 2:仮想マシンを展開し、ネットワーク フィルターをテストする

## <a name="network-and-application-security-groups-diagram"></a>ネットワーク セキュリティ グループとアプリケーション セキュリティ グループの図

![image](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-create-the-virtual-networking-infrastructure"></a>演習 1:仮想ネットワーク インフラストラクチャを作成する

### <a name="estimated-timing-20-minutes"></a>推定時間:20 分

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East (US)<ept id="p1">**</ept> region. Verify with your instructor this is region to use for you class. 

この演習では、次のタスクを実行します。

- タスク 1:1 つのサブネットがある仮想ネットワークを作成します。
- タスク 2:2 つのアプリケーションのセキュリティ グループを作成します。
- タスク 3:ネットワーク セキュリティ グループを作成し、仮想ネットワーク サブネットに関連付けます。
- タスク 4:Web サーバーへのすべてのトラフィックと管理サーバーへの RDP に対するインバウンド NSG セキュリティ ルールを作成します。

#### <a name="task-1--create-a-virtual-network"></a>タスク 1:仮想ネットワークを作成する

このタスクでは、ネットワークおよびアプリケーションのセキュリティ グループで使用する仮想ネットワークを作成します。 

1. Azure portal ( **`https://portal.azure.com/`** ) にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**Virtual Networks**」と入力し、**Enter** キーを押します。

3. **[Virtual Networks]** ブレードで、**[+ 作成]** をクリックします。

4. **[仮想ネットワークの作成]** ブレードの **[基本]** タブで、次の設定を指定し (他の設定は既定値のままにします)、 **[次へ:IP アドレス]** をクリックします。

    |設定|[値]|
    |---|---|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**[新規作成]** をクリックし、「**AZ500LAB07**」という名前を入力する|
    |名前|**myVirtualNetwork**|
    |リージョン|**米国東部**|

5. **[仮想ネットワークの作成]** ブレードの **[IP アドレス]** タブで、**[IPv4 アドレス空間]** を **10.0.0.0/16** に設定し、必要に応じて **[サブネット名]** 列で **[既定]** をクリックし、**[サブネットの編集]** ブレードで次の設定を指定し、**[保存]** をクリックします。

    |設定|値|
    |---|---|
    |サブネット名|**default**|
    |サブネットのアドレス範囲|**10.0.0.0/24**|

6. **[仮想ネットワークの作成]** ブレードの **[IP アドレス]** タブに戻り、**[確認と作成]** をクリックします。

7. **[仮想ネットワークの作成]** ブレードの **[確認と作成]** タブで、**[作成]** をクリックします。

#### <a name="task-2--create-application-security-groups"></a>タスク 2:アプリケーション セキュリティ グループを作成する

このタスクでは、アプリケーション セキュリティ グループを作成します。

1. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**アプリケーション セキュリティ グループ**」と入力し、**Enter** キーを押します。

2. **[アプリケーション セキュリティ グループ]** ブレードで、**[+ 作成]** をクリックします。

3. **[アプリケーション セキュリティ グループの作成]** ブレードの **[基本]** タブで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |リソース グループ|**AZ500LAB07**|
    |名前|**myAsgWebServers**|
    |リージョン|**米国東部**|

    >**注**:このグループは Web サーバー用になります。

4. **[Review + create]\(レビュー + 作成\)** をクリックし、 **[作成]** をクリックします。

5. **[アプリケーション セキュリティ グループ]** ブレードに戻り、**[+ 作成]** をクリックします。

6. **[アプリケーション セキュリティ グループの作成]** ブレードの **[基本]** タブで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |リソース グループ|**AZ500LAB07**|
    |名前|**myAsgMgmtServers**|
    |リージョン|**米国東部**|

    >**注**:このグループは管理サーバー用になります。

7. **[Review + create]\(レビュー + 作成\)** をクリックし、 **[作成]** をクリックします。

#### <a name="task-3--create-a-network-security-group-and-associate-the-nsg-to-the-subnet"></a>タスク 3:ネットワーク セキュリティ グループを作成し、NSG をサブネットに関連付けます。

このタスクでは、ネットワーク セキュリティ グループを作成します。 

1. Azure portal で、Azure portal の上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**ネットワーク セキュリティ グループ**」と入力し、**Enter** キーを押します。

2. **[ネットワーク セキュリティ グループ]** ブレードで、**[+ 作成]** をクリックします。

3. **[ネットワーク セキュリティ グループの作成]** ブレードの **[基本]** タブで、次の設定を指定します。 

    |設定|[値]|
    |---|---|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB07**|
    |名前|**myNsg**|
    |リージョン|**米国東部**|

4. **[Review + create]\(レビュー + 作成\)** をクリックし、 **[作成]** をクリックします。

5. Azure portal で、**[ネットワーク セキュリティ グループ]** ブレードに戻り、**[myNsg]** エントリをクリックします。

6. **[myNsg]** ブレードの **[設定]** セクションで、**[サブネット]** をクリックし、**[+ 関連付け]** をクリックします。 

7. **[サブネットの関連付け]** ブレードで、次の設定を指定し、**[OK]** をクリックします。

    |設定|値|
    |---|---|
    |仮想ネットワーク|**myVirtualNetwork**|
    |Subnet|**default**|

#### <a name="task-4-create-inbound-nsg-security-rules-to-all-traffic-to-web-servers-and-rdp-to-the-management-servers"></a>タスク 4:Web サーバーへのすべてのトラフィックと管理サーバーへの RDP に対するインバウンド NSG セキュリティ ルールを作成します。 

1. **[myNsg]** ブレードの **[設定]** セクションで、**[受信セキュリティ規則]** をクリックします。

2. 既定の受信セキュリティ規則を確認してから、**[+ 追加]** を選択します。

3. **[受信セキュリティ規則の追加]** ブレードで、TCP ポート 80 および 443 を許可するために、**myAsgWebServers** アプリケーション セキュリティ グループに次の設定を指定します (他の設定はすべて既定値のままにします)。 

    |設定|値|
    |---|---|
    |宛先|ドロップダウン リストで、 **[アプリケーションのセキュリティ グループ]** を選択してから、 **[myAsgWebServers]** をクリックする|
    |宛先ポート範囲|**80,443**|
    |Protocol|**TCP**|
    |Priority|**100**|                                                    
    |名前|**Allow-Web-All**|

4. **[受信セキュリティ規則の追加]** ブレードで、**[追加]** をクリックして新しい受信規則を作成します。 

5. **[myNsg]** ブレードの **[設定]** セクションで、**[受信セキュリティ規則]** をクリックし、**[+ 追加]** をクリックします。

6. **[受信セキュリティ規則の追加]** ブレードで、RDP ポート (TCP 3389) を許可するために、次の設定を **myAsgMgmtServers** アプリケーション セキュリティ グループに指定します (他の設定はすべて既定値のままにします)。 

    |設定|値|
    |---|---|
    |宛先|ドロップダウン リストで、 **[アプリケーションのセキュリティ グループ]** を選択してから、 **[myAsgMgmtServers]** をクリックする|
    |宛先ポート範囲|**3389**|
    |Protocol|**TCP**|
    |Priority|**110**|                                                    
    |名前|**Allow-RDP-All**|

7. **[受信セキュリティ規則の追加]** ブレードで、**[追加]** をクリックして新しい受信規則を作成します。 

> 結果:仮想ネットワーク、受信セキュリティ規則を使用したネットワーク セキュリティ、および 2 つのアプリケーション セキュリティ グループをデプロイしました。 

### <a name="exercise-2-deploy-virtual-machines-and-test-network-filters"></a>演習 2:仮想マシンをデプロイしネットワーク フィルターをテストする

### <a name="estimated-timing-25-minutes"></a>推定時間:25 分

この演習では、次のタスクを実行します。

- タスク 1:Web サーバーとして使用する仮想マシンを作成します。
- タスク 2:管理サーバーとして使用する仮想マシンを作成します。 
- タスク 3:各仮想マシンのネットワーク インターフェイスをアプリケーション セキュリティ グループに関連付けます。
- タスク 4:ネットワーク トラフィックのフィルタリングをテストします。

#### <a name="task-1-create-a-virtual-machine-to-use-as-a-web-server"></a>タスク 1:Web サーバーとして使用する仮想マシンを作成します。

このタスクでは、Web サーバーとして使用する仮想マシンを作成します。

1. Azure portal で、Azure portal の上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**Virtual Machines**」と入力し、**Enter** キーを押します。

2. **[仮想マシン]** ブレードで、 **[+ 作成]** をクリックし、ドロップダウン リストで **[+ Azure 仮想マシン]** をクリックします。

3. **[仮想マシンの作成]** ブレードの **[基本]** タブで、以下の設定を指定します (他の設定は既定値のまま)。

   |設定|値|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|**AZ500LAB07**|
   |仮想マシン名|**myVmWeb**|
   |リージョン|**(US)East US**|
   |Image|**Windows Server 2022 Datacenter:Azure Edition- Gen2**|
   |サイズ|**Standard D2s v3**|
   |ユーザー名|**学生**|
   |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|
   |[パスワードの確認入力]|**パスワードを再入力する**|
   |パブリック受信ポート|**なし**|
   |既存の Windows Server ライセンスを使用しますか? |**No**|

    >**注**:公開の受信ポートの場合、事前に作成された NSG に依存します。 

4. **[次へ: ディスク >]** をクリックし、 **[仮想マシンの作成]** ブレードの **[ディスク]** タブで、 **[OS ディスクの種類]** を **[Standard HDD]** に設定し、 **[次へ:ネットワーク >]** を選択します。

5. **[仮想マシンの作成]** ブレードの **[ネットワーク]** タブで、以前に作成したネットワーク **myVirtualNetwork** を選択します。

6. **[NIC ネットワーク セキュリティ グループ]** で **[なし]** を選択します。

7. **[次へ: 管理 >]** をクリックし、 **[仮想マシンの作成]** ブレードの **[管理]** タブで、次の設定を確認します。

   |設定|値|
   |---|---|
   |ブート診断|**マネージド ストレージ アカウントで有効にする (推奨)**|

8. **[確認と作成]** ブレードで **[確認と作成]** をクリックし、検証が完了したことを確認し、**[作成]** をクリックします。

#### <a name="task-2-create-a-virtual-machine-to-use-as-a-management-server"></a>タスク 2:管理サーバーとして使用する仮想マシンを作成します。 

このタスクでは、管理サーバーとして使用する仮想マシンを作成します。

1. Azure portal で、 **[仮想マシン]** ブレードに戻り、 **[+ 作成]** をクリックし、ドロップダウンリストで **[+ Azure 仮想マシン]** をクリックします。

2. **[仮想マシンの作成]** ブレードの **[基本]** タブで、以下の設定を指定します (他の設定は既定値のまま)。

   |設定|値|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|**AZ500LAB07**|
   |仮想マシン名|**myVMMgmt**|
   |リージョン|(US) 米国東部|
   |Image|**Windows Server 2022 Datacenter:Azure Edition - Gen 2**|
   |サイズ|**Standard D2s v3**|
   |ユーザー名|**学生**|
   |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|
   |パブリック受信ポート|**なし**|
   |すでに Windows サーバーのライセンスを持っています|**No**|

    >**注**:公開の受信ポートの場合、事前に作成された NSG に依存します。 

3. **[次へ: ディスク >]** をクリックし、 **[仮想マシンの作成]** ブレードの **[ディスク]** タブで、 **[OS ディスクの種類]** を **[Standard HDD]** に設定し、 **[次へ:ネットワーク >]** を選択します。

4. **[仮想マシンの作成]** ブレードの **[ネットワーク]** タブで、以前に作成したネットワーク **myVirtualNetwork** を選択します。

5. **[NIC ネットワーク セキュリティ グループ]** で **[なし]** を選択します。

6. **[次へ: 管理 >]** をクリックし、 **[仮想マシンの作成]** ブレードの **[管理]** タブで、次の設定を指定します。

   |設定|値|
   |---|---|
   |ブート診断|**マネージド ストレージ アカウントで有効にする (推奨)**|

7. **[確認と作成]** ブレードで **[確認と作成]** をクリックし、検証が完了したことを確認し、**[作成]** をクリックします。

    >**注**:続行する前に、仮想マシンがデプロイされるのを待ちます。 

#### <a name="task-3-associate-each-virtual-machines-network-interface-to-its-application-security-group"></a>タスク 3:各仮想マシンのネットワーク インターフェイスをアプリケーション セキュリティ グループに関連付けます。

In this task, you will associate each virtual machines network interface with the corresponding application security group. The myVMWeb virtual machine interface will be associated to the myAsgWebServers ASG. The myVMMgmt virtual machine interface will be associated to the myAsgMgmtServers ASG. 

1. Azure portal で、**[仮想マシン]** ブレードに戻り、両方の仮想マシンが **[実行中]** 状態で一覧表示されていることを確認します。

2. 仮想マシンの一覧で、**[myVMWeb]** エントリをクリックします。

3. **[myVMWeb]** ブレードの **[設定]** セクションで、 **[ネットワーク]** をクリックしてから、 **[myVMWeb \| ネットワーク]** ブレードで **[アプリケーションのセキュリティ グループ]** タブをクリックします。

4. **[アプリケーション セキュリティ グループの構成]** をクリックし、**[アプリケーション セキュリティグループ]** ドロップダウン リストで **[myAsgWebServers]** を選択してから、**[保存]** をクリックします。

5. **[仮想マシン]** ブレードに戻り、仮想マシンの一覧で、**[myVMMgmt]** エントリをクリックします。

6. **[myVMMgmt]** ブレードの **[設定]** セクションで、 **[ネットワーク]** をクリックしてから、 **[myVMMgmt \| ネットワーク]** ブレードで **[アプリケーションのセキュリティ グループ]** タブをクリックします。

7. **[アプリケーション セキュリティ グループの構成]** をクリックし、**[アプリケーション セキュリティ グループ]** ドロップダウン リストで **[myAsgMgmtServers]** を選択してから、**[保存]** をクリックします。

#### <a name="task-4-test-the-network-traffic-filtering"></a>タスク 4:ネットワーク トラフィックのフィルタリングをテストする

In this task, you will test the network traffic filters. You should be able to RDP into the myVMMgmnt virtual machine. You should be able to connect from the internet to the myVMWeb virtual machine and view the default IIS web page.  

1. **[myVMMgmt]** 仮想マシン ブレードに戻ります。

2. **[myVMMgmt]** ブレードで **[接続]** をクリックし、ドロップダウン メニューで **[RDP]** をクリックします。 

3. 組織の仮想ネットワーク インフラストラクチャを実装し、正しく動作していることを確認するためのテストを依頼されました。

   |設定|値|
   |---|---|
   |ユーザー名|**学生**|
   |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

    >特に次の点に違いがあります。

4. Azure portal で、**[myVMWeb]** 仮想マシン ブレードに移動します。

5. **[myVMWeb]** ブレードの **[操作]** セクションで、**[コマンドの実行]** をクリックし、**[RunPowerShellScript]** をクリックします。

6. **[コマンド スクリプトの実行]** ペインで、次のコマンドを実行して、Web サーバーのロールを **myVmWeb** にインストールします。

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the installation to complete. This might take a couple of minutes. At that point, you can verify that myVMWeb can be accessed via HTTP/HTTPS.

7. Azure portal で、**[myVMWeb]** ブレードに戻ります。

8. **[myVMWeb]** ブレードで、myVmWeb Azure VM の **[パブリック IP]** アドレスを特定します。

9. 別のブラウザー タブを開き、前の手順で特定した IP アドレスに移動します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The browser page should display the default IIS welcome page because port 80 is allowed inbound from the internet based on the setting of the <bpt id="p2">**</bpt>myAsgWebServers<ept id="p2">**</ept> application security group. The network interface of the myVMWeb Azure VM is associated with that application security group. 

> 結果:NSG および ASG の設定が機能しており、トラフィックが正しく管理されていることを確認しました。 

**リソースをクリーンアップする**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  **[Cloud Shell]** ペインを閉じます。 
