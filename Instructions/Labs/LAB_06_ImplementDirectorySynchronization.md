---
lab:
  title: 06 - ディレクトリ同期を導入する
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-06-implement-directory-synchronization"></a>ラボ 06:ディレクトリ同期を導入する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

オンプレミスの Active Directory Domain Services (AD DS) 環境を Azure Active Directory (Azure AD) テナントに統合する方法を示す概念実証を作成するよう依頼されました。 具体的には、次のことを行います。

- AD DS ドメイン コントローラーをホストする Azure VM をデプロイして単一ドメイン AD DS フォレストを導入する
- Azure AD テナントを作成および構成する
- AD DS フォレストを Azure AD テナントと同期する

> このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 これがクラスで使用するリージョンであることを講師に確認します。 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする
- 演習 2:Azure Active Directory テナントを作成および構成する
- 演習 3:Active Directory フォレストを Azure Active Directory テナントと同期する

## <a name="implement-directory-synchronization"></a>ディレクトリ同期を導入する

![image](https://user-images.githubusercontent.com/91347931/157525374-8f740f14-c2db-47b3-98f8-7feb9bc122b5.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-deploy-an-azure-vm-hosting-an-active-directory-domain-controller"></a>演習 1:Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする

### <a name="estimated-timing-10-minutes"></a>推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:Azure VM のデプロイで利用可能な DNS 名を特定する
- タスク 2:ARM テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする

#### <a name="task-1-identify-an-available-dns-name-for-an-azure-vm-deployment"></a>タスク 1:Azure VM のデプロイで利用可能な DNS 名を特定する

このタスクでは、Azure VM デプロイの DNS 名を識別します。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

2. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。 メッセージが表示されたら、**[PowerShell]** と **[ストレージの作成]** をクリックします。

3. [Cloud Shell] ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

4. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、この演習の次のタスクで Azure VM のデプロイに使用できる利用可能な DNS 名を特定します:

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**注**:`<custom-label>` プレースホルダーを、グローバルに一意である可能性が高い有効な DNS 名に置き換えます。 `<location>` プレースホルダーを、このラボで使用する Active Directory ドメイン コントローラーをホストする Azure VM をデプロイするリージョンの名前に置き換えます。

    >**注**:Azure VM をプロビジョニングできる Azure リージョンを特定するには、[ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/) を参照してください

5. コマンドが **True** を返したことを確認します。 そうでない場合は、コマンドが **True** を戻すまで、異なる値の `<custom-label>` で同じコマンドを再実行します。

6. 成功した結果をもたらした `<custom-label>` の値を記録します。 これは、次のタスクで必要になります。

7. Cloud Shell を閉じます。

#### <a name="task-2-use-an-arm-template-to-deploy-an-azure-vm-hosting-an-active-directory-domain-controller"></a>タスク 2:ARM テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする

このタスクでは、Active Directory ドメイン コントローラーをホストする Azure VM をデプロイします

1. 同じブラウザー ウィンドウで別のブラウザー タブを開き、 **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain** に移動します。

2. **[新しい Windows VM を作成し、新しい AD フォレスト、ドメイン、および DC を作成する]** ページで、**[Azure に配置する]** をクリックします。 これにより、ブラウザーが Azure portal の **[新しい AD フォレストで Azure VM を作成する]** ブレードに自動的にリダイレクトされます。

3. **[新しい AD フォレストで Azure VM を作成する]** ブレードで、**[パラメーターの編集]** をクリックします。

4. **[パラメーターの編集]** ブレードで、 **[ファイルの読み込み]** をクリックし、 **[開く]** ダイアログ ボックスで、フォルダー **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json** に移動し、 **[開く]** をクリックして、 **[保存]** をクリックします。

5. **[新しい AD フォレストで Azure VM を作成する]** ブレードで、次の設定を指定します (他の設定は既存の値のままにします)。

   |設定|値|
   |---|---|
   |サブスクリプション|Azure サブスクリプションの名前|
   |Resource group|**[新規作成]** をクリックし、「**AZ500LAB06**」という名前を入力します|
   |リージョン|前のタスクで特定した Azure リージョン|
   |管理ユーザー名|**学生**|
   |管理パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|
   |ドメイン名|**adatum.com**|
   |DNS プレフィックス|前のタスクで識別した DNS ホスト名|
   |VM サイズ|**Standard_D2s_v3**|

6. **[新しい AD フォレストで Azure VM を作成する]** ブレードで、**[確認および作成]** をクリックし、**[作成]** をクリックします。

    >**注**:デプロイが完了するのを待たず、代わりに次の演習に進みます。 デプロイには約 15 分間かかります。 このラボの 3 番目の演習では、このタスクでデプロイされた仮想マシンを使用します。

> 結果:この演習を完了した後、Azure Resource Manager テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure VM のデプロイを開始しました。


### <a name="exercise-2-create-and-configure-an-azure-active-directory-tenant"></a>演習 2:Azure Active Directory テナントを作成および構成する 

### <a name="estimated-timing-20-minutes"></a>推定時間:20 分

この演習では、次のタスクを実行します。

- タスク 1:Azure Active Directory (AD) テナントを作成する
- タスク 2:新しい Azure AD テナントにカスタム DNS 名を追加する
- タスク 3:グローバル管理者の役割を持つ Azure AD ユーザーを作成する

#### <a name="task-1-create-an-azure-active-directory-ad-tenant"></a>タスク 1:Azure Active Directory (AD) テナントを作成する

このタスクでは、このラボで使用する新しい Azure AD テナントを作成します。 

1. Azure portal の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、Azure portal ページの上部に「**Azure Active Directory**」と入力し、**Enter** キーを押します。

2. 現在の Azure AD テナントの **[概要]** を表示しているブレードで、 **[テナントの管理]** をクリックし、次の画面で **[+ 作成]** をクリックします。

3. **テナントの作成** ブレードの **基本** タブで、オプション **Azure Active Directory** が選択されていることを確認し、**次へ:構成 >** をクリックします。

4. **[ディレクトリの作成]** ブレードの **[構成]** タブで、次の設定を指定します。

   |設定|値|
   |---|---|
   |組織名|**AdatumSync**|
   |初期ドメイン名|文字と数字の組み合わせから成る一意の名前|
   |国/リージョン|**米国**|

    >**注**:初期ドメイン名を記録します。 このラボで後ほど必要になります。

    >**注**:**[初期ドメイン名]** テキスト ボックスの緑色のチェック マークは、入力したドメイン名が有効で一意であるかどうかを示します。 (後で使用するために、初期ドメイン名を記録してください)。

5. **[Review + create](レビュー + 作成)** をクリックし、 **[作成]** をクリックします。

    >**注**:新しいテナントが作成されるのを待ちます。 **通知** アイコンを使用して、デプロイ ステータスを監視します。 

#### <a name="task-2-add-a-custom-dns-name-to-the-new-azure-ad-tenant"></a>タスク 2:新しい Azure AD テナントにカスタム DNS 名を追加する

このタスクでは、新しい Azure AD テナントにカスタム DNS 名を追加します。 

1. Azure portal のツール バーで、Cloud Shell アイコンの右側にある **[ディレクトリ + サブスクリプション]** アイコンをクリックします。 

2. **[Directory + subscription]** ブレードで、新しく作成したテナント **AdatumSync** の行を選択し、**切り替え**ボタンをクリックします。

    >**注**:**AdatumSync** エントリが **Directory + subscription** のフィルター リストに表示されない場合は、ブラウザーの画面を更新する必要があります。

3. **[AdatumSync \| Azure Active Directory]** ブレードの **[管理]** セクションで、 **[カスタム ドメイン名]** をクリックします。

4. **[AdatumSync \| カスタム ドメイン名]** ブレードで、 **[+ カスタム ドメインの追加]** をクリックします。

5. **[カスタム ドメイン名]** ブレードの **[カスタム ドメイン名]** テキスト ボックスに「**adatum.com**」と入力し、**[ドメインの追加]** をクリックします。

6. **adatum.com** ブレードで、Azure AD ドメイン名の検証を実行するために必要な情報を確認し、 **[削除]** を 2 回選択します。 

    >**注**:**adatum.com** DNS ドメイン名を所有していないため、検証プロセスを完了できません。 これにより **adatum.com** AD DS ドメインと Azure AD テナントとの同期が妨げになるわけではありません。 この目的のために、前のタスクで特定した Azure AD テナントの最初の DNS 名 (**onmicrosoft.com** サフィックスで終わる名前) を使用します。 ただし、その結果、AD DS ドメインの DNS ドメイン名と Azure AD テナントの DNS 名は異なることを覚えておいてください。 つまり、AD DS ドメインにサインインするときと Azure AD テナントにサインインするときに、Adatum ユーザーは異なる名前を使用する必要があります。

#### <a name="task-3-create-an-azure-ad-user-with-the-global-administrator-role"></a>タスク 3:グローバル管理者の役割を持つ Azure AD ユーザーを作成する

このタスクでは、新しい Azure AD ユーザーを追加し、グローバル管理者の役割に割り当てます。 

1. **[AdatumSync]** Azure AD テナント ブレードの **[管理]** セクションで、**[ユーザー]** をクリックします。

2. **[ユーザー \| すべてのユーザー]** ブレードで、 **[+ 新しいユーザー]** をクリックします。 

3. **[新しいユーザー]** ブレードで、**[ユーザーの作成]** オプションが選択されていることを確認し、次の設定を指定し (その他の設定はすべて既定値のままにします)、**[作成]** をクリックします。

   |設定|値|
   |---|---|
   |ユーザー名|**syncadmin**|
   |名前|**syncadmin**|
   |パスワード|**[パスワードの自動生成]** オプションが選択されていることを確認し、 **[パスワードの表示]** をクリックします|
   |グループ|**0 個のグループが選択されました**|
   |ロール|**[ユーザー]** をクリックし、 **[グローバル管理者]** をクリックし、 **[選択]** をクリックします|
   |利用場所|**米国**|  

    >**注**:完全なユーザー名を記録します。 ドメイン名が表示されているドロップダウン リストの右側にある **[クリップボードにコピー]** ボタンをクリックすると、値をコピーできます。 

    >**注**:ユーザーのパスワードを記録します。 これは、このラボの後半で必要になります。 

    >**注**:Azure AD Connect を導入するには、グローバル管理者の役割を持つ Azure AD ユーザーが必要です。

4. InPrivate ブラウザー ウィンドウを開きます。

5. Azure portal に移動し **syncadmin** ユーザー アカウントを使用してサインインします。 プロンプトが表示されたら、このタスクで前に記録したパスワードを **Pa55w.rd1234** に変更します。

    >**注**:サインインするには、このタスクの前半で記録した Azure AD テナントの DNS ドメイン名を含む、**syncadmin** ユーザー アカウントの完全修飾名を指定する必要があります。 このユーザー名の形式は、syncadmin@`<your_tenant_name>`.onmicrosoft.com です。`<your_tenant_name>` は、一意の Azure AD テナント名を表すプレースホルダーです。 

6. **syncadmin** としてサインアウトし、InPrivate ブラウザーの画面を閉じます。

> **結果**: この演習を完了した結果、Azure AD テナントが作成され、新しい Azure AD テナントにカスタム DNS 名が追加され、グローバル管理者ロールを持つ Azure AD ユーザーが作成されます。


### <a name="exercise-3-synchronize-active-directory-forest-with-an-azure-active-directory-tenant"></a>演習 3:Active Directory フォレストを Azure Active Directory テナントと同期する

### <a name="estimated-timing-20-minutes"></a>推定時間:20 分

この演習では、次のタスクを実行します。

- タスク 1:ディレクトリ同期用の AD DS を準備する
- タスク 2:Azure AD Connect をインストールします。
- タスク 3:ディレクトリ同期を確認する

#### <a name="task-1-prepare-ad-ds-for-directory-synchronization"></a>タスク 1:ディレクトリ同期用の AD DS を準備する

このタスクでは、AD DS ドメイン コントローラーを実行している Azure VM に接続し、ディレクトリ同期アカウントを作成します。 

   > このタスクを開始する前に、このラボの最初の演習で開始した Template deployment が正常に完了したことを確認します。

1. Azure portal で、このラボの最初の演習で Azure VM をデプロイした Azure サブスクリプションに関連付けられている Azure AD テナントに **Directory + subscription** フィルターを設定します。

2. Azure portal の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、Azure portal ページの上部に「**Virtual Machines**」と入力し、**Enter** キーを押します。

3. **[Virtual Machines]** ブレードで **adVM** エントリをクリックします。 

4. **[adVM]** ブレードで **[接続]** をクリックし、ドロップ ダウン メニューの **[RDP]** をクリックします。 

5. **[IP アドレス]** パラメーターで、**[ロード バランサーのパブリック IP アドレス]** を選択し、**[RDP ファイルのダウンロード]** をクリックして、リモート デスクトップ経由で **adVM** Azure VM に接続します。 認証を求められたら、次の資格情報を入力します。

   |設定|値|
   |---|---|
   |ユーザー名|**学生**|
   |パスワード|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|

    >**注**:リモート デスクトップ セッションと **サーバー マネージャー** が読み込まれるまで待ちます。  

    >**注**:**adVM** Azure VM へのリモート デスクトップ セッションでは、次の手順を実行します。 

6. **サーバー マネージャー**で **[ローカル サーバー]** をクリックし、**[IE セキュリティ強化の構成]** をクリックします。

7. **[Internet Explorer セキュリティ強化の構成]** ダイアログ ボックスで、両方のオプションを **[オフ]** に設定し、**[OK]** をクリックします。

8. Internet Explorer を起動して、 **https://www.microsoft.com/en-us/edge/business/download** に移動します。Microsoft Edge インストール バイナリをダウンロードし、インストールを実行し、既定の設定で Web ブラウザーを構成します。

9. **サーバー マネージャー** で、**[ツール]** をクリックし、ドロップダウン メニューの **[Active Directory 管理センター]** をクリックします。

10. **Active Directory 管理センター**で、 **[adatum (ローカル)]** をクリックし、 **[タスク]** ペインで、ドメイン名 **[adatum (ローカル)]** の下にある **[新規作成]** をクリックして、カスケード メニューで **[組織単位]** をクリックします。

11. **[組織単位の作成]** ウィンドウの **[名前]** テキスト ボックスに「**ToSync**」と入力し、**[OK]** をクリックします。

12. 新しくクレートされた **ToSync** 組織単位をダブルクリックして、Active Directory 管理センター コンソールの詳細ペインにその内容が表示されるようにします。 

13. **[タスク]** ペインの **[ToSync]** セクションで、**[新規作成]** をクリックし、カスケード メニューの **[ユーザー]** をクリックします。

14. **[ユーザーの作成]** ウィンドウで、次の設定で新しいユーザー アカウントを作成し (他の設定は既存の値のままにします)、**[OK]** をクリックします。

   |設定|値|
   |---|---|
   |[名前]|**aduser1**|
   |[ユーザー UPN ログオン]|**aduser1**|
   |[ユーザー SAM アカウント名ログオン]|**aduser1**|
   |[パスワード] と [パスワードの確認入力]|**ラボ 04 > 演習 1 > タスク 1 > 手順 9 で作成した個人用パスワードを使用してください。**|
   |その他のパスワード オプション|**パスワードを無期限にする**|

#### <a name="task-2-install-azure-ad-connect"></a>タスク 2:Azure AD Connect をインストールします。

このタスクでは、仮想マシンに AD Connect をインストールします。 

1. **adVM** へのリモート デスクトップ セッション内で、Microsoft Edge を使って Azure portal ( **https://portal.azure.com** ) に移動し、前の演習で作成した **syncadmin** ユーザー アカウントを使ってサインインします。 メッセージが表示されたら、記録した完全なユーザー名と **Pa55w.rd1234** パスワードを指定します。

2. Azure portal の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、Azure portal ページの上部に「**Azure Active Directory**」と入力し、**Enter** キーを押します。

3. Azure portal の **[AdatumSync \| 概要]** ブレードで、 **[Azure AD Connect]** をクリックします。

4. **[AdatumSync \| Azure AD Connect]** ブレードで、 **[Azure AD Connect のダウンロード]** リンクをクリックします。 **[Microsoft Azure Active Directory Connect]** ダウンロード ページにリダイレクトされます。

5. **[Microsoft Azure Active Directory Connect]** ダウンロード ページで、**[ダウンロード]** をクリックします。

6. メッセージが表示されたら、**[実行]** をクリックして、**Microsoft Azure Active Directory Connect** ウィザードを開始します。

7. **Microsoft Azure Active Directory Connect** ウィザードの **[Azure AD Connect へようこそ]** ページで、**[ライセンス条項とプライバシーに関する通知に同意する]** チェック ボックスをオンにし、**[続行]** をクリックします。

8. **Microsoft Azure Active Directory Connect** ウィザードの **[簡単設定]** ページで、**[カスタマイズ]** オプションをクリックします。

9. **[必須コンポーネントのインストール]** ページで、オプションの構成オプションをすべて解除したままにして、**[インストール]** をクリックします。

10. **[ユーザー サインイン]** ページで、**[パスワード ハッシュ同期]** のみが有効になっていることを確認し、**[次へ]** をクリックします。

11. **[Azure AD に接続]** ページで、前の演習で作成した **syncadmin** ユーザー アカウントの資格情報を使用して認証し、**[次へ]** をクリックします。 

12. **[ディレクトリの接続]** ページで、**adatum.com** フォレスト エントリの右側にある **[ディレクトリの追加]** ボタンをクリックします。

13. **[AD フォレスト アカウント]** ウィンドウで、**[新しい AD アカウントを作成]** オプションが選択されていることを確認し、次の資格情報を指定して、**[OK]** をクリックします。

   |設定|値|
   |---|---|
   |[ユーザー名]|**ADATUM\\Student**|
   |パスワード|**ラボ 06 > 演習 1 > タスク 2 で作成した個人用パスワードを使用してください**|

14. **[ディレクトリの接続]** ページに戻り、**adatum.com** エントリが構成されたディレクトリとして表示されていることを確認し、**[次へ]** をクリックします。

15. **[Azure AD サインインの構成]** ページで、**UPN サフィックスが検証済みのドメイン名と一致しない場合は、ユーザーがオンプレミスの資格情報を使用して Azure AD にサインインできないことを示す**警告を確認し、**[一部の UPN サフィックスが確認済みドメインに一致していなくても続行する]** チェック ボックスをオンにし、**[次へ]** をクリックします。

    >**注**:前述したように、カスタムの Azure AD DNS ドメイン **adatum.com** を確認できなかったため、これが予想されます。

16. **[ドメインと OU のフィルタリング]** ページで、 **[選択したドメインと OU の同期]** オプションをクリックします。ドメイン名 **adatum.com** がオンになり、 **[adatum.com]** を展開して **[ToSync]** を表示します。 すべてのチェックボックスをオフにし、 **[ToSync]** OU の横にあるチェックボックスのみをクリックして、 **[次へ]** をクリックします。

17. **[ユーザーを一意に識別]** ページで、既定の設定を受け入れ、**[次へ]** をクリックします。

18. **[ユーザーおよびデバイスのフィルタリング]** ページで、既定の設定を受け入れて、**[次へ]** をクリックします。

19. **[オプション機能]** ページで、既定の設定を受け入れ、**[次へ]** をクリックします。

20. **[構成の準備完了]** ページで、**[構成が完了したら、同期プロセスを開始する]** チェック ボックスがオンになっていることを確認し、**[インストール]** をクリックします。

    >**注**:インストールにはおよそ 2 分かかります。

21. **[構成が完了しました]** ページの情報を確認し、**[終了]** をクリックして **[Microsoft Azure Active Directory Connect]** ウィンドウを閉じます。


#### <a name="task-3-verify-directory-synchronization"></a>タスク 3:ディレクトリ同期を確認する

このタスクでは、ディレクトリ同期が機能していることを確認します。 

1. **adVM** へのリモート デスクトップ セッション内の、Azure portal を表示する Microsoft Edge ウィンドウで、Adatum Lab Azure AD テナントの **[ユーザー - すべてのユーザー (プレビュー)]** ブレードに移動します。

2. **[ユーザー \| すべてのユーザー (プレビュー)]** ブレードで、ユーザー オブジェクトのリストに **aduser1** アカウントが含まれていることを確認してください。 

>**注**: **aduser1** ユーザー アカウントが表示されるまで、数分待ってから **[更新]** を選択する必要がある場合があります。

3. **aduser1** アカウントを選択し、**[プロファイル > ID]** セクションで、**[ソース]** 属性が **Windows Server AD** に設定されていることを確認してください。

4. **[aduser1 \| プロファイル]** ブレードの **[ジョブ情報]** セクションで、 **[部署]** 属性が設定されていないことに注意してください。

5. **adVM** へのリモート デスクトップ セッション内で、**Active Directory 管理センター**に切り替え、**ToSync** OU のオブジェクトのリストで **aduser1** エントリを選択し、 **[タスク]** ペインの **[aduser1]** セクションで、 **[プロパティ]** を選択します。

6. **[aduser1]** ウィンドウの **[組織]** セクションの **[部署]** テキスト ボックスに「**売上**」と入力し、**[OK]** をクリックします。

7. **adVM ** へのリモート デスクトップ セッション内で、**Windows PowerShell** を起動します。

8. **[管理者:Windows PowerShell]** コンソールから、次を実行して、Azure AD Connect の差分同期を開始します。

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. **[aduser1 \| プロファイル]** ブレードが表示されている Microsoft Edge ウィンドウに切り替え、ページを更新して、 **[部署]** プロパティが **[売上]** に設定されていることを確認します。

    >**注**: **[部署]** 属性が設定されていない場合は、もう 1 分待ってからページを再度更新する必要がある場合があります。

> **Result**:この演習を完了したら、ディレクトリ同期用の AD DS を準備し、Azure AD Connect をインストールし、ディレクトリ同期を検証しました。


**リソースをクリーンアップする**

>**注**:Azure AD 同期を無効にして開始する

1. **adVM** へのリモート デスクトップ セッション内で、管理者として Windows PowerShell を起動します。

2. Windows PowerShell コンソールから、次のコマンドを実行して MsOnline PowerShell モジュールをインストールします (メッセージが表示されたら、[続行するには NuGet プロバイダーが必要です] ダイアログ ボックスで、「**はい**」と入力して Enter キーを押します)。

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. Windows PowerShell コンソールから、次のコマンドを実行して AdatumSync Azure AD テナントに接続します (メッセージが表示されたら、**syncadmin** の資格情報でサインインします)。

    ```powershell
    Connect-MsolService
    ```

4. Windows PowerShell コンソールから、次のコマンドを実行して Azure AD Connect の同期を無効にします。

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. Windows PowerShell コンソールで、次の操作を実行して操作が正常に完了したことを確認します。

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**注**:結果は `False` となるはずです。 そうでない場合は、少し待ってからコマンドを再実行してください。

    >**注**:次に、Azure リソースを削除します
6. リモート デスクトップ セッションを終了します。

7. Azure portal で、**Directory + Subscription** フィルターを、**adVM** Azure VM をデプロイした Azure サブスクリプションに関連付けられている Azure AD テナントに設定します。

8. Azure portal から、Azure portal の右上にあるアイコンをクリックして、Cloud Shell を開きます。 

9. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** を選択し、メッセージが表示されたら **[確認]** をクリックします。 

10. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. **[Cloud Shell]** ペインを閉じます。

    >**注**:最後に、Azure AD テナントを削除します。
    
    >**注 2**:テナントの削除は非常に難しいプロセスであるため、誤ってまたは悪意を持って削除されることはありません。  つまり、このラボの一部としてテナントを削除しても、うまくいかないことがよくあります。  ここにはテナントを削除する手順がありますが、このラボを完了したと考える必要はありません。 現実の世界でテナントを削除する必要がある場合は、DOCS.Microsoft.com に役立つ記事があります。

12. Azure portal に戻り、**Directory + Subscription** フィルターを使用して **AdatumSync** Azure Active Directory テナントに切り替えます。

13. Azure portal で、 **[ユーザー - すべてのユーザー]** ブレードに移動し、**syncadmin** ユーザー アカウントを表すエントリをクリックし、 **[syncadmin - プロファイル]** ブレードで **[削除]** をクリックして、確認を求められたら、 **[はい]** をクリックします。

14. 同じ一連の手順を繰り返して、**aduser1** ユーザー アカウントと**オンプレミスのディレクトリ同期サービス アカウント**を削除します。

15. Azure AD テナントの **[AdatumSync - 概要]** ブレードに移動し、 **[テナントの管理]** をクリックして **AdatumSync** ディレクトリのチェック ボックスをオンにし、 **[削除]** をクリックして、 **[テナント 'AdatumSync' の削除]** ブレードで **[Azure リソースを削除する権限を取得]** のリンクをクリックし、Azure Active Directory の **[プロパティ]** ブレードで **[Azure リソースのアクセス管理]** を **[はい]** に設定して、 **[保存]** をクリックします。

    >**注**:削除中に **[すべてのユーザーを削除する]** のような警告記号が表示された場合は、作成したユーザーの削除に進みます。または、警告記号に **[Delete LinkedIn applications](LinkedIn アプリケーションの削除)** と表示された場合は、警告メッセージをクリックして LinkedIn アプリケーションの削除を確認します。テナントの削除を完了するには、すべての警告に対処する必要があります。

16. Azure portal からサインアウトし、サインインし直します。 

17. **[テナント 'AdatumSync' の削除]** ブレードに戻り、 **[削除]** をクリックします。

> このタスクに関する追加情報については、[https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto) を参照してください
