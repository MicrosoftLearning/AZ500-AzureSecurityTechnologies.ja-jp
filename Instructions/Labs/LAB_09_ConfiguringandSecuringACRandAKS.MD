---
lab:
  title: 09 - ACR と AKS の構成とセキュリティ保護
  module: Module 02 - Implement Platform Protection
---

# <a name="lab-09-configuring-and-securing-acr-and-aks"></a>ラボ 09:ACR と AKS の構成とセキュリティ保護
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You have been asked to deploy a proof of concept with Azure Container Registry and Azure Kubernetes Service. Specifically, the proof of concept should demonstrate:

- Dockerfile を使用してイメージをビルドします。
- Azure Container Registry を使用してイメージを格納します。
- Azure Kubernetes Service を構成します。
- コンテナー アプリケーションのセキュリティ保護とアクセスを社内外で行います。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>ラボの目的

このラボでは、次の演習を行います。

- 演習 1:ACR と AKS の構成とセキュリティ保護

## <a name="configuring-and-securing-acr-and-aks-diagram"></a>ACR と AKS の構成とセキュリティ保護の図

![image](https://user-images.githubusercontent.com/91347931/157532250-1104a829-792a-4b6d-beff-fe976e2d5d4b.png)

## <a name="intructions"></a>指示

## <a name="lab-files"></a>ラボ ファイル：

- **\\Allfiles\\Labs\\09\\nginxexternal.yaml**
- **\\Allfiles\\Labs\\09\\nginxinternal.yaml**

### <a name="exercise-1-configuring-and-securing-acr-and-aks"></a>演習 1:ACR と AKS の構成とセキュリティ保護

### <a name="estimated-timing-45-minutes"></a>推定時間:45 分

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East (US)<ept id="p1">**</ept> region. Verify with your instructor this is region to use for you class. 

この演習では、次のタスクを実行します。

- タスク 1:Azure コンテナー レジストリを作成する
- タスク 2:Dockerfile を作成し、コンテナーを構築して、Azure Container Registry にプッシュする
- タスク 3:Azure Kubernetes Service クラスターを作成する
- タスク 4:AkS クラスターに ACR にアクセスするためのアクセス許可を付与する
- タスク 5:AKS への外部サービスのデプロイ
- タスク 6:外部 AKS ホスト サービスにアクセスできることを確認する
- タスク 7:AKS への内部サービスの展開
- タスク 8:内部 AKS ホスト サービスにアクセスできることを確認する

#### <a name="task-1-create-an-azure-container-registry"></a>タスク 1:Azure コンテナー レジストリを作成する

このタスクでは、ラボ用リソース グループと Azure Container Registry を作成します。

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**:このラボで使用している Azure サブスクリプションの所有者または共同作成者のロールと、そのサブスクリプションに関連付けられている Azure AD テナントのグローバル管理者の役割を持つアカウントを使用して、Azure portal にサインインします。

2. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>Bash<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

3. Cloud Shell ウィンドウの左上隅にあるドロップダウン メニューで **Bash** が選択されていることを確認します。

4. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して、このラボ用の新しいリソース グループを作成します。

    ```sh
    az group create --name AZ500LAB09 --location eastus
    ```

5. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して、リソース グループが作成されたことを確認します。

    ```
    az group list --query "[?name=='AZ500LAB09']" -o table
    ```

6. [Cloud Shell] ウィンドウ内の Bash セッションで、次のコマンドを実行して新しい Azure Container Registry (ACR) インスタンスを作成します (ACR の名前はグローバルに一意である必要があります)。 

    ```sh
    az acr create --resource-group AZ500LAB09 --name az500$RANDOM$RANDOM --sku Basic
    ```

7. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して、新しい ACR が作成されたことを確認します。

    ```sh
    az acr list --resource-group AZ500LAB09
    ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record the name of the ACR. You will need it in the next task.

#### <a name="task-2-create-a-dockerfile-build-a-container-and-push-it-to-azure-container-registry"></a>タスク 2:Dockerfile を作成し、コンテナーを構築して、Azure Container Registry にプッシュする

このタスクでは、Dockerfile を作成し、Dockerfile からイメージをビルドして、ACR にイメージをデプロイします。 

1. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して Dockerfile を作成し、Nginx ベースのイメージを作成します。 

    ```sh
    echo FROM nginx > Dockerfile
    ```

2. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して Dockerfile からイメージを構築し、新しい ACR にイメージをプッシュします。 

    >Azure Container Registry と Azure Kubernetes Service を使用して概念実証をデプロイするよう依頼されました。 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az acr build --resource-group AZ500LAB09 --image sample/nginx:v1 --registry $ACRNAME --file Dockerfile .
    ```

    >具体的には、概念実証は次の例を示す必要があります。

3. [Cloud Shell] ペインを閉じます。

4. Azure portal で **AZ500Lab09** リソース グループに移動し、リソースの一覧で、前のタスクでプロビジョニングした Azure Container Registry インスタンスを表すエントリをクリックします。

5. コンテナー レジストリ ブレードの **サービス** セクションで、**レポジトリ** をクリックします。 

6. リポジトリのリストに **sample/nginx** という名前の新しいコンテナー イメージが含まれていることを確認します。

7. **sample/nginx** エントリをクリックし、イメージのバージョンを識別する **v1** タグの存在を確認します。

8. **v1** エントリをクリックして、イメージ マニフェストを表示します。

    >**注**:マニフェストには、sha256 ダイジェスト、マニフェスト作成日、およびプラットフォーム エントリが含まれます。 

#### <a name="task-3-create-an-azure-kubernetes-service-cluster"></a>タスク 3:Azure Kubernetes Service クラスターを作成する

このタスクでは、Azure Kubernetes Service を作成し、デプロイされたリソースを確認します。 

1. Azure portal ページの上部の **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、**Kubernetes サービス** を入力し **Enter**  キーを押します。

2. **[Kubernetes サービス]** ブレードで、 **[+ 作成]** をクリックし、ドロップダウン メニューで **[+ Kubernetes クラスターを作成]** をクリックします

3. On the <bpt id="p1">**</bpt>Basics<ept id="p1">**</ept> tab of the <bpt id="p2">**</bpt>Create Kubernetes cluster<ept id="p2">**</ept> blade, select <bpt id="p3">**</bpt>Cluster preset configuration<ept id="p3">**</ept>, select <bpt id="p4">**</bpt>Dev/Test ($)<ept id="p4">**</ept>. Now specify the following settings (leave others with their default values):

    |設定|[値]|
    |----|----|
    |サブスクリプション|このラボで使用している Azure サブスクリプションの名前|
    |リソース グループ|**AZ500LAB09**|
    |Kubernetes クラスター名|**MyKubernetesCluster**|
    |リージョン|**(米国) 米国東部**|
    |可用性ゾーン |**なし**|
    |スケーリング方法|**[手動]**|
    |ノード数|**1**|

4. **[次へ: ノード プール >]** をクリックし **[Kubernetes クラスターを作成]** ブレードの **[ノード プール]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    |設定|値|
    |----|----|
    |仮想ノードを有効にする|チェックボックスがオフ|
    
5. **[次へ: アクセス >]** をクリックし、 **[Kubernetes クラスターを作成]** ブレードの **[アクセス]** タブで、既定値を受け入れて、 **[次へ:ネットワーク >]** を選択します。 

6. **[Kubernetes クラスターの作成]** ブレードの **[ネットワーク]** タブで、次の設定を指定します (既定値を他のユーザーのままにします)。

    |設定|値|
    |----|----|
    |ネットワーク構成|**Azure CNI**|
    |DNS 名プレフィックス|**既定値のままにします**|

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: AKS can be configured as a private cluster. This assigns a private IP to the API server to ensure network traffic between your API server and your node pools remains on the private network only. For more information, visit <bpt id="p1">[</bpt>Create a private Azure Kubernetes Service cluster<ept id="p1">](https://docs.microsoft.com/en-us/azure/aks/private-clusters)</ept> page.

7. **[次へ: 統合 >]** をクリックし、 **[Kubernetes クラスターを作成]** ブレードの **[統合]** タブで、 **[コンテナーの監視]** を **[無効]** に設定します。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In production scenarios, you would want to enable monitoring. Monitoring is disabled in this case since it is not covered in the lab. 

8. **[確認と作成]** をクリックしてから、**[作成]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete. This might take about 10 minutes.

9. デプロイが完了したら、Azure portal ページの上部にある **[リソース、サービス、ドキュメントの検索]** テキスト ボックスで、**リソース グループ**と入力し、**Enter** キーを押します。

10. On the <bpt id="p1">**</bpt>Resource groups<ept id="p1">**</ept> blade, in the listing of resource groups, note a new resource group named <bpt id="p2">**</bpt>MC_AZ500LAB09_MyKubernetesCluster_eastus<ept id="p2">**</ept> that holds components of the AKS Nodes. Review resources in this resource group. 
    
11. **[リソース グループ]** ブレードに戻り、**AZ500LAB09** エントリをクリックします。 

    >**注**:リソースの一覧で、AKS クラスターと対応する仮想ネットワークに注意してください。

12. Azure portal で、 Cloud Shell の Bash セッションを開始します。 

    >**注**:Cloud Shell ウィンドウの左上隅にあるドロップダウン メニューで **Bash** が選択されていることを確認します。

13. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して Kubernetes クラスターに接続します。

    ```sh
    az aks get-credentials --resource-group AZ500LAB09 --name MyKubernetesCluster
    ```

14. [Cloud Shell] ウィンドウ内の Bash セッションで、Kubenetes クラスターのノードを一覧表示するには、次の手順を実行します。 

    ```sh
    kubectl get nodes
    ```

    >**注**:クラスタ ノードの**ステータス**が**準備完了**としてリストされていることを確認します。

#### <a name="task-4-grant-the-aks-cluster-permissions-to-access-the-acr-and-manage-its-virtual-network"></a>タスク 4:ACRにアクセスし、その仮想ネットワークを管理するためのアクセス許可を AKS クラスターに付与する

このタスクでは、AKS クラスターに ACR にアクセスし、その仮想ネットワークを管理するためのアクセス許可を付与します。 

1. [Cloud Shell] ウィンドウ内の Bash セッションで、このラボで先ほど作成した Azure Container Registry インスタンスを使用するように AKS クラスターを構成するには、次の手順を実行します。 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az aks update -n MyKubernetesCluster -g AZ500LAB09 --attach-acr $ACRNAME
    ```

    >**注**:このコマンドは、ACR に 'acrpull' ロールの割り当てを付与します。 

    >**注**:このコマンドが完了するまでに数分かかる場合があります。 

2. Cloud Shell ペイン内の Bash セッションで、以下を実行して、AKS クラスターにその仮想ネットワークへのコントリビューター ロールを付与します。 

    ```sh
    RG_AKS=AZ500LAB09
    
    AKS_VNET_NAME=AZ500LAB09-vnet
    
    AKS_CLUSTER_NAME=MyKubernetesCluster
    
    AKS_VNET_ID=$(az network vnet show --name $AKS_VNET_NAME --resource-group $RG_AKS --query id -o tsv)
    
    AKS_MANAGED_ID=$(az aks show --name $AKS_CLUSTER_NAME --resource-group $RG_AKS --query identity.principalId -o tsv)
    
    az role assignment create --assignee $AKS_MANAGED_ID --role "Contributor" --scope $AKS_VNET_ID
    ```

#### <a name="task-5-deploy-an-external-service-to-aks"></a>タスク 5:AKS への外部サービスのデプロイ

このタスクでは、マニフェスト ファイルをダウンロードし、YAML ファイルを編集し、変更をクラスターに適用します。 

1. このラボのすべてのリソースについて、**米国東部**リージョンを使用しています。

2. [Cloud Shell] ウィンドウ内の Bash セッションで、次のコマンドを実行して、Azure Container Registry インスタンスの名前を識別します。

    ```sh
    echo $ACRNAME
    ```

    >これがクラスで使用するリージョンであることを講師に確認します。

3. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して nginxexternal.yaml ファイルを開き、その内容を編集できるようにします。 

    ```sh
    code ./nginxexternal.yaml
    ```

    >**注**:これは*外部* yaml ファイルです。

4. [エディター] ペインで、**24 行目**までスクロールし、 **`<ACRUniquename>`** プレースホルダーを ACR 名に置き換えます。

5. [エディター] ウィンドウの右上隅にある **省略記号**アイコンをクリックし、 **[保存]** をクリック して、 **[エディターを閉じる]** をクリックします。 

6. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行してクラスターに変更を適用します。

    ```sh
    kubectl apply -f nginxexternal.yaml
    ```

7. [Cloud Shell] ウィンドウ内の Bash セッションで、前のタスクで実行したコマンドの出力を確認して、デプロイと対応するサービスが作成されたことを確認します。 

    ```
    deployment.apps/nginxexternal created
    service/nginxexternal created
    ```

#### <a name="task-6-verify-the-you-can-access-an-external-aks-hosted-service"></a>タスク 6:外部 AKS ホスト サービスにアクセスできることを確認する

このタスクでは、パブリック IP アドレスを使用して、コンテナーに外部からアクセスできることを確認します。

1. [Cloud Shell] ウィンドウ内の Bash セッションで、次のコマンドを実行して、名前、タイプ、IP アドレス、ポートなどの nginxexternal サービスに関する情報を取得します。 

    ```sh
    kubectl get service nginxexternal
    ```

2. In the Bash session within the Cloud Shell pane, review the output and record the value in the External-IP column. You will need it in the next step. 

3. 新しいブラウザー タブを開き、前の手順で特定した IP アドレスを参照します。

4. Ensure the <bpt id="p1">**</bpt>Welcome to nginx!<ept id="p1">**</ept> page displays. 

#### <a name="task-7-deploy-an-internal-service-to-aks"></a>タスク 7:AKS への内部サービスの展開

このタスクでは、AKS に内部向けサービスをデプロイします。 

1. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行して nginxintenal.yaml ファイルを開き、内容を編集できるようにします。 

    ```sh
    code ./nginxinternal.yaml
    ```

    >**注**:これは*内部* yaml ファイルです。

2. [エディター] ペインで、コンテナ イメージへの参照を含む行までスクロールし、 **`<ACRUniquename>`** プレースホルダーを ACR 名に置き換えます。

3. [エディター] ウィンドウの右上隅にある **省略記号**アイコンをクリックし、 **[保存]** をクリック して、 **[エディターを閉じる]** をクリックします。 

4. [Cloud Shell] ウィンドウ内の Bash セッションで、次の手順を実行してクラスターに変更を適用します。

    ```sh
    kubectl apply -f nginxinternal.yaml
    ```

5.  Cloud Shell ウィンドウ内の Bash セッションで、出力を確認してデプロイおよび、サービスが作成されたことを確認します。

    ```
    deployment.apps/nginxinternal created
    service/nginxinternal created
    ```

6. [Cloud Shell] ウィンドウ内の Bash セッションで、次のコマンドを実行して、名前、タイプ、IP アドレス、ポートなどの nginxinternal サービスに関する情報を取得します。 

    ```sh
    kubectl get service nginxinternal
    ```

7. In the Bash session within the Cloud Shell pane, review the output. The External-IP is, in this case, a private IP address. If it is in a <bpt id="p1">**</bpt>Pending<ept id="p1">**</ept> state then run the previous command again.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record this IP address. You will need it in the next task. 

    >**注**:内部サービス エンドポイントにアクセスするには、クラスターで実行されているポッドのいずれかに対話式で接続します。 

    >**注**:または、CLUSTER-IP アドレスを使用することもできます。

#### <a name="task-8-verify-the-you-can-access-an-internal-aks-hosted-service"></a>タスク 8:内部 AKS ホスト サービスにアクセスできることを確認する

このタスクでは、AKS クラスターで実行されているポッドの 1 つを使用して内部サービスにアクセスします。 

1. [Cloud Shell] ウィンドウ内の Bash セッションで、AKS クラスターの既定の名前空間にあるポッドを一覧表示するには、次の手順を実行します。

    ```sh
    kubectl get pods
    ```

2. ポッドの一覧表示で、**NAME** 列の最初の項目をコピーします。

    >**注**:これは、以降の手順で使用するポッドです。

3. [Cloud Shell] ペイン内の Bash セッションで、次の手順を実行して、最初のポッドに対話的に接続します (`<pod_name>` プレースホルダーを前の手順でコピーした名前に置き換えます)。

    ```sh
    kubectl exec -it <pod_name> -- /bin/bash
    ```

4. [Cloud Shell] ペイン内の Bash セッションで、次の手順を実行して、nginx Web サイトがサービスのプライベート IP アドレスを介して利用できることを確認します (`<internal_IP>` プレースホルダーを前のタスクで記録した名前に置き換えます)。

    ```sh
    curl http://<internal_IP>
    ```

5. [Cloud Shell] ペインを閉じます。

> 結果:ACR と AKS を構成し、セキュリティで保護しました。


**リソースをクリーンアップする**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs.

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、Cloud Shell を開きます。 

2. [Cloud Shell] ペインの左上のドロップダウン メニューで **[PowerShell]** を選択し、メッセージが表示されたら **[確認]** をクリックします。

3. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB09" -Force -AsJob
    ```

4.  **[Cloud Shell]** ペインを閉じます。 
