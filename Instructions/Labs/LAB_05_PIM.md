---
lab:
  title: 05 - Azure AD Privileged Identity Management
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-05-azure-ad-privileged-identity-management"></a>ラボ 05:Azure AD Privileged Identity Management
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to create a proof of concept that uses Azure Privileged Identity Management (PIM) to enable just-in-time administration and control the number of users who can perform privileged operations. The specific requirements are:

- aaduser2 Azure AD ユーザーのセキュリティ管理者の役割への永続的な割り当てを作成します。 
- aaduser2 Azure AD ユーザーが課金管理者の役割とグローバル閲覧者の役割の対象となるように構成します。
- Aaduser3 Azure AD ユーザーの承認を要求するようにグローバル閲覧者の役割のアクティブ化を構成します。
- グローバル閲覧者の役割のアクセス レビューを構成し、監査機能を確認します。

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

> Before you proceed, ensure that you have completed Lab 04: MFA, Conditional Access and AAD Identity Protection . You will need the Azure AD tenant, AdatumLab500-04, and the aaduser1, aaduser2, and aaduser3 user accounts.

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:PIM ユーザーとロールを構成します。
- 演習 2:承認の有無にかかわらず、PIM ロールをアクティブ化します。
- 演習 3:アクセス レビューを作成し、PIM 監査機能を確認します。

## <a name="azure-ad-privileged-identity-management-diagram"></a>Azure AD Privileged Identity Management の図

![image](https://user-images.githubusercontent.com/91347931/157522920-264ce57e-5c55-4a9d-8f35-e046e1a1e219.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1---configure-pim-users-and-roles"></a>演習 1: PIM ユーザーとロールを構成する

#### <a name="estimated-timing-15-minutes"></a>推定時間:15 分

この演習では、次のタスクを実行します。

- タスク 1:ユーザーをロールの対象にします。
- タスク 2:有資格メンバーをアクティブ化して追加する承認を要求するようにロールを構成します。
- タスク 3:ユーザーに永続的なロールを割り当てます。 

#### <a name="task-1-make-a-user-eligible-for-a-role"></a>タスク 1:ユーザーをロールの対象にする

このタスクでは、ユーザーに Azure AD ディレクトリ ロールの資格を与えます。

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Ensure that you are signed-in to the <bpt id="p2">**</bpt>AdatumLab500-04<ept id="p2">**</ept> Azure AD tenant. You can use the <bpt id="p1">**</bpt>Directory + subscription<ept id="p1">**</ept> filter to switch between Azure AD tenants. Ensure you are signed in as a user with the Global Administrator role.
    
    >**注**:それでも AdatumLab500-04 エントリが表示されない場合は、[ディレクトリの切り替え] リンクをクリックし、AdatumLab500-04 行を選択して、[切り替え] ボタンをクリックします。

2. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで「**Azure AD Privileged Identity Management**」と入力し、**Enter** キーを押します。

3. **[Privileged Identity Management]** ブレードの **[管理]** セクションで、 **[Azure AD ロール]** をクリックします。

4. **[AdatumLab500-04 \| クイック スタート]** ブレードの **[管理]** セクションで、 **[ロール]** をクリックします。

5. **[AdatumLab500-04 \| ロール]** ブレードで、 **[+ 割り当ての追加]** をクリックします。

6. **[割り当ての追加]** ブレードの **[ロールの選択]** ドロップダウンで、**[課金管理者]** を選択します。

7. **[メンバーが選択されていません]** リンクをクリックし、**[メンバーの選択]** ブレードで **[aaduser2]** をクリックし、**[選択]** をクリックします。

8. **[割り当ての追加]** ブレードに戻り、**[次へ]** をクリックします。 

9. **[割り当ての種類]** が **[有資格]** に設定されていることを確認し、**[割り当て]** をクリックします。
 
10. **[AdatumLab500-04 \| ロール]** ブレードに戻り、 **[管理]** セクションで、 **[割り当て]** をクリックします。

11. **[AdatumLab500-04 \| 割り当て]** ブレードに戻り、 **[資格のある割り当て]** 、 **[アクティブな割り当て]** 、 **[期限切れの割り当て]** の各タブを確認します。

12. **[資格のある割り当て]** タブで、**[aaduser2]** が **[課金管理者]** として表示されていることを確認します。 

    >**注**:サインイン時に、aaduser2 は課金管理者ロールを使用する資格があります。 

#### <a name="task-2-configure-a-role-to-require-approval-to-activate-and-add-an-eligible-member"></a>タスク 2:有資格メンバーをアクティブ化して追加する承認を要求するようにロールを構成する

1. Azure portal で、 **[Privileged Identity Management]** ブレードに戻り、 **[Azure AD ロール]** をクリックします。

2. **[AdatumLab500-04 \| クイック スタート]** ブレードの **[管理]** セクションで、 **[ロール]** をクリックします。

3. **[AdatumLab500-04 \| ロール]** ブレードで、 **[グローバル閲覧者]** のロール エントリをクリックします。 

4. **[グローバル閲覧者 \| 割り当て]** ブレードで、ブレードのツール バーにある **[設定]** アイコンをクリックし、Azure Multi-Factor Authentication 要件を含むロールの構成設定を確認します。

5. **[編集]** をクリックします。

6. **[アクティブ化]** タブで、**[アクティブ化するには承認が必要]** チェック ボックスをオンにします。

7. **[メンバーの選択]** ブレードで、**[承認者の選択]** をクリックし、**[aaduser3]** をクリックした後、**[選択]** をクリックします。

8. **[次へ: 割り当て]** をクリックします。

9. **[永続的な資格のある割り当てを許可する]** チェック ボックスをオフにし、その他のすべての設定は既定値のままにします。

10. **[次へ: 通知]** をクリックします。

11. **[通知]** 設定を確認し、すべてを既定で設定されているままにして、 **[更新]** をクリックします。

    >**注**:グローバル閲覧者ロールを使用しようとする人は、aaduser3 の承認が必要になります。 

12. **[グローバル閲覧者 \| 割り当て]** ブレードで、 **[+ 割り当ての追加]** をクリックします。

13. **[割り当ての追加]** ブレードで、**[メンバーが選択されていません]** をクリックし、**[メンバーの選択]** ブレードで **[aaduser2]** をクリックしてから、**[選択]** をクリックします。

14. **[次へ]** をクリックします。 

15. **[割り当ての種類]** が **[対象]** であることを確認し、対象期間の設定を確認します。

16. **[割り当て]** をクリックします。

    >**注**:ユーザー aaduser2 はグローバル閲覧者ロールの対象となります。 
 
#### <a name="task-3-give-a-user-permanent-assignment-to-a-role"></a>タスク 3:ユーザーに永続的なロールを割り当てます。

1. Azure portal で、 **[Privileged Identity Management]** ブレードに戻り、 **[Azure AD ロール]** をクリックします。

2. **[AdatumLab500-04 \| クイック スタート]** ブレードの **[管理]** セクションで、 **[ロール]** をクリックします。

3. **[AdatumLab500-04 \| ロール]** ブレードで、 **[+ 割り当ての追加]** をクリックします。

4. **[割り当ての追加]** ブレードの **[ロールの選択]** ドロップダウンで、**[セキュリティ管理者]** を選択します。

5. **[割り当ての追加]** ブレードで、**[メンバーが選択されていません]** をクリックし、**[メンバーの選択]** ブレードで **[aaduser2]** をクリックして、**[選択]** をクリックします。

6. **[次へ]** をクリックします。 

7. **[割り当ての種類]** の設定を確認し、**[割り当て]** をクリックします。

8. On the <bpt id="p1">**</bpt>Assignments<ept id="p1">**</ept> page on the <bpt id="p2">**</bpt>Eligible Assignments<ept id="p2">**</ept> tab, select <bpt id="p3">**</bpt>Update<ept id="p3">**</ept> for the <bpt id="p4">**</bpt>aaduser2<ept id="p4">**</ept> assignment. Select <bpt id="p1">**</bpt>Permanently Eligible<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Save<ept id="p2">**</ept>.

    >**注**:これで、ユーザー aaduser2 は、永久的にセキュリティ管理者ロールの資格を持つようになりました。
    
### <a name="exercise-2---activate-pim-roles-with-and-without-approval"></a>演習 2 - 承認の有無にかかわらず PIM ロールをアクティブ化する

#### <a name="estimated-timing-15-minutes"></a>推定時間:15 分

この演習では、次のタスクを実行します。

- タスク 1:承認を必要としないロールをアクティブ化します。 
- タスク 2:承認が必要なロールをアクティブ化します。 

#### <a name="task-1-activate-a-role-that-does-not-require-approval"></a>タスク 1:承認を必要としないロールをアクティブ化します。

このタスクでは、承認を必要としないロールをアクティブ化します。

1. InPrivate ブラウザー ウィンドウを開きます。

2. InPrivate ブラウザー画面で、Azure portal に移動し、**aaduser2** ユーザー アカウントを使用してサインインします。

    >Azure Privileged Identity Management (PIM) を使用してジャストインタイム管理を有効にし、特権操作を実行できるユーザー数を制御する概念実証を作成するように依頼されました。 

3. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで「**Azure AD Privileged Identity Management**」と入力し、**Enter** キーを押します。

4. **[Privileged Identity Management]** ブレードの **[タスク]** セクションで、 **[自分のロール]** をクリックします。

5. **aaduser2** の 3 つの**資格のあるロール**が表示されます。**グローバル閲覧者**、**セキュリティ管理者**および**課金管理者**。 

6. **[課金管理者]** のロール エントリが表示されている行で、**[アクティブ化]** をクリックします。

7. 必要な場合は、警告 **[追加検証が必要です。クリックして続行します]** をクリックし、指示に従って、本人確認を行います。

8. **[アクティブ化 - 課金管理者]** ブレードの **[理由]** テキスト ボックスにアクティブ化の理由を示すテキストを入力し、**[アクティブ化]** をクリックします。

    >**注**:PIM でロールをアクティブ化する場合、有効化が有効となるまでに最大 10 分かかることがあります。 
    
    >**注**:ロールの割り当てがアクティブになると、ブラウザーが更新されます (問題が発生した場合は、サインアウトして、**aaduser2** ユーザー アカウントを使用して Azure portal に再度サインインします)。

9. **[Privileged Identity Management]** ブレードに戻り、 **[タスク]** セクションで **[自分のロール]** をクリックします。

10. **[自分のロール \| Azure AD ロール]** ブレードで、**[アクティブな割り当て]** タブに切り替えます。**[課金管理者ロール]** が **[アクティブ化]** になっていることに注目してください。

    >**注**:ロールがアクティブ化されると、 **[終了時刻]** (有効期間) の下の制限時間に達した時点で、自動的に非アクティブ化されます。

    >**注**:管理者のタスクを早期に完了した場合、ロールを手動で非アクティブ化することもできます。

11.  **[アクティブなロール]** のリストで、課金管理者割り当てを表す行で、**[非アクティブ化]** リンクをクリックします。

12.  **[非アクティブ化 - 課金管理者]** ブレードで、再度 **[非アクティブ化]** をクリックして確定します。


#### <a name="task-2-activate-a-role-that-requires-approval"></a>タスク 2:承認が必要なロールをアクティブ化します。 

このタスクでは、承認が必要なロールをアクティブ化します。

1. InPrivate ブラウザー ウィンドウ内の Azure portal で、**aaduser2** ユーザーとしてサインインしている間に、 **[Privileged Identity Management \| クイック スタート]** ブレードに戻ります。 

2. **[Privileged Identity Management \| クイック スタート]** ブレードの **[タスク]** セクションで、 **[自分のロール]** をクリックします。

3. **[自分のロール \| Azure AD ロール]** ブレードの **[資格のある割り当て]** の一覧で、 **[グローバル閲覧者]** ロールが表示された行で、 **[アクティブ化]** をクリックします。 

4. **[アクティブ化 - グローバル閲覧者]** ブレードの **[理由]** テキスト ボックスに、アクティブ化の理由を示すテキストを入力し、**[アクティブ化]** をクリックします。

5. Azure portal ツール バーの **[通知]** アイコンをクリックし、要求が承認待ちであることを知らせる通知を表示します。

    >**注**:特権ロール管理者として、いつでも要求を確認および取り消すことができます。 

6. **[自分のロール \| Azure AD ロール]** ブレードで、 **[セキュリティ管理者]** のロールを見つけて、 **[アクティブ化]** をクリックします。 

7. 警告 **[追加検証が必要です。クリックして続行します]** をクリックします。 

8. 指示に従って、本人確認を行います。

    >**注**:セッションごとに 1 回だけ認証する必要があります。 

9. Azure portal インターフェイスに戻ったら、**[アクティブ化 - セキュリティ管理者]** ブレードの **[理由]** テキスト ボックスに、アクティブ化の理由を示すテキストを入力し、**[アクティブ化]** をクリックします。

    >**注**:自動承認プロセスが完了するはずです。

10. **[自分のロール \| Azure AD ロール]** ブレードに戻り、 **[アクティブな割り当て]** タブをクリックすると、**アクティブな割り当て**の一覧には**セキュリティ管理者**が含まれますが、**グローバル閲覧者**ロールは含まれていないことがわかります。

    >**注**:これでグローバル閲覧者ロールを承認します。

11. Azure portal から **aaduser2** としてサインアウトします。

12. **aaduser3** としてAzure portal にサインインします。

    >**注**:いずれかのユーザー アカウントを使用した認証で問題が発生した場合は、自身のユーザー アカウントを使用してAzure AD テナントにサインインし、パスワードをリセットするか、サインイン オプションを再構成できます。

13. Azure portal で、**[Azure AD Privileged Identity Management]** に移動します (Azure portal ページの上部にある [リソース、サービス、ドキュメントの検索] テキスト ボックスで「Azure AD Privileged Identity Management」と入力し、Enter キーを押します)。

14. **[Privileged Identity Management \| クイック スタート]** ブレードの **[タスク]** セクションで、 **[申請の承認]** をクリックします。

15. **[申請の承認 \| Azure AD ロール]** ブレードの **[ロールのアクティブ化の要求]** セクションで、**aaduser2** による **[グローバル閲覧者]** ロールへのロールのアクティブ化要求を表すエントリのチェックボックスをオンにします。

16. 具体的な要件は次のとおりです。 

    >**注**:リクエストを拒否することもできます。

17. Azure portal から **aaduser3** としてサインアウトします。

18. Azure portal に **aaduser2** としてサインインします。

19. Azure portal で、**Azure AD Privileged Identity Management** に移動します (Azure portal ページの上部にある [リソース、サービス、ドキュメントの検索] テキスト ボックスで「Azure AD Privileged Identity Management」と入力し、Enter キーを押します)。

20. **[Privileged Identity Management \| クイック スタート]** ブレードの **[タスク]** セクションで、 **[自分のロール]** をクリックします。

21. **[自分のロール \| Azure AD ロール]** ブレードで、 **[アクティブな割り当て]** タブをクリックし、グローバル閲覧者ロールがアクティブになっていることを確認します。

    >**注**:更新されたアクティブな割り当ての一覧を表示するには、ページを更新する必要がある場合があります。

22. サインアウトし、InPrivate ブラウザーの画面を閉じます。

> 結果:承認有りと無しで、PIM ロールの有効化を練習しました。 

### <a name="exercise-3---create-an-access-review-and-review-pim-auditing-features"></a>演習 3 - アクセス レビューを作成し、PIM 監査機能を確認する

#### <a name="estimated-timing-10-minutes"></a>推定時間:10 分

この演習では、次のタスクを実行します。

- タスク 1:PIM で Azure AD ディレクトリロールのセキュリティアラートを構成する
- タスク 2:PIM アラート、サマリ情報、詳細な監査情報の確認

#### <a name="task-1-configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>タスク 1:PIM で Azure AD ディレクトリロールのセキュリティアラートを構成する

In this task, you will reduce the risk associated with "stale" role assignments. You will do this by creating a PIM access review to ensure that assigned roles are still valid. Specifically, you will review the Global Reader role. 

1. ご自分のアカウントを使用して Azure portal **`https://portal.azure.com/`** にサインインします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Ensure that you are signed-in to the <bpt id="p2">**</bpt>AdatumLab500-04<ept id="p2">**</ept> Azure AD tenant. You can use the <bpt id="p1">**</bpt>Directory + subscription<ept id="p1">**</ept> filter to switch between Azure AD tenants. Ensure you are signed in as a user with the Global Administrator role.
    
    >**注**:それでも AdatumLab500-04 エントリが表示されない場合は、[ディレクトリの切り替え] リンクをクリックし、AdatumLab500-04 行を選択して、[切り替え] ボタンをクリックします。

2. Azure portal で、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで「**Azure AD Privileged Identity Management**」と入力し、**Enter** キーを押します。

3. **[Privileged Identity Management]** ブレードに移動します。 

4. **[Privileged Identity Management \| クイック スタート]** ブレードの **[管理]** セクションで、 **[Azure AD ロール]** をクリックします。

5. **[AdatumLab500-04 \| クイック スタート]** ブレードの **[管理]** セクションで、 **[アクセス レビュー]** をクリックします。

6. **[AdatumLab500-04 \| アクセス レビュー]** ブレードで、 **[新規]** をクリックします。

7. **[アクセス レビューの作成]** ブレードで、次の設定を指定します (他の設定は既定値のままにします)。 

   |設定|値|
   |---|---|
   |レビュー名|**グローバル閲覧者のレビュー**|
   |開始日|今日の日付| 
   |頻度|**指定日時**|
   |終了日|当月の終わり|
   |ロール、特権ロールの選択|**グローバル閲覧者**|
   |レビュー担当者|**選択したユーザー**|
   |レビュー担当者の選択|あなたのアカウント|

8. **[アクセス レビューの作成]** ブレードで、**[開始]** をクリックします。
 
    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It will take about a minute for the review to deploy and appear on the <bpt id="p2">**</bpt>AdatumLab500-04 <ph id="ph1">\|</ph> Access reviews<ept id="p2">**</ept> blade. You might have to refresh the web page. The review status will be <bpt id="p1">**</bpt>Active<ept id="p1">**</ept>. 

9. **[AdatumLab500-04 \| アクセス レビュー]** ブレードの **[グローバル閲覧者レビュー]** ヘッダーの下にある **[グローバル閲覧者]** エントリをクリックします。 

10. **[グローバル閲覧者レビュー]** ブレードで **[概要]** ページを確認し、**[進行状況]** グラフには **[未確認]** カテゴリのユーザーが 1 人表示されていることを確認します。 

11. On the <bpt id="p1">**</bpt>Global Reader Review<ept id="p1">**</ept> blade, in the <bpt id="p2">**</bpt>Manage<ept id="p2">**</ept> section, click <bpt id="p3">**</bpt>Results<ept id="p3">**</ept>. Note that aaduser2 is listed as having access to this role.

12. **aaduser2** をクリックすると、そのユーザーが関与する PIM アクティビティを表すエントリを含む詳細な監査ログが表示されます。

13. **[AdatumLab500-04 \| アクセス レビュー]** ブレードに戻ります。

14. **[AdatumLab500-04 \| アクセス レビュー]** ブレードの **[タスク]** セクションで、 **[アクセス権の確認]** をクリックし、 **[グローバル閲覧者レビュー]** エントリをクリックします。 

15. **[グローバル閲覧者レビュー]** ブレードで、**[aaduser2]** エントリをクリックします。 

16. **[理由]** テキスト ボックスに承認の根拠を入力し、**[承認]** をクリックして現在のロール メンバーシップを維持するか、**[拒否]** をクリックして、承認を取り消します。 

17. **[Privileged Identity Management]** ブレードに戻り、 **[管理]** セクションで **[Azure AD ロール]** をクリックします。

18. **[AdatumLab500-04 \| クイック スタート]** ブレードの **[管理]** セクションで、 **[アクセス レビュー]** をクリックします。

19. Select the entry representing the <bpt id="p1">**</bpt>Global Reader<ept id="p1">**</ept> review. Note that the <bpt id="p1">**</bpt>Progress<ept id="p1">**</ept> chart has been updated to show your review. 

#### <a name="task-2-review-pim-alerts-summary-information-and-detailed-audit-information"></a>タスク 2:PIM アラート、サマリ情報、および詳細な監査情報を確認します。 

このタスクでは、PIM アラート、サマリー情報、および詳細な監査情報を確認します。 

1. **[Privileged Identity Management]** ブレードに戻り、 **[管理]** セクションで **[Azure AD ロール]** をクリックします。

2. **[AdatumLab500-04 \| クイック スタート]** ブレードの **[管理]** セクションで、 **[アラート]** をクリックし、 **[設定]** をクリックします。

3. このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。 

4. **[AdatumLab500-04 \| クイック スタート]** ブレードに戻り、 **[概要]** をクリックします。 

5. **[AdatumLab500-04 \| 概要]** ブレードで、ロールのアクティブ化、PIM アクティビティ、アラート、およびロールの割り当てに関する概要情報を確認します。

6. **[AdatumLab500-04 \| 概要]** ブレードの **[アクティビティ]** セクションで、 **[リソース監査]** をクリックします。 

    >**注**:監査履歴は、過去 30 日以内にすべての特権ロールの割り当てとアクティブ化に使用できます。

7. **時間**、**リクエスター**、**アクション**、**リソース名**、**スコープ**、**プライマリ ターゲット**、**件名**などの詳細情報を取得できることに注意してください。 

> 結果:アクセス レビューを構成し、監査情報を確認しました。 

**リソースをクリーンアップする**

> これがクラスで使用するリージョンであることを講師に確認します。 

1. Azure portal で、**Directory + Subscription** フィルターを、**az500-04-vm1** Azure VM をデプロイした Azure サブスクリプションに関連付けられている Azure AD テナントに設定します。

    >**注**: メインの Azure AD テナント エントリが表示されない場合は、[ディレクトリの切り替え] リンクをクリックし、メインのテナントの行を選択して、[切り替え] ボタンをクリックします。

2. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

3. Cloud Shell ペインの左上隅にあるドロップダウン メニューで **[PowerShell]** が選択されていることを確認します。

4. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、前のラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

5. **[Cloud Shell]** ペインを閉じます。 

6. Azure portal に戻り、**Directory + Subscription** フィルターを使用して **AdatumLab500-04** Azure Active Directory テナントに切り替えます。

7. **[AdatumLab500-04 Azure Active Directory]** ブレードに戻り、 **[管理]** セクションで、 **[ライセンス]** をクリックします。

8. **[ライセンス** | 概要] ブレードで、 **[すべての製品]** をクリックし、 **[Azure Active Directory Premium P2]** のチェックボックスをオンにし、クリックして開きます。

    >**注**: ラボ 4 - 演習 2 - タスク 4 の「**Azure AD ユーザーに Azure AD Premium P2 ライセンスを割り当てる**」では、Premium ライセンスをユーザー **aaduser1、aaduser2、and aaduser3** に割り当てました。これらのライセンスはロール割り当て済みユーザーから削除する必要があります

9. 先に進む前に、「ラボ 04: MFA、条件付きアクセス、および AAD ID 保護」を完了していることをご確認ください。

10. Azure portal で、**[ユーザー - すべてのユーザー]** ブレードに移動し、**[aaduser1]** ユーザー アカウントを表すエントリをクリックします。**[aaduser1 - プロファイル]** ブレードで **[削除]** をクリックし、確認を求められたら、**[はい]** を選択します。

11. 同じ手順を繰り返して、作成した残りのユーザー アカウントを削除します。

12. Azure AD テナント、AdatumLab500-04、および aaduser1、aaduser2、および aaduser3 ユーザー アカウントが必要です。

13. Azure portal からサインアウトし、サインインし直します。 

14. **[ディレクトリ 'AdatumLab500-04' の削除]** ブレードに戻り、**[削除]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Still not able to delete the tenant and throwing an error <bpt id="p2">**</bpt>Delete all license-based and subscriptions<ept id="p2">**</ept>, then it might be due to any subscriptions that have been linked to the tenant. Here <bpt id="p1">**</bpt>Free Premium P2 license<ept id="p1">**</ept> could be throwing the validation error. Deleting the trial subscription of the Premium P2 License using the Global admin id from the M365 admin&gt;&gt; <bpt id="p1">**</bpt>Your Products<ept id="p1">**</ept> and from the <bpt id="p2">**</bpt>Business Store<ept id="p2">**</ept> portal would solve this issue. Also note that deleting the tenant takes more time. Check the End date of the subscription, once after the end of the trial period, revisit the Azure Active directory and then try to delete the tenant.    

> このタスクに関する追加情報については、[https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto) を参照してください
