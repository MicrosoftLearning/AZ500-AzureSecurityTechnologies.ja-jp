---
lab:
  title: 02 - ネットワーク セキュリティ グループとアプリケーション セキュリティ グループ
  module: Module 01 - Plan and implement security for virtual networks
---

# ラボ 02: ネットワーク セキュリティ グループとアプリケーション セキュリティ グループ
# 受講生用ラボ マニュアル

## ラボのシナリオ

組織の仮想ネットワーク インフラストラクチャを実装し、正しく動作していることを確認するためのテストを依頼されました。 特に次の点に違いがあります。

- 組織には、次の 2 つのサーバー グループがあります。Web サーバーと管理サーバー。
- サーバーの各グループは、独自のアプリケーション セキュリティ グループに含める必要があります。 
- 管理サーバーに RDP を実行することはできますが、Web サーバーには RDP を実行することはできません。
- インターネットからアクセスすると、Web サーバーに IIS Web ページが表示されます。 
- ネットワーク セキュリティ グループの規則を使用して、ネットワーク アクセスを制御する必要があります。 

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1:仮想ネットワーク インフラストラクチャを作成する
- 演習 2:仮想マシンを展開し、ネットワーク フィルターをテストする

## ネットワーク セキュリティ グループとアプリケーション セキュリティ グループの図

![image](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## Instructions

### 演習 1:仮想ネットワーク インフラストラクチャを作成する

### 推定時間:20 分

> このラボのすべてのリソースに対して、**東部 (米国)** リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

この演習では、次のタスクを実行します。

- タスク 1:1 つのサブネットがある仮想ネットワークを作成します。
- タスク 2:2 つのアプリケーションのセキュリティ グループを作成します。
- タスク 3:ネットワーク セキュリティ グループを作成し、仮想ネットワーク サブネットに関連付けます。
- タスク 4:Web サーバーへのすべてのトラフィックと管理サーバーへの RDP に対するインバウンド NSG セキュリティ ルールを作成します。

#### タスク 1:仮想ネットワークを作成する

このタスクでは、ネットワークおよびアプリケーションのセキュリティ グループで使用する仮想ネットワークを作成します。 

1. Azure portal ( **`https://portal.azure.com/`** ) にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**Virtual Networks**」と入力し、**Enter** キーを押します。

3. **[Virtual Networks]** ブレードで、**[+ 作成]** をクリックします。

4. **[仮想ネットワークの作成]** ブレードの **[基本]** タブで、次の設定を指定し (他の設定は既定値のままにします)、 **[次へ:IP アドレス]** をクリックします。

    |設定|値|
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

#### タスク 2:アプリケーション セキュリティ グループを作成する

このタスクでは、アプリケーション セキュリティ グループを作成します。

1. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**アプリケーション セキュリティ グループ**」と入力し、**Enter** キーを押します。

2. **[アプリケーション セキュリティ グループ]** ブレードで、**[+ 作成]** をクリックします。

3. **[アプリケーション セキュリティ グループの作成]** ブレードの **[基本]** タブで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |名前|**myAsgWebServers**|
    |リージョン|**米国東部**|

    >**注**:このグループは Web サーバー用になります。

4. **[Review + create](レビュー + 作成)** をクリックし、 **[作成]** をクリックします。

5. **[アプリケーション セキュリティ グループ]** ブレードに戻り、**[+ 作成]** をクリックします。

6. **[アプリケーション セキュリティ グループの作成]** ブレードの **[基本]** タブで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |名前|**myAsgMgmtServers**|
    |リージョン|**米国東部**|

    >**注**:このグループは管理サーバー用になります。

7. **[Review + create](レビュー + 作成)** をクリックし、 **[作成]** をクリックします。

#### タスク 3:ネットワーク セキュリティ グループを作成し、NSG をサブネットに関連付けます。

このタスクでは、ネットワーク セキュリティ グループを作成します。 

1. Azure portal で、Azure portal の上部にある **[リソース、サービス、ドキュメントを検索する]** テキスト ボックスで、「**ネットワーク セキュリティ グループ**」と入力し、**Enter** キーを押します。

2. **[ネットワーク セキュリティ グループ]** ブレードで、**[+ 作成]** をクリックします。

3. **[ネットワーク セキュリティ グループの作成]** ブレードの **[基本]** タブで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB07**|
    |名前|**myNsg**|
    |リージョン|**米国東部**|

4. **[Review + create](レビュー + 作成)** をクリックし、 **[作成]** をクリックします。

5. Azure portal で、**[ネットワーク セキュリティ グループ]** ブレードに戻り、**[myNsg]** エントリをクリックします。

6. **[myNsg]** ブレードの **[設定]** セクションで、**[サブネット]** をクリックし、**[+ 関連付け]** をクリックします。 

7. **[サブネットの関連付け]** ブレードで、次の設定を指定し、**[OK]** をクリックします。

    |設定|値|
    |---|---|
    |仮想ネットワーク|**myVirtualNetwork**|
    |Subnet|**default**|

#### タスク 4: Web サーバーへのすべてのトラフィックとサーバーへの RDP に対するインバウンド NSG セキュリティ ルールを作成します。 

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

### 演習 2:仮想マシンをデプロイしネットワーク フィルターをテストする

### 推定時間:25 分

この演習では、次のタスクを実行します。

- タスク 1:Web サーバーとして使用する仮想マシンを作成します。
- タスク 2:管理サーバーとして使用する仮想マシンを作成します。 
- タスク 3:各仮想マシンのネットワーク インターフェイスをアプリケーション セキュリティ グループに関連付けます。
- タスク 4:ネットワーク トラフィックのフィルタリングをテストします。

#### タスク 1:Web サーバーとして使用する仮想マシンを作成します。

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
   |可用性のオプション|**インフラストラクチャ冗長は必要ありません**
   |セキュリティの種類|**Standard**
   |Image|**Windows Server 2022 Datacenter: Azure Edition- x64 Gen2**|
   |サイズ|**Standard D2s v3**|
   |ユーザー名|**Student**|
   |パスワード|**自分専用のパスワードを作成し、後のラボで参照できるよう記録しておいてください**|
   |[パスワードの確認入力]|**パスワードを再入力する**|
   |パブリック受信ポート|**なし**|
   |既存の Windows Server ライセンスを使用しますか? |**No**|

    >**注**:公開の受信ポートの場合、事前に作成された NSG に依存します。 

5. **[次へ: ディスク >]** をクリックし、 **[仮想マシンの作成]** ブレードの **[ディスク]** タブで、 **[OS ディスクの種類]** を **[Standard HDD]** に設定し、 **[次へ:ネットワーク >]** を選択します。

6. **[仮想マシンの作成]** ブレードの **[ネットワーク]** タブで、以前に作成したネットワーク **myVirtualNetwork** を選択します。

7. **[NIC ネットワーク セキュリティ グループ]** で **[なし]** を選択します。

8. **[次へ: 管理 >]**、**[次へ: 監視 >]** の順にクリックします。 **[仮想マシンの作成]** ブレードの **[管理]** タブで、次の設定を確認します。

   |設定|値|
   |---|---|
   |ブート診断|**マネージド ストレージ アカウントで有効にする (推奨)**|

9. **[確認と作成]** ブレードで **[確認と作成]** をクリックし、検証が完了したことを確認し、**[作成]** をクリックします。

#### タスク 2:管理サーバーとして使用する仮想マシンを作成します。 

このタスクでは、管理サーバーとして使用する仮想マシンを作成します。

1. Azure portal で、 **[仮想マシン]** ブレードに戻り、 **[+ 作成]** をクリックし、ドロップダウンリストで **[+ Azure 仮想マシン]** をクリックします。

2. **[仮想マシンの作成]** ブレードの **[基本]** タブで、以下の設定を指定します (他の設定は既定値のまま)。

   |設定|値|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|**AZ500LAB07**|
   |仮想マシン名|**myVMMgmt**|
   |リージョン|(US)East US|
   |可用性のオプション|**インフラストラクチャ冗長は必要ありません**
   |セキュリティの種類|**Standard**
   |Image|**Windows Server 2022 Datacenter: Azure Edition - x64 Gen2**|
   |サイズ|**Standard D2s v3**|
   |ユーザー名|**Student**|
   |パスワード|**ラボ 02 > 演習 2 > タスク 1 > 手順 3 で作成した自分の個人用パスワードを使ってください。**|
   |パブリック受信ポート|**なし**|
   |すでに Windows サーバーのライセンスを持っています|**No**|

    >**注**:公開の受信ポートの場合、事前に作成された NSG に依存します。 

4. **[次へ: ディスク >]** をクリックし、 **[仮想マシンの作成]** ブレードの **[ディスク]** タブで、 **[OS ディスクの種類]** を **[Standard HDD]** に設定し、 **[次へ:ネットワーク >]** を選択します。

5. **[仮想マシンの作成]** ブレードの **[ネットワーク]** タブで、以前に作成したネットワーク **myVirtualNetwork** を選択します。

6. **[NIC ネットワーク セキュリティ グループ]** で **[なし]** を選択します。

7. **[次へ: 管理 >]**、**[次へ: 監視 >]** の順にクリックします。 **[仮想マシンの作成]** ブレードの **[管理]** タブで、次の設定を確認します。

   |設定|値|
   |---|---|
   |ブート診断|**マネージド ストレージ アカウントで有効にする (推奨)**|

8. **[確認と作成]** ブレードで **[確認と作成]** をクリックし、検証が完了したことを確認し、**[作成]** をクリックします。

    >**注**:続行する前に、仮想マシンがデプロイされるのを待ちます。 

#### タスク 3:各仮想マシンのネットワーク インターフェイスをアプリケーション セキュリティ グループに関連付けます。

このタスクでは、各仮想マシンのネットワーク インターフェイスを対応するアプリケーション セキュリティ グループに関連付けます。 myVMWeb 仮想マシン インターフェイスは、myAsgWebServers ASG に関連付けられます。 myVMMgmt 仮想マシン インターフェイスは、myAsgMgmtServers ASG に関連付けられます。 

1. Azure portal で、**[仮想マシン]** ブレードに戻り、両方の仮想マシンが **[実行中]** 状態で一覧表示されていることを確認します。

2. 仮想マシンの一覧で、**[myVMWeb]** エントリをクリックします。

3. **[myVMWeb]** ブレードの **[ネットワーク]** セクションで、**[ネットワーク設定]** をクリックしてから、**[myVMWeb \| ネットワーク設定]** ブレードで **[アプリケーション セキュリティ グループ]** タブをクリックします。

4. **[アプリケーション セキュリティ グループの構成]** をクリックし、**[アプリケーション セキュリティグループ]** ドロップダウン リストで **[myAsgWebServers]** を選択してから、**[保存]** をクリックします。

5. **[仮想マシン]** ブレードに戻り、仮想マシンの一覧で、**[myVMMgmt]** エントリをクリックします。

6. **[myVMMgmt]** ブレードの **[設定]** セクションで、 **[ネットワーク]** をクリックしてから、 **[myVMMgmt \| ネットワーク]** ブレードで **[アプリケーションのセキュリティ グループ]** タブをクリックします。

7. **[アプリケーション セキュリティグループの追加]** をクリックし、**[アプリケーション セキュリティグループ]** リストで **[myAsgMgmtServers]** を選択してから、**[保存]** をクリックします。

#### タスク 4:ネットワーク トラフィックのフィルタリングをテストする

このタスクでは、ネット-ワーク トラフィック フィルターをテストします。 myVMMgmnt 仮想マシンに RDP できるはずです。 インターネットから myVMWeb 仮想マシンに接続し、既定の IIS Web ページを表示できるはずです。  

1. **[myVMMgmt]** 仮想マシン ブレードに戻ります。

2. **[myVMMgmt 概要]** ブレードで、**[接続]** をクリックし、ドロップダウン メニューの **[接続]** をクリックします。 

3. RDP ファイルをダウンロードし、それを使用してリモート デスクトップ経由で **myVMMgmt** Azure VM に接続します。 認証を求めるプロンプトが表示されたら、次の認証情報を入力します。

   |設定|値|
   |---|---|
   |ユーザー名|**Student**|
   |パスワード|**ラボ 02 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

    >**注**:リモート デスクトップ接続が正常に完了したことを確認します。 この時点で、リモート デスクトップから myVMMgmt に接続できることを確認しました。

4. Azure portal で、**[myVMWeb]** 仮想マシン ブレードに移動します。

5. **[myVMWeb]** ブレードの **[操作]** セクションで、**[コマンドの実行]** をクリックし、**[RunPowerShellScript]** をクリックします。

6. **[コマンド スクリプトの実行]** ペインで、次のコマンドを実行して、Web サーバーのロールを **myVmWeb** にインストールします。

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**注**: インストールが完了するまで待ちます。 これには数分間かかることがあります。 その時点で、HTTP/HTTPS 経由で myVMWeb にアクセスできることを確認できます。

7. Azure portal で、**[myVMWeb]** ブレードに戻ります。

8. **[myVMWeb]** ブレードで、myVmWeb Azure VM の **[パブリック IP]** アドレスを特定します。

9. 別のブラウザー タブを開き、前の手順で特定した IP アドレスに移動します。

    >**注**:ポート 80 が、**myAsgWebServers** アプリケーション セキュリティ グループの設定に基づいてインターネットからの受信を許可されるため、ブラウザー ページには既定の IIS ウェルカム ページが表示されるはずです。 myVMWeb Azure VM のネットワーク インターフェイスは、そのアプリケーション セキュリティ グループに関連付けられます。 

> 結果:NSG および ASG の設定が機能しており、トラフィックが正しく管理されていることを確認しました。 

**リソースをクリーンアップする**

> 新規に作成し、使用しなくなったすべての Azure リソースを削除することを忘れないでください。 使用していないリソースを削除することで、予期しないコストが発生しなくなります。 

1. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** を選択します。

2. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  **[Cloud Shell]** ペインを閉じます。 
