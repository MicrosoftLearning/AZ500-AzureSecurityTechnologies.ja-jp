---
lab:
  title: 10 - VM で Just-In-Time アクセスを有効にする
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# ラボ 10: VM で Just-In-Time アクセスを有効にする

# 受講生用ラボ マニュアル

## ラボのシナリオ

金融サービス企業の Azure セキュリティ エンジニアは、重要なアプリケーションをホストする仮想マシン (VM) を含む Azure リソースをセキュリティで保護する責任があります。 セキュリティ チームは、VM への継続的なオープン アクセスにより、総当たり攻撃と未承認アクセスのリスクが高まることを確認しました。 これを軽減するために、最高情報セキュリティ責任者 (CISO) は、財務トランザクションの処理に使用される特定の Azure VM で Just-In-Time (JIT) VM アクセスを有効にするよう要求しました。

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1: Azure portal から VM で JIT を有効にする。

- 演習 2: Azure portal から JIT が有効になっている VM へのアクセスを要求する。

## 演習の手順 

### 演習 1: Azure 仮想マシンから VM で JIT を有効にする

>**注**: Azure portal の Azure 仮想マシンページから、VM 上の JIT を有効にすることができます。

1. ポータルの上部にある検索ボックスに「仮想マシン」と入力します。**** 検索結果から **[仮想マシン]** を選択します。

2. **[myVM]** を選択します。
 
3. **myVM** の **[設定]** セクションから **[構成]** を選択します。
   
4. **[Just-In-Time アクセス]** で、**[Just-In-Time を有効にする]** を選択します。

5. 「**Just-in-time VM アクセス**」で、**[Microsoft Defender for Cloud を開く]** というリンクをクリックします。

6. 既定では、VM の Just-In-Time アクセスでは次の設定が使用されます。

   - Windows マシン
   
     - RDP ポート: 3389
     - 最大許容アクセス: 3 時間
     - 許可されているソース IP アドレス: すべて

   - Linux マシン
     - SSH ポート: 22
     - 最大許容アクセス: 3 時間
     - 許可されているソース IP アドレス: すべて
   
7. 既定では、VM の Just-In-Time アクセスでは次の設定が使用されます。

   - **[構成済み]** タブで、ポートを追加する VM を右クリックして、[編集] を選択します。

   ![image](https://github.com/user-attachments/assets/aa4ded55-c5b1-4d40-b5a0-a4c33b9eb81b)
   
   - **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。
   - ポートの編集が終わったら、 **[保存]** を選択します。   

### 演習 2: Azure 仮想マシンの接続ページから JIT が有効になっている VM へのアクセスを要求する。

>**注**: VM で JIT が有効になっている場合は、VM に接続するためのアクセス権を要求する必要があります。 JIT を有効にした方法に関係なく、サポートされているいずれかの方法でアクセス権を要求できます。
   
1. Azure portal で仮想マシンのページを開きます。

2. 接続先の VM を選択し、 **[接続]** ページを開きます。

   - Azure により、その VM で JIT が有効になっているかどうかが確認されます。

        - VM に対して JIT が有効になっていない場合は、有効にするように求められます。
    
        - JIT が有効になっている場合は、**[アクセス権の要求]** を選択し、要求元の IP、時間の範囲、その VM 用に構成されているポートを指定して、アクセス要求を渡します。
    
   ![image](https://github.com/user-attachments/assets/f5d0b67c-7731-4261-b0eb-a56c505dadd4)

> **結果**: VM で JIT を有効にする方法と、Microsoft Defender for Cloud で JIT が有効になっている VM へのアクセスを要求する方法について、さまざまな方法を確認しました。