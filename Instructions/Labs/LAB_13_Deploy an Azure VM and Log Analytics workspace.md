---
lab:
  title: 13 - Azure VM と Log Analytics ワークスペースをデプロイする
  module: Module 04 - Manage security operations
---

# ラボ 13:Azure VM と Log Analytics ワークスペースをデプロイする

# 受講生用ラボ マニュアル

## ラボのシナリオ

Azure 仮想マシンと Log Analytics ワークスペースを作成するよう依頼されました。

- Azure 仮想マシンをデプロイする。
- Log Analytics ワークスペースを作成します。

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Azure 仮想マシンを作成する
  
## 手順

### 演習 1:Azure Virtual Machine をデプロイする

### 演習のタイミング:10 分

この演習では、次のタスクを実行します。 

- タスク 1: Azure 仮想マシンをデプロイする 

#### タスク 1: Azure 仮想マシンをデプロイする

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
    |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

    >**注**: デプロイが完了するまで待ちます。 

7. [Cloud Shell] ペイン内の PowerShell セッションで、次のコマンドを実行して、**myVM** という名前の仮想マシンが作成され、その **[ProvisioningState]** が **[成功]** であることを確認します。

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. [Cloud Shell] ペインを閉じます。 

#### タスク 2:Log Analytics ワークスペースを作成する

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

> 結果:Azure 仮想マシンをデプロイし、Log Analytics ワークスペースを作成しました。

>**注**: Microsoft Defender for Cloud ラボと Microsoft Sentinel ラボで必要なため、このラボからリソースは削除しないでください。
 
