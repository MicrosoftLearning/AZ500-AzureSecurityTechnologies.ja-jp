---
lab:
  title: 14 - Microsoft Defender for Cloud
  module: Module 04 - Microsoft Defender for Cloud
---

# <a name="lab-14-microsoft-defender-for-cloud"></a>ラボ 14:Microsoft Defender for Cloud
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

あなたは、Microsoft Defender for Cloud ベースの環境の概念実証を作成するよう依頼されました。 具体的には、次のことを行います。

- 仮想マシンを監視するように Microsoft Defender for Cloud を構成します。
- 仮想マシンに関する Microsoft Defender for Cloud の推奨事項を確認します。
- ゲスト構成と Just In Time VM アクセスの推奨事項を実装します。 
- セキュア スコアを使用して、より安全なインフラストラクチャの作成に向けた進捗状況を判断する方法を確認します。

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

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

3. まだ完了されていない場合は、 **[Microsoft Defender for Cloud \| 開始]** ブレードで、 **[アップグレード]** をクリックします。
     
4. まだ完了されていない場合は、 **[Microsoft Defender for Cloud \| 開始]** ブレードの **[エージェントのインストール]** タブで、下にスクロールして **[エージェントのインストール]** をクリックします。

5. **[Microsoft Defender for Cloud \| 開始]** ブレードの **[アップグレード]** タブ >> **[Select workspaces with enhanced security features](強化されたセキュリティ機能を含むワークスペースの選択)** セクション >> お使いの Log Analytics ワークスペースを選択して **[Microsoft Defender plan](Microsoft Defender プラン)** を有効にします。 

    >**注**:Microsoft Defender プランの一部として使用できるすべての機能を確認します。 

6. **[Microsoft Defender for Cloud]** に移動し、左側にある垂直メニュー バーで、[管理] 設定の下にある **[環境の設定]** をクリックします。

7. **[Microsoft Defender for Cloud \| 環境の設定]** ブレードで、関連するサブスクリプションをクリックします。 

8. **[Defender プラン]** ブレードで、 **[すべての Microsoft Defender for Cloud プランの有効化]** を選択します。

9. **[Microsoft Defender for Cloud \| 環境の設定]** ブレードに戻り、サブスクリプションを展開して、前のラボで作成した Log Analytics ワークスペースを表すエントリをクリックします。

10. **[設定 \| Defender プラン]** ブレードで、 **[すべての Microsoft Defender for Cloud プランの有効化]** を選択します。

11. **[Microsoft Defender for Cloud \| 設定]** ブレードから **[データ コレクション]** を選択します。 **[すべてのイベント]** 、 **[保存]** の順に選択します。

#### <a name="task-2-review-the-microsoft-defender-for-cloud-recommendation"></a>タスク 2:Microsoft Defender for Cloud の推奨事項を確認する

このタスクでは、Microsoft Defender for Cloud の推奨事項を確認します。 

1. Azure portal で、 **[Microsoft Defender for Cloud \| 概要]** ブレードに戻ります。 

2. **[Microsoft Defender for Cloud \| 概要]** ブレードで、 **[セキュア スコア]** タイルを確認します。

    >**注**:あれば、現在のスコアを記録します。

3. **[Microsoft Defender for Cloud \| 概要]** ブレードに戻り、 **[評価済みリソース]** を選択します。

4. **[インベントリ]** ブレードで、**[myVM]** エントリを選択します。

    >**注**:エントリが表示されるまで数分待ってから、ブラウザ ページを更新する必要がある場合があります。
    
5. **[リソース正常性]** ブレードの **[推奨事項]** タブで、**myVM** の推奨事項のリストを確認します。

#### <a name="task-3-implement-the-microsoft-defender-for-cloud-recommendation-to-enable-just-in-time-vm-access"></a>タスク 3:Just In Time VM アクセスを有効にするために Microsoft Defender for Cloud の推奨事項を実装する

このタスクでは、仮想マシンでの Just In Time VM アクセスを有効にする Microsoft Defender for Cloud の推奨事項を実装します。 

1. Azure portal で、 **[Microsoft Defender for Cloud | 概要]** ブレードに戻り、 **[クラウド セキュリティ]** タイルの **[ワークロード保護]** を選択します。

2. **[ワークロード保護]** ブレードの **[高度な保護]** セクションで **[Just-In-Time VM アクセス]** タイルをクリックし、 **[Just-In-Time VM アクセス]** ブレードに移動します。

3. **[Just In Time VM アクセス]** ブレードの **[仮想マシン]** セクションで、 **[未構成]** を選択し、**myVM** エントリをクリックします。

4. **[仮想マシン]** セクションの右端にある **[1 つの VM で JIT を有効にする]** オプションをクリックします。

    >**注**:**myVM** エントリが利用可能になるまで、数分待つ必要があるかもしれません。

5. **[JIT VM アクセス構成]** ブレードの、ポート **22** を参照する行の右端で、省略記号ボタンをクリックしてから、**[削除]** をクリックします。

6. **[JIT VM アクセス構成]** ブレードで、**[保存]** をクリックします。

    >**注**: ツールバーの **[通知]** アイコンをクリックして、**[通知]** ブレードを表示して、構成の進行状況を監視します。 

    >**注**:このラボでの推奨事項の実装がセキュア スコアに反映されるまでに時間がかかる場合があります。 セキュア スコアを定期的にチェックして、これらの機能の実装による影響を判断します。 

> 結果:Microsoft Defender for Cloud にオンボードし、仮想マシンの推奨事項を実装しています。 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
