---
lab:
  title: 09 - サーバー用に Microsoft Defender for Cloud の強化されたセキュリティ機能を構成する
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# ラボ 09: サーバー用に Microsoft Defender for Cloud の強化されたセキュリティ機能を構成する

# 受講生用ラボ マニュアル

## ラボのシナリオ

グローバルな e コマース企業の Azure セキュリティ エンジニアは、会社のクラウド インフラストラクチャをセキュリティで保護する責任があります。 組織は、重要なアプリケーションの実行、顧客データの管理、トランザクションの処理のために、Azure Virtual Machines (VM) とオンプレミス サーバーに大きく依存しています。 最高情報セキュリティ責任者 (CISO) は、サイバー脅威、脆弱性、構成ミスからこれらのリソースを保護するための強化されたセキュリティ対策の必要性を特定しました。 Microsoft Defender for Cloud で Microsoft Defender for Servers を有効にして、Azure VM とハイブリッド サーバーの両方に高度な脅威に対する保護とセキュリティ監視を提供する作業を行いました。

## ラボの目的

- サーバー用の Microsoft Defender for Cloud セキュリティ強化機能を設定する
  
- Microsoft Defender for Servers プラン 2 の強化されたセキュリティ機能を確認する

## 演習の手順

### サーバー用の Microsoft Defender for Cloud セキュリティ強化機能を設定する

1. Azure portal において、Azure portal ページの上部にある [リソース、サービス、ドキュメントの検索] テキスト ボックスに、「**Microsoft Defender for Cloud**」と入力し、**Enter** キーを押します。

2. **Microsoft Defender for Cloud** の **[管理] ブレード**で、**[環境設定]** に移動します。 **サブスクリプション** セクションが表示されるまで環境設定フォルダーを展開し、**サブスクリプション**をクリックして詳細を表示します。

   ![image](https://github.com/user-attachments/assets/3b25dd82-e09e-4f8a-b85e-c9bc6c4bd488)
   
3. **[設定]** ブレードの **[Defender プラン]** で、**[クラウド ワークロード保護 (CWP)]** を展開します。

4. **クラウド ワークロード保護 (CWP) プラン**の一覧から **[サーバー]** を選択します。 ページの右側で、**[状態]** を **[オフ]** から **[オン]** に変更し、**[保存]** をクリックします。

5. **Microsoft Defender for Servers プラン 2** の詳細を確認するには、**[プランの変更]** を選択します。

   注: クラウド ワークロード保護 (CWP) サーバー プランをオフからオンにすると、Microsoft Defender for Servers プラン 2 が有効になります。
 
   ![image](https://github.com/user-attachments/assets/de434a75-345a-4023-83f1-fa53fcb5f288)
   
> **結果**: サブスクリプションで Microsoft Defender for Servers プラン 2 が有効になりました。
