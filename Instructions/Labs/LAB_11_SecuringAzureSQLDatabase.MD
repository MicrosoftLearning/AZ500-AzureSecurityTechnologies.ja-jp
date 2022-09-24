---
lab:
  title: 11 - Azure SQL Database のセキュリティ保護
  module: Module 03 - Secure data and applications
---

# <a name="lab-11-securing-azure-sql-database"></a>ラボ 11:Azure SQL データベースのセキュリティ保護
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to review security features for Azure SQL database. Specifically, you are interested in:

- SQL インジェクションやデータ流出などの攻撃に対する保護。 
- データベース情報を検出し、機密などのカテゴリに分類する機能。 
- データベース サーバーとデータベース クエリを監査し、イベントをログに記録する機能。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:SQL Database のセキュリティ機能を実装する

## <a name="securing-azure-sql-database-diagram"></a>Azure SQL Database のセキュリティ保護の図

![image](https://user-images.githubusercontent.com/91347931/157533836-7250fa58-a109-4882-a55b-d3fa3baf34ab.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>ラボ ファイル：

- **\\Allfiles\\Labs\\11\\azuredeploy.json**

### <a name="exercise-1-implement-sql-database-security-features"></a>演習 1:SQL Database のセキュリティ機能を実装する

### <a name="estimated-timing-30-minutes"></a>推定時間:30 分

この演習では、次のタスクを実行します。

- タスク 1:Azure SQL データベースをデプロイする
- タスク 2:高度なデータ保護の構成
- タスク 3:データ分類を構成する
- タスク 4:監査の構成

#### <a name="task-1-deploy-an-azure-sql-database"></a>タスク 1:Azure SQL データベースをデプロイする

このタスクでは、テンプレートを使用してラボ インフラストラクチャを展開します。 

1. Azure portal ( **`https://portal.azure.com/`** ) にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal で Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**カスタム テンプレートをデプロイする**」と入力し、**Enter** キーを押します。

3. **[カスタム デプロイ]** ブレードで、**[エディターで独自のテンプレートをビルド]** のオプションをクリックします。

4. **[テンプレートの編集]** ブレードで、 **[ファイルの読み込み]** をクリックし、 **\\Allfiles\\Labs\\11\\azuredeploy.json** ファイルを見つけて、 **[開く]** をクリックします。

    >**注**:テンプレートの内容を確認し、Azure SQL データベースをデプロイしていることを確認します。

5. **[テンプレートの編集]** ブレードで、**[保存]** をクリックします。

6. **[カスタム デプロイ]** ブレードで、次の設定が構成されていることを確認します (既定値を使用して他の設定を行います)。

   |設定|[値]|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|**[新規作成]** をクリックし、「**AZ500LAB11**」という名前を入力する|
   |場所|**(米国) 米国東部**|

7. **[確認と作成]** をクリックしてから、**[作成]** をクリックします。

    >**注**: デプロイが完了するまで待ちます。 

#### <a name="task-2-configure-advanced-data-protection"></a>タスク 2:高度なデータ保護の構成

1. Azure portal の、Azure portal ページの上部にある **[ソース、サービス、ドキュメントの検索]** テキスト ボックスで、「**リソース グループ**」と入力し、**Enter** キーを押します。

2. **[リソース グループ]** ブレードのリソース グループの一覧で、 **[AZ500LAB11]** エントリをクリックします。

3. **[AZ500LAB11]** ブレードで、新しく作成された SQL サーバーを表すエントリをクリックします。

4. On the SQL server blade, in the <bpt id="p1">**</bpt>Security<ept id="p1">**</ept> section, click <bpt id="p2">**</bpt>Microsoft Defender for Cloud<ept id="p2">**</ept>. Scroll to the bottom of the <bpt id="p1">**</bpt>Getting started<ept id="p1">**</ept> page and select <bpt id="p2">**</bpt>Upgrade<ept id="p2">**</ept>. On the <bpt id="p1">**</bpt>Install agents<ept id="p1">**</ept> tab select <bpt id="p2">**</bpt>Install agents<ept id="p2">**</ept>.

5. [SQL サーバー] ブレードの **[セキュリティ]** セクションで、 **[Microsoft Defender for Cloud]** をクリックし、 **[Microsoft Defender for SQL を有効にする]** を選択します。

      >**注**:Azure Defender for SQL が正常に有効になったことを示す通知が表示されるまで待ちます。

6. [SQL サーバー] ブレードの **[セキュリティ]** セクションの **[Microsoft Defender for Cloud]** ページにある **[Microsoft Defender for SQL:Enabled at the server-level (Configure)]\(Microsoft Defender for SQL: サーバーレベルで有効化 (構成)\)** パラメーターで、 **[(構成)]** をクリックします。   

      >**注**: **[(構成)]** が表示されない場合は、ブラウザーを更新してください。
    
7. **[サーバー設定]** ブレードで、価格と試用期間、 **[脆弱性評価設定]** 、および **[高度な脅威保護設定]** に関する情報を確認します。

8. **[Microsoft Defender for Cloud]** ブレードに戻り、 **[推奨事項]** と **[セキュリティ アラート]** を確認します。

      ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It may take 10-15 minutes for recommendations to appear on the <bpt id="p2">**</bpt>Microsoft Defender for Cloud<ept id="p2">**</ept> blade. Rather than waiting, proceed to the next task but consider returning to this blade once you complete all the remaining tasks.


#### <a name="task-3-configure-data-classification"></a>タスク 3:データ分類を構成する

このタスクでは、GPDR とデータ保護のコンプライアンスに関する SQL Database 内の情報を検出し、分類します。

1. [SQL サーバー] ブレードの **[設定]** セクションで、 **[SQL データベース]** をクリックします。

2. ポリシーの一覧で、 **[AZ500LabDb]** エントリを選択します。 

3. **[AZ500LabDb** SQL Database] ブレードの **[セキュリティ]** セクションで、 **[データ検出と分類]** をクリックします。

4. **[データ検出と分類]** ブレードで、 **[分類]** タブをクリックします。

    >**注**:分類エンジンによって、機密データが含まれる可能性のある列についてデータベースがスキャンされ、推奨される列の分類のリストが提供されます。

5. ブレードの上部にある青いバーに表示されたテキスト メッセージ **[分類の推奨事項を備えた 15 列が見つかりました]** をクリックします。

6. リストされた列と推奨される秘密度ラベルを確認します。 

7. **[すべて選択]** チェック ボックスをオンにし、 **[選択した推奨事項を受け入れる]** をクリックします。

    >**注**:または、特定の列だけを選択して他の列を無視することができます。 

    >**注**:情報タイプと秘密度ラベルを変更することもできます。 

8. レビューが完了したら、 **[保存]** をクリックします。 

    >**注**:これで分類が完了し、データベース列に新しい分類メタデータが永続的にラベル付けされます。 

9. **[データの検出と分類]** ブレードの **[概要]** タブに戻り、最新の分類情報を考慮して更新されていることを確認してください。 

#### <a name="task-4--configure-auditing"></a>タスク 4:監査の構成 

このタスクでは、まずサーバー レベルの監査を構成し、次にデータベース レベルの監査を構成します。 

1. Azure portal で、[SQL Server] ブレードに戻ります。

2. [SQL Server] ブレードの **[セキュリティ]** セクションで、 **[監査]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is server level auditing. The default auditing settings include all the queries and stored procedures executed against the database, as well as successful and failed logins.

3. **[Azure SQL 監査を有効にする]** スイッチを**オン**に設定して、監査を有効にします。 

4. **[ストレージ]** チェックボックスを選択すると、 **[サブスクリプション]** と **[ストレージ アカウント]** の入力ボックスが表示されます。

5. ドロップダウン リストから **[サブスクリプション]** を選びます。

6. **[ストレージ アカウント]** をクリックして、 **[新規作成]** を選択します。

7. **[ストレージ アカウントの作成]** ブレードの **[名前]** ボックスに、小文字と数字を含めた 3 ~ 24 文字のグローバルな一意の名前を入力し、 **[OK]** をクリックします。 

    >**注**:ストレージ アカウントが使用可能になる前に、ブラウザーの更新が必要になる場合があります。

8. **[監査]** ブレードに戻り、**[詳細プロパティ]** で **[保有期間 (日数)]** を [**5**] に設定します。 

9. [監査] ブレードで、 **[保存]** をクリックして監査の設定を保存します。 

    >Azure SQL データベースのセキュリティ機能を確認するように依頼されました。

10. サーバー ブレードの **[設定]** セクションで、 **[SQL データベース]** をクリックします。

11. ポリシーの一覧で、 **[AZ500LabDb]** エントリを選択します。 

12. **[AZ500LabDb** SQL データベース] ブレードの **[セキュリティ]** セクションで、 **[監査]** をクリックします。 

    >具体的には、次のことに関心があります。 
  
    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Audits can be written to an Azure storage account, to a Log Analytics workspace, or to the Event Hub. You can configure any combination of these options.

    >**注**:サーバーでストレージベースの監査が有効になっている場合、データベースの設定に関係なく、常にデータベースに適用されます。

13. **[監査ログの表示]** をクリックします。

14. **[監査レコード]** ブレードで、サーバー監査とデータベース監査を切り替えることができることに注意してください。 

    >**注**:この SQL サーバーとデータベースは最近作成されたため、この時点でイベントが利用できることはほとんどありません。 

> 結果:SQL サーバーとデータベースを作成し、データ分類を構成し、監査を行いました。 


**リソースをクリーンアップする**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB11" -Force -AsJob
    ```
4. **[Cloud Shell]** ペインを閉じます。 
