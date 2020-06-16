---
title: Azure Key Vault와 Kubernetes 통합
description: 이 자습서에서는 비밀 저장소 CSI(컨테이너 스토리지 인터페이스) 드라이버를 사용하여 Azure Key Vault에서 비밀에 액세스하고 이를 검색한 다음, Kubernetes pod에 탑재합니다.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636937"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>자습서: Kubernetes에서 비밀 저장소 CSI 드라이버의 Azure Key Vault 공급자 구성 및 실행

이 자습서에서는 비밀 저장소 CSI(컨테이너 스토리지 인터페이스) 드라이버를 사용하여 Azure Key Vault에서 비밀에 액세스하고 이를 검색한 다음, Kubernetes pod에 탑재합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 서비스 주체 만들기
> * Azure Kubernetes Service 클러스터 배포
> * Helm 및 비밀 저장소 CSI 드라이버 설치
> * Azure Key Vault 만들기 및 비밀 설정
> * 사용자 고유의 SecretProviderClass 개체 만들기
> * Key Vault에서 탑재된 비밀로 Pod 배포

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 자습서를 시작하기 전에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)를 설치해야 합니다.

## <a name="create-a-service-principal-or-use-managed-identities"></a>서비스 주체 만들기 또는 관리 ID 사용

관리 ID를 사용하려면 다음 섹션으로 이동합니다.

Azure Key Vault에서 액세스할 수 있는 리소스를 제어하는 서비스 주체를 만듭니다. 이 서비스 주체의 액세스는 서비스 주체에게 할당된 역할로 제한됩니다. 이 기능을 사용하면 서비스 주체가 비밀을 관리하는 방법을 제어할 수 있습니다. 아래 예제에서 서비스 주체의 이름은 **contosoServicePrincipal**입니다.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
이 작업을 수행하면 일련의 키/값 쌍이 반환됩니다.

![이미지](../media/kubernetes-key-vault-1.png)

**appID** 및 **암호**를 복사해 둡니다. 이 자격 증명은 나중에 필요합니다.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Azure CLI를 사용하여 Azure Kubernetes Service 클러스터 배포

Azure Cloud Shell을 사용할 필요 없이, Azure CLI가 설치된 명령 프롬프트(터미널)를 사용하면 됩니다. 

이 [가이드](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)에 따라 **리소스 그룹 만들기**, **AKS 클러스터 만들기** 및 **클러스터에 연결** 섹션을 완료하세요.

**참고:** 서비스 주체 대신 Pod ID를 사용하려면 Kubernetes 클러스터를 만들 때 아래와 같이 Pod ID를 사용하도록 설정해야 합니다.

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. 다운로드한 "kubectl.exe" 파일로 [PATH 환경 변수를 설정](https://www.java.com/en/download/help/path.xml)합니다.
1. 아래의 명령을 사용하여 Kubernetes 버전을 확인합니다. 이 명령은 클라이언트 및 서버 버전을 출력합니다. 클라이언트 버전은 사용자가 설치한 "kubectl.exe"이고, 서버 버전은 클러스터가 실행되는 Azure Kubernetes Services입니다.
    ```azurecli
    kubectl version
    ```
1. Kubernetes 버전이 **v1.16.0** 이상인지 확인합니다. 이 명령은 Kubernetes 클러스터와 노드 풀을 모두 업그레이드합니다. 실행하는 데 몇 분이 걸릴 수 있습니다. 이 예제에서 리소스 그룹은 **contosoResourceGroup**이고 Kubernetes 클러스터는 **contosoAKSCluster**입니다.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. 아래 명령을 사용하여 만든 AKS 클러스터의 메타데이터를 표시합니다. **principalId**, **clientId**, **subscriptionId** 및 **nodeResourceGroup**을 복사합니다.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    다음은 두 매개 변수가 모두 강조 표시된 출력입니다.
    
    ![이미지](../media/kubernetes-key-vault-5.png) ![이미지](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Helm 및 비밀 저장소 CSI 드라이버 설치

비밀 저장소 CSI 드라이버를 설치하려면 [Helm](https://helm.sh/docs/intro/install/)을 설치해야 합니다.

[비밀 저장소 CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) 드라이버 인터페이스를 사용하면 Azure Key Vault 인스턴스에 저장된 비밀 콘텐츠를 가져오고 드라이버 인터페이스를 사용하여 이 비밀 콘텐츠를 Kubernetes Pod에 탑재할 수 있습니다.

1. Helm 버전이 v3 이상인지 확인합니다.
    ```azurecli
    helm version
    ```
1. 비밀 저장소 CSI 드라이버와 이 드라이버의 Azure Key Vault 공급자를 설치합니다.
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Azure Key Vault 만들기 및 비밀 설정

이 [가이드](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)에 따라 고유한 Key Vault를 만들고 비밀을 설정합니다.

**참고:** Azure Cloud Shell을 사용하거나 새 리소스 그룹을 만들 필요가 없습니다. 앞에서 Kubernetes 클러스터용으로 만든 리소스 그룹을 사용하면 됩니다.

## <a name="create-your-own-secretproviderclass-object"></a>사용자 고유의 SecretProviderClass 개체 만들기

사용자 고유의 SecretProviderClass 개체를 만들기 위해 제공된 이 [템플릿](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml)을 사용하여 비밀 저장소 CSI 드라이버의 공급자별 매개 변수를 제공합니다. 이 개체는 Key Vault에 대한 ID 액세스를 제공합니다.

제공된 샘플 SecretProviderClass YAML 파일을 사용합니다. 누락된 매개 변수를 입력하려고 합니다. 필수 매개 변수는 다음과 같습니다.

1.  **userAssignedIdentityID:** 서비스 주체의 클라이언트 ID
1.  **keyvaultName:** Key Vault의 이름
1.  **objects:** 이 개체는 탑재하려는 모든 비밀 콘텐츠를 포함합니다.
    1.  **objectName:** 비밀 콘텐츠의 이름
    1.  **objectType:** 개체 유형(비밀, 키, 인증서)
1.  **resourceGroup:** 리소스 그룹의 이름
1.  **subscriptionId:** Key Vault의 구독 ID
1.  **tenantID:** Key Vault의 테넌트 ID(디렉터리 ID)

다음은 업데이트된 템플릿입니다. .yaml 파일로 다운로드하여 해당하는 필수 필드를 입력합니다. 이 예제에서 Key Vault는 **contosoKeyVault5**이고, 두 개의 비밀 **secret1** 및 **secret2**가 있습니다.

**참고:** 관리 ID를 사용하는 경우 **usePodIdentity** 필드가 **true**여야 하고 **userAssignedIdentityID** 필드는 따옴표 **""** 만 있는 상태로 두어야 합니다. 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
다음은 "az keyvault show --name contosoKeyVault5"의 콘솔 출력과 강조 표시된 관련 메타데이터입니다.

![이미지](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>서비스 주체 할당 또는 관리 ID 사용

### <a name="using-service-principal"></a>서비스 주체 사용

서비스 주체를 사용하는 경우, 서비스 주체에게 Key Vault에 액세스하고 비밀을 검색할 수 있는 권한을 부여해야 합니다. 다음 단계를 완료하여 서비스 주체에게 **"Reader"** 역할을 할당하고 Key Vault에서 비밀을 **"get"** 할 수 있는 권한을 부여합니다.

1. 서비스 주체를 기존 Key Vault에 할당합니다. **$AZURE _CLIENT_ID** 매개 변수는 서비스 주체를 만든 후에 복사한 **appId**입니다.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    다음은 명령의 출력입니다. 

    ![이미지](../media/kubernetes-key-vault-3.png)

1. 서비스 주체에게 비밀을 가져올 수 있는 권한을 부여합니다.
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. 이제 Key Vault에서 비밀을 읽을 수 있는 권한을 갖도록 서비스 주체를 구성했습니다. **$AZURE_CLIENT_SECRET**은 서비스 주체의 **암호**입니다. 서비스 주체 자격 증명을 비밀 저장소 CSI 드라이버로 액세스할 수 있는 Kubernetes 암호로 추가합니다.
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**참고:** Kubernetes Pod를 배포하는 경우 나중에 잘못된 클라이언트 암호 ID에 대한 오류가 나타나면 만료되거나 다시 설정된 이전 클라이언트 암호 ID가 있을 수 있습니다. 이 문제를 해결하려면 비밀 “secrets-store-creds”를 삭제하고 현재 클라이언트 암호 ID로 새 비밀을 만듭니다. “secrets-store-creds”를 삭제하려면 아래 명령을 실행합니다.
```azurecli
kubectl delete secrets secrets-store-creds
```

서비스 주체의 클라이언트 암호 ID를 잊어버린 경우 다음 명령을 사용하여 다시 설정할 수 있습니다.

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>관리 ID 사용

관리 ID를 사용하는 경우 내가 만든 AKS 클러스터에 특정 역할을 할당합니다. 
1. 사용자가 할당한 관리 ID를 만들거나 나열하거나 읽으려면 AKS 클러스터에 [관리 ID 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할이 할당되어야 합니다. **$clientId**가 Kubernetes 클러스터의 ID인지 확인합니다.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Azure AD(Azure Active Directory) ID를 AKS에 설치합니다.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Azure AD ID를 만듭니다. **clientId**와 **principalId**를 복사합니다.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. 방금 Key Vault에 대해 만든 Azure AD ID에 읽기 역할을 할당합니다. 그런 다음, Key Vault에서 비밀을 얻기 위한 ID 권한을 부여합니다. 방금 만든 Azure ID의 **clientId** 및 **principalId**를 사용합니다.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Key Vault에서 탑재된 비밀로 Pod 배포

아래 명령은 SecretProviderClass 개체를 구성합니다.
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>서비스 주체 사용

서비스 주체를 사용하는 경우, 아래 명령은 Kubernetes 포드를 앞에서 구성한 SecretProviderClass 및 secrets-store-creds와 함께 배포합니다. 다음은 [linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) 및 [windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) 배포를 위한 템플릿입니다.
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>관리 ID 사용

관리 ID를 사용하는 경우 클러스터에 앞에서 만든 ID를 참조하는 **AzureIdentity**를 만듭니다. 그런 다음, 이 **AzureIdentity**를 참조하는 **AzureIdentityBinding**을 만듭니다. 아래 템플릿을 사용하여 해당 매개 변수를 입력하고 **podIdentityAndBinding.yaml**로 저장합니다.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
다음 명령을 실행하여 바인딩을 실행합니다.

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

다음은 Pod의 실제 배포입니다. 다음은 마지막 단계의 Pod ID 바인딩을 사용하는 배포 YAML 파일입니다. 이 파일을 **podBindingDeployment.yaml**로 저장합니다.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

다음 명령을 실행하여 Pod를 배포합니다.

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>상태 및 비밀 콘텐츠 확인 
배포한 Pod를 표시하려면 다음을 사용합니다.
```azurecli
kubectl get pods
```

Pod의 상태를 확인하려면 다음 명령을 사용합니다.
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![이미지](../media/kubernetes-key-vault-4.png)

배포된 Pod는 "Running" 상태입니다. 아래쪽의 "Events" 섹션에서 왼쪽에 있는 모든 Event Type이 "Normal"로 분류됩니다.
Pod가 Running 상태임을 확인한 후에는 Pod에 Key Vault의 비밀이 있는지 확인할 수 있습니다.

Pod에 있는 모든 비밀을 표시하려면 다음을 사용합니다.
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

특정 비밀에서 콘텐츠를 가져오려면 다음을 사용합니다.
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

표시된 비밀의 콘텐츠를 확인합니다.

## <a name="next-steps"></a>다음 단계

Key Vault를 복구할 수 있는지 확인합니다.
> [!div class="nextstepaction"]
> [일시 삭제 설정](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
