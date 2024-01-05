---
lab:
  title: 09 - Microsoft Defender for Cloud
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# ラボ 09:Microsoft Defender for Cloud
# 受講生用ラボ マニュアル

## ラボのシナリオ

あなたは、Microsoft Defender for Cloud ベースの環境の概念実証を作成するよう依頼されました。 具体的には、次のことを行います。

- 仮想マシンを監視するように Microsoft Defender for Cloud を構成します。
- 仮想マシンに関する Microsoft Defender for Cloud の推奨事項を確認します。
- ゲスト構成と Just-In-Time VM アクセスの推奨事項を実装します。 
- セキュア スコアを使用して、より安全なインフラストラクチャの作成に向けた進捗状況を判断する方法を確認します。

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Microsoft Defender for Cloud を実装する

## Microsoft Defender for Cloud の図

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## Instructions

### 演習 1:Microsoft Defender for Cloud を実装する

この演習では、次のタスクを実行します。

- タスク 1:Microsoft Defender for Cloud を構成する
- タスク 2:Microsoft Defender for Cloud の推奨事項を確認する
- タスク 3:Just-In-Time VM アクセスを有効にするために Microsoft Defender for Cloud の推奨事項を実装する

#### タスク 1:Microsoft Defender for Cloud を構成する

このタスクでは、Microsoft Defender for Cloud をオンボードして構成します。

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、Azure portal ページの上部に「**Microsoft Defender for Cloud**」と入力し、**Enter** キーを押します。

3. 左ナビゲーション パネルで、 **[作業の開始]** をクリックします。 **[Microsoft Defender for Cloud \| 開始]** ブレードで、 **[アップグレード]** をクリックします。
     
4. **[Microsoft Defender for Cloud \| 開始]** ブレードの [エージェントのインストール] タブで下へスクロールして **[エージェントのインストール]** をクリックします。 

5. **[Microsoft Defender for Cloud \| 開始]** ブレードの **[アップグレード]** タブで **[強化されたセキュリティ機能を含むワークスペースの選択]** セクションが表示されるまでスクロール ダウンし、お使いの Log Analytics ワークスペースを選択して **[Microsoft Defender プラン]** をオンにしてから大きな青の更新ボタンをクリックします。  

    >**注**:Microsoft Defender プランの一部として使用できるすべての機能を確認します。 

6. **[Microsoft Defender for Cloud]** に移動し、[管理] セクションの下にある左側のナビゲーション パネルで **[環境の設定]** をクリックします。

7. **[Microsoft Defender for Cloud \| 環境の設定]** ブレードで下へスクロールし、サブスクリプションが表示されるまで展開して、関連するサブスクリプションをクリックします。 

8. **[設定 \| Defender プラン]** ブレードで **[すべてのプランを有効にする]** を選択し、必要に応じて **[保存]** をクリックします。

9. **[Microsoft Defender for Cloud \| 環境の設定]** ブレードに戻り、サブスクリプションが表示されるまで展開して、前のラボで作成した Log Analytics ワークスペースを表すエントリをクリックします。

10. **[設定 \| Defender プラン]** ブレードですべてのオプションが "オン" になっていることを確認します。 必要に応じて、 **[すべてのプランを有効にする]** をクリックし、 **[保存]** をクリックします。

11. **[設定 \| Defender プラン]** ブレードから **[データ収集]** を選択します。 **[すべてのイベント]** 、 **[保存]** の順にクリックします。

#### タスク 2:Microsoft Defender for Cloud の推奨事項を確認する

このタスクでは、Microsoft Defender for Cloud の推奨事項を確認します。 

1. Azure portal で、 **[Microsoft Defender for Cloud \| 概要]** ブレードに戻ります。 

2. **[Microsoft Defender for Cloud \| 概要]** ブレードで、 **[セキュア スコア]** タイルを確認します。

    >**注**:あれば、現在のスコアを記録します。

3. **[Microsoft Defender for Cloud \| 概要]** ブレードに戻り、 **[評価済みリソース]** をクリックします。

4. **[インベントリ]** ブレードで、 **[myVM]** エントリをクリックします。

    >**注**:エントリが表示されるまで数分待ってから、ブラウザ ページを更新する必要がある場合があります。
    
5. **[リソース正常性]** ブレードの **[推奨事項]** タブで、**myVM** の推奨事項のリストを確認します。

#### タスク 3:Just-In-Time VM アクセスを有効にするために Microsoft Defender for Cloud の推奨事項を実装する

このタスクでは、仮想マシンでの Just-In-Time VM アクセスを有効にする Microsoft Defender for Cloud の推奨事項を実装します。 

1. Azure portal で **[Microsoft Defender for Cloud \| 概要]** ブレードに戻り、左側のナビゲーション パネルで **[クラウド セキュリティ]** の下の **[ワークロード保護]** をクリックします。

2. **[Microsoft Defender for Cloud \| ワークロード保護]** ブレードで、 **[高度な保護]** セクションまで下へスクロールし、 **[Just-In-Time VM アクセス]** タイルをクリックします。

3. **[Just-In-Time VM アクセス]** ブレードの **[仮想マシン]** セクションで **[未構成]** を選択し、 **[myVM]** エントリのチェックボックスをオンにします。

    >**注**: 数分待ってからブラウザー ページを更新し、 **[未構成]** を再選択すると、エントリが表示される場合があります。

4. **[仮想マシン]** セクションの右端にある **[1 つの VM で JIT を有効にする]** オプションをクリックします。

5. **[JIT VM アクセス構成]** ブレードの、ポート **22** を参照する行の右端で、省略記号ボタンをクリックしてから、**[削除]** をクリックします。

6. **[JIT VM アクセス構成]** ブレードで、**[保存]** をクリックします。

    >**注**: ツールバーの **[通知]** アイコンをクリックして、**[通知]** ブレードを表示して、構成の進行状況を監視します。 

    >**注**:このラボでの推奨事項の実装がセキュア スコアに反映されるまでに時間がかかる場合があります。 セキュア スコアを定期的にチェックして、これらの機能の実装による影響を判断します。 

> 結果:Microsoft Defender for Cloud にオンボードし、仮想マシンの推奨事項を実装しています。 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
