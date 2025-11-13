---
lab:
  title: 08 - Log Analytics ワークスペースとデータ収集ルール (DCR) を作成する
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# ラボ 08: Log Analytics ワークスペースとデータ収集ルール (DCR) を作成する

# 受講生用ラボ マニュアル

## ラボのシナリオ

金融テクノロジ企業の Azure セキュリティ エンジニアは、財務トランザクションの処理や顧客の機密データの管理に使用されるすべての Azure Virtual Machine (VM) の監視とセキュリティの可視性を強化する必要があります。 セキュリティ チームは、潜在的な脅威を検出し、システム パフォーマンスを最適化するために、これらの VM からの詳細なログとパフォーマンス指標が必要です。 最高情報セキュリティ責任者 (CISO) から、セキュリティ イベント、システム ログ、およびパフォーマンス カウンターを収集するソリューションを実施するよう求められました。 ログの収集とパフォーマンスの監視を一元化するために、Azure Monitor エージェント (AMA) とデータ収集ルール (DCR) を構成する任務を与えられました。



> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Azure Virtual Machine をデプロイする
- 演習 2:Log Analytics ワークスペースを作成する
- 演習 3:Azure ストレージ アカウントを作成する
- 演習 4:データ収集ルールを作成する。
  
## 手順

### 演習 1:Azure Virtual Machine をデプロイする

### 演習のタイミング:10 分

この演習では、次のタスクを実行します。 

- タスク 1:Azure 仮想マシンをデプロイする。 

#### タスク 1: Azure 仮想マシンをデプロイする

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** を選択します。

3. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

4. [Cloud Shell] ペイン内の PowerShell セッションで、次の手順を実行して、このラボで使用するリソース グループを作成します。
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**注**: このリソース グループは、ラボ 8、9、および 10 に使用されます。

5. Cloud Shell ウィンドウ内の PowerShell セッションで次を実行して、ホスト (EAH) で暗号化を有効にします。
   
   ```powershell
    Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace Microsoft.Compute 
    ```

5. [Cloud Shell] ペイン内の PowerShell セッションで、次の手順を実行して、新しい Azure 仮想マシンを作成します。 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  資格情報の入力を求められた場合:

    |設定|値|
    |---|---|
    |User |**localadmin**|
    |Password|**ラボ 02 > 演習 2 > タスク 1 > 手順 3 で作成した個人用パスワードを使用してください。**|

    >**注**: デプロイが完了するまで待ちます。 

7. [Cloud Shell] ペイン内の PowerShell セッションで、次のコマンドを実行して、**myVM** という名前の仮想マシンが作成され、その **[ProvisioningState]** が **[成功]** であることを確認します。

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. [Cloud Shell] ペインを閉じます。 

### 演習 2:Log Analytics ワークスペースを作成する

### 演習のタイミング:10 分

この演習では、次のタスクを実行します。 

- タスク 1:Log Analytics ワークスペースを作成します。

#### タスク 1:Log Analytics ワークスペースを作成する

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

### 演習 3:Azure ストレージ アカウントを作成する

### 推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:Azure ストレージ アカウントを作成します。

#### タスク 1:Azure ストレージ アカウントを作成する

このタスクでは、ストレージ アカウントを作成します。

1. Azure portal の「**リソース、サービス、ドキュメントの検索**」テキスト ボックスで、Azure portal ページの上部に「**ストレージ アカウント**」と入力し、**Enter** キーを押します。

2. Azure portal の **[ストレージ アカウント]** ブレードで、**[+ 作成]** ボタンをクリックして新しいストレージ アカウントを作成します。

    ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/73eb9241-d642-455a-a1ff-b504670395c0)

3. 「**ストレージ アカウントの作成**」ブレードの「**基本**」タブで、次の設定を指定します (他の設定は既定値のままにします)。

    |設定|値|
    |---|---|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB131415**|
    |ストレージ アカウント名|文字と数字で構成される、長さが 3 から 24 のグローバルに一意の名前|
    |場所|**(米国) EastUS**|
    |パフォーマンス|**Standard (汎用 v2 アカウント)**|
    |冗長性|**ローカル冗長ストレージ (LRS)**|

4. **[ストレージ アカウントの作成]** ブレードの **[基本]** タブで、**[確認]** をクリックし、検証プロセスが完了するまで待ってから、**[作成]** をクリックします。

     ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d443821c-2ddf-4794-87fa-bfc092980eba)

    >**注**:ストレージ アカウントが作成されるのを待ちます。 これには 2 分ほどかかります。

### 演習 3:データ収集ルールを作成する

### 推定時間:15 分

この演習では、次のタスクを実行します。

- タスク 1:データ収集ルールを作成する。

#### タスク 1:データ収集ルールを作成する。

このタスクでは、データ収集ルールを作成します。

1. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスに「**監視**」と入力し、**Enter** キーを押します。

2. **[監視設定]** ブレードで、 **[データ収集ルール]** をクリックします。

  ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d43e8f94-efb2-4255-9320-210c976fd45e)


3. **[+ 作成]** をクリックして新しいデータ収集ルールを作成します。

4. **[データ収集ルールの作成]** ブレードの **[基本]** タブで、以下の設定を指定します。
  
    |設定|値|
    |---|---|
    |**ルールの詳細**|
    |規則の名前|**DCR1**|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB131415**|
    |リージョン|**米国東部**|
    |プラットフォームの種類|**Windows**|
    |データ収集エンドポイント|*空白のままにします*|

    ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/9b58c4ce-b7a8-4acf-8289-d95b270a6083)


4. **[次へ: リソース >]** というラベルの付いたボタンをクリックして次に進みます。
   
6. [リソース] タブで、**[+ リソースの追加]** を選択してから、**[データ収集エンドポイントを有効にする]** をチェックします。 [スコープ テンプレートの選択] で、**[AZ500LAB131415]** をチェックして、**[適用]** をクリックします。

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d4191115-11bc-43ec-9bee-e84b9b95a821)

10. **[次へ: 収集と配信 >]** というラベルの付いたボタンをクリックして次に進みます。

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8294d300-f910-4757-ad52-43c7594ac822)

11. **[+ データ ソースの追加]** をクリックしてから、**[データ ソースの追加]** ページで、**[データ ソースの種類]** ドロップダウン メニューを変更して **[パフォーマンス カウンター]** を表示します。 以下の既定の設定はそのままにします。

    |設定|値|
    |---|---|
    |**パフォーマンス カウンター**|**サンプル レート (秒)**|
    |CPU|60|
    |メモリ|60|
    |ディスク|60|
    |ネットワーク|60|

   ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/a24e44ad-1d10-4533-80e2-bae1b3f6564d)

11. **[次へ: 移行先 >]** というラベルの付いたボタンをクリックして次に進みます。
  
12. **[+ 宛先の追加]** をクリックし、**[宛先の種類]** ドロップダウン メニューを変更して **[Azure Monitor ログ]** を表示します。 **[サブスクリプション]** ウィンドウで、"サブスクリプション" が表示されていることを確認してから、前に作成した Log Analytics ワークスペースを反映するように **[アカウントまたは名前空間]** ドロップダウン メニューを変更します。**

   ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/481843f5-94c4-4a8f-bf51-a10d49130bf8)

11. ページ下部の **[データ ソースの追加]** をクリックします。
    
    ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/964091e7-bbbc-4ca8-8383-bb2871a1e7f0)

13. **[Review + create](レビュー + 作成)** をクリックします。

    ![画像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/50dd8407-a106-4540-9e14-ae40a3c04830)

14. **Create** をクリックしてください。

> 結果:Azure 仮想マシン、Log Analytics ワークスペース、Azure ストレージ アカウント、データ収集ルールをデプロイして、Azure Monitor エージェントを使用して仮想マシンからイベントとパフォーマンス カウンターを収集しました。

>**注**: このラボからリソースを削除しないでください。それらのリソースは、Microsoft Defender for Cloud ラボ、「VM で Just-In-Time アクセスを有効にする」ラボ、および Microsoft Sentinel ラボで必要です
 
