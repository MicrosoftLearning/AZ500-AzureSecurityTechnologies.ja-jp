---
lab:
  title: 14 - Microsoft Defender for Cloud
  module: Module 04 - Microsoft Defender for Cloud
---

# <a name="lab-14-microsoft-defender-for-cloud"></a>ラボ 14:Microsoft Defender for Cloud
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to create a proof of concept of Microsoft Defender for Cloud-based environment. Specifically, you want to:

- 仮想マシンを監視するように Microsoft Defender for Cloud を構成します。
- 仮想マシンに関する Microsoft Defender for Cloud の推奨事項を確認します。
- ゲスト構成と Just In Time VM アクセスの推奨事項を実装します。 
- セキュア スコアを使用して、より安全なインフラストラクチャの作成に向けた進捗状況を判断する方法を確認します。

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Microsoft Defender for Cloud を実装する

## <a name="microsoft-defender-for-cloud-diagram"></a>Microsoft Defender for Cloud の図

![image](https://user-images.githubusercontent.com/91347931/157537800-94a64b6e-026c-41b2-970e-f8554ce1e0ab.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-implement-microsoft-defender-for-cloud"></a>演習 1:Microsoft Defender for Cloud を実装する

この演習では、次のタスクを実行します。

- タスク 1:Microsoft Defender for Cloud を構成する
- タスク 2:Microsoft Defender for Cloud の推奨事項を確認する
- タスク 3:Just In Time VM アクセスを有効にするために Microsoft Defender for Cloud の推奨事項を実装する

#### <a name="task-1-configure-microsoft-defender-for-cloud"></a>タスク 1:Microsoft Defender for Cloud を構成する

このタスクでは、Microsoft Defender for Cloud をオンボードして構成します。

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、Azure portal ページの上部に「**Microsoft Defender for Cloud**」と入力し、**Enter** キーを押します。

3. まだ完了されていない場合は、 **[Microsoft Defender for Cloud | 開始]** ブレードで、 **[アップグレード]** をクリックします。
     
4. まだ完了されていない場合は、 **[Microsoft Defender for Cloud | 開始]** ブレードの **[エージェントのインストール]** タブで、下にスクロールして **[エージェントのインストール]** をクリックします。

5. **[Microsoft Defender for Cloud | 開始]** ブレードの **[アップグレード]** タブ >> **[強化されたセキュリティ機能を含むワークスペースの選択]** セクション >> お使いの Log Analytics ワークスペースを選択して **[Microsoft Defender プラン]** を有効にします。 

    >**注**:Microsoft Defender プランの一部として使用できるすべての機能を確認します。 

6. **[Microsoft Defender for Cloud]** に移動し、左側にある垂直メニュー バーで、[管理] 設定の下にある **[環境の設定]** をクリックします。

7. **[Microsoft Defender for Cloud | 環境の設定]** ブレードで、関連するサブスクリプションをクリックします。 

8. **[Defender plans]\(Defender プラン\)** タブで、 **[すべての Microsoft Defender for Cloud プランの有効化]** を選択し、 **[保存]** をクリックします。

9. **[設定 | Defender プラン]** ブレードの左側にある垂直方向のメニュー バーで、 **[自動プロビジョニング]** をクリックします。 

10. **[設定 | 自動プロビジョニング]** ブレードで、最初の項目である **[Azure VM の Log Analytics エージェント]** の自動プロビジョニングが **[オン]** に設定されていることを確認します。

11. **[設定 | ワークフローの自動化]** ブレードで、使用可能な設定を確認します。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can trigger actions based threat detection alerts and Microsoft Defender for Cloud recommendations. You can also configure an action based on Logic apps. 
    
12. **[ワークフロー自動化の追加]** ブレードで、使用可能な設定を確認します。

    >注:Microsoft Defender for Cloud では、システムの更新状況、OS セキュリティ構成、エンドポイント保護など、仮想マシンに関する多くの分析情報が提供されます。

13. **[ワークフロー自動化の追加]** ブレードで、**[キャンセル]** をクリックします。

14. **[Microsoft Defender for Cloud | 環境の設定]** ブレードに戻り、サブスクリプションを展開して、前のラボで作成した Log Analytics ワークスペースを表すエントリをクリックします。

15. **[設定 | Defender プラン]** ブレードで、 **[すべての Microsoft Defender for Cloud プランの有効化]** が選択されていることを確認し、 **[保存]** をクリックします。

16. Select <bpt id="p1">**</bpt>Data collection<ept id="p1">**</ept> from the <bpt id="p2">**</bpt>Microsoft Defender for Cloud | Settings<ept id="p2">**</ept> blade. Select <bpt id="p1">**</bpt>All Events<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Save<ept id="p2">**</ept>.


#### <a name="task-2-review-the-microsoft-defender-for-cloud-recommendation"></a>タスク 2:Microsoft Defender for Cloud の推奨事項を確認する

このタスクでは、Microsoft Defender for Cloud の推奨事項を確認します。 

1. Azure portal で、 **[Microsoft Defender for Cloud | 概要]** ブレードに戻ります。 

2. **[Microsoft Defender for Cloud | 概要]** ブレードで、 **[セキュア スコア]** タイルを確認します。

    >**注**:あれば、現在のスコアを記録します。

3. **[Microsoft Defender for Cloud | 概要]** ブレードに戻り、 **[評価済みリソース]** を選択します。

4. **[インベントリ]** ブレードで、**[myVM]** エントリを選択します。

    >**注**:エントリが表示されるまで数分待ってから、ブラウザ ページを更新する必要がある場合があります。
    
5. **[リソース正常性]** ブレードの **[推奨事項]** タブで、**myVM** の推奨事項のリストを確認します。


#### <a name="task-3-implement-the-microsoft-defender-for-cloud-recommendation-to-enable-just-in-time-vm-access"></a>タスク 3:Just In Time VM アクセスを有効にするために Microsoft Defender for Cloud の推奨事項を実装する

このタスクでは、仮想マシンでの Just In Time VM アクセスを有効にする Microsoft Defender for Cloud の推奨事項を実装します。 

1. Azure portal で、 **[Microsoft Defender for Cloud | 概要]** ブレードに戻り、 **[クラウド セキュリティ]** タイルの **[ワークロード保護]** を選択します。

2. **[ワークロード保護]** ブレードの **[高度な保護]** セクションで **[Just-In-Time VM アクセス]** タイルをクリックし、 **[Just-In-Time VM アクセス] ブレード**で **[Just In Time VM アクセスを試す]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If the VMs are not listed, navigate to <bpt id="p2">**</bpt>Virtual Machine<ept id="p2">**</ept> blade and click the <bpt id="p3">**</bpt>Configuration<ept id="p3">**</ept>, Click the <bpt id="p4">**</bpt>Enable the Just-in-time VMs<ept id="p4">**</ept> option under the <bpt id="p5">**</bpt>Just-in-time Vm's access<ept id="p5">**</ept>. Repeat the above step to navigate back to the <bpt id="p1">**</bpt>Microsoft Defender for Cloud<ept id="p1">**</ept> and refresh the page, the VM will appear.

3. **[Just In Time VM アクセス]** で、**[未構成]** を選択し、**myVM** エントリをクリックします。

    >**注**:**myVM** エントリが利用可能になるまで、数分待つ必要があるかもしれません。

4. **[1 つの VM で JIT を有効にする]** を選択します。

5. **[JIT VM アクセス構成]** ブレードの、ポート **22** を参照する行の右端で、省略記号ボタンをクリックしてから、**[削除]** をクリックします。

6. **[JIT VM アクセス構成]** ブレードで、**[保存]** をクリックします。

    >**注**: ツールバーの **[通知]** アイコンをクリックして、**[通知]** ブレードを表示して、構成の進行状況を監視します。 

    >あなたは、Microsoft Defender for Cloud ベースの環境の概念実証を作成するよう依頼されました。 

> 結果:Microsoft Defender for Cloud にオンボードし、仮想マシンの推奨事項を実装しています。 

    >**Note**: Do not remove the resources from this lab as they are needed for the Azure Sentinel lab.
