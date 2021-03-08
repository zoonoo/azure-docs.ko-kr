---
title: Azure Key Vault와 Kubernetes 통합
description: 이 자습서에서는 비밀 저장소 CSI(컨테이너 스토리지 인터페이스) 드라이버를 통해 비밀을 Kubernetes Pod에 탑재하여 Azure 키 자격 증명 모음에서 해당 비밀에 액세스하고 검색합니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: fd40ad41bda50d93943a514cd9cc3aeeab4ac948
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198883"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>자습서: Kubernetes에서 비밀 저장소 CSI 드라이버에 대한 Azure Key Vault 공급자 구성 및 실행

> [!IMPORTANT]
> 비밀 저장소 CSI 드라이버는 Azure 기술 지원에서 지원되지 않는 오픈 소스 프로젝트입니다. 페이지 하단의 github 링크에서 CSI 드라이버 Key Vault 통합과 관련된 모든 피드백과 문제를 보고하세요. 이 도구는 사용자가 클러스터에 직접 설치하고 커뮤니티에서 피드백을 수집할 수 있도록 제공됩니다.

이 자습서에서는 비밀 저장소 CSI(컨테이너 스토리지 인터페이스) 드라이버를 통해 비밀을 Kubernetes Pod에 탑재하여 Azure 키 자격 증명 모음에서 해당 비밀에 액세스하고 검색합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 관리 ID를 사용합니다.
> * Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터 배포
> * Helm, 비밀 저장소 CSI 드라이버 및 CSI 드라이버의 Azure Key Vault 공급자를 설치합니다.
> * Azure 키 자격 증명 모음 만들기 및 비밀 설정
> * 사용자 고유의 SecretProviderClass 개체 만들기
> * 키 자격 증명 모음에서 탑재된 비밀을 사용하여 Pod 배포

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* 이 자습서를 시작하기 전에 [Azure CLI](/cli/azure/install-azure-cli-windows)를 설치해야 합니다.

이 자습서에서는 Linux 노드에서 Azure Kubernetes Service를 실행한다고 가정합니다.

## <a name="use-managed-identities"></a>관리 ID 사용

이 다이어그램은 관리 ID에 대한 AKS–Key Vault 통합 흐름을 보여줍니다.

![관리 ID에 대한 AKS–Key Vault 통합 흐름을 보여주는 다이어그램](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터 배포

Azure Cloud Shell은 사용할 필요가 없습니다. Azure CLI가 설치된 명령 프롬프트(터미널)만 사용하면 됩니다. 

[Azure CLI를 사용하여 Azure Kubernetes Service 클러스터 배포](../../aks/kubernetes-walkthrough.md)의 "리소스 그룹 만들기", "AKS 클러스터 만들기" 및 "클러스터에 연결" 섹션을 완료합니다. 

> [!NOTE] 
> Pod ID를 사용하려는 경우 권장되는 네트워크 플러그 인은 `azure`입니다. 자세한 내용은 [설명서](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/)를 참조하세요. 다음 명령에 표시된 것처럼 Kubernetes 클러스터를 만듭니다.
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [PATH 환경 변수](https://www.java.com/en/download/help/path.xml)를 다운로드한 *kubectl.exe* 파일로 설정합니다.
2. 클라이언트 및 서버 버전을 출력하는 다음 명령을 사용하여 Kubernetes 버전을 확인합니다. 클라이언트 버전은 설치한 *kubectl.exe* 파일이고, 서버 버전은 클러스터가 실행되는 AKS(Azure Kubernetes Service)입니다.
    ```azurecli
    kubectl version
    ```
3. Kubernetes 버전이 1.16.0 이상인지 확인합니다. Windows 클러스터의 경우 Kubernetes 버전이 1.18.0 이상인지 확인합니다. 다음 명령은 Kubernetes 클러스터와 노드 풀을 모두 업그레이드합니다. 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 이 예제에서 리소스 그룹은 *contosoResourceGroup* 이고, Kubernetes 클러스터는 *contosoAKSCluster* 입니다.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. 만든 AKS 클러스터의 메타데이터를 표시하려면 다음 명령을 사용합니다. 나중에 사용할 수 있도록 **principalId**, **clientId**, **subscriptionId** 및 **nodeResourceGroup** 을 복사합니다. 관리 ID를 사용하도록 설정하여 AKS 클러스터를 만들지 않은 경우 **principalId** 및 **clientId** 는 null이 됩니다. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    출력에는 강조 표시된 두 매개 변수가 모두 표시됩니다.
    
    ![principalId 및 clientId 값이 강조 표시된 Azure CLI의 스크린샷](../media/kubernetes-key-vault-2.png) ![subscriptionId 및 nodeResourceGroup 값이 강조 표시된 Azure CLI의 스크린샷](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Helm 및 비밀 저장소 CSI 드라이버 설치
> [!NOTE]
> 아래 설치는 Linux의 AKS에서만 작동합니다. Secrets Store CSI 드라이버 설치에 대한 자세한 내용은 [Secrets Store CSI 드라이버용 Azure Key Vault 공급자](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/)를 참조하세요. 

비밀 저장소 CSI 드라이버 및 Azure Key Vault 공급자를 설치하려면 먼저 [Helm](https://helm.sh/docs/intro/install/)을 설치해야 합니다.

[비밀 저장소 CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/) 드라이버 인터페이스를 사용하면 Azure Key Vault 인스턴스에 저장된 비밀을 가져온 다음, 드라이버 인터페이스를 사용하여 해당 비밀 콘텐츠를 Kubernetes Pod에 탑재할 수 있습니다.

1. Helm 버전이 v3 이상인지 확인합니다.
    ```azurecli
    helm version
    ```
1. 비밀 저장소 CSI 드라이버와 이 드라이버의 Azure Key Vault 공급자를 설치합니다.
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Windows 노드에서 비밀 저장소 CSI 드라이버 및 Azure Key Vault 공급자를 사용하려는 경우에는 [Helm 구성 값](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration)을 사용하여 Windows 노드에서 드라이버 및 공급자를 사용하도록 설정합니다.

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Azure 키 자격 증명 모음 만들기 및 비밀 설정

사용자 고유의 키 자격 증명 모음을 만들고 비밀을 설정하려면 [Azure CLI를 사용하여 Azure Key Vault에서 비밀 설정 및 검색](../secrets/quick-create-cli.md)의 지침을 따릅니다.

> [!NOTE] 
> Azure Cloud Shell을 사용하거나 새 리소스 그룹을 만들 필요가 없습니다. 이전에 만든 리소스 그룹을 Kubernetes 클러스터에 사용할 수 있습니다.

## <a name="create-your-own-secretproviderclass-object"></a>사용자 고유의 SecretProviderClass 개체 만들기

비밀 저장소 CSI 드라이버에 대한 공급자별 매개 변수를 사용하여 사용자 고유의 사용자 지정 SecretProviderClass 개체를 만들려면 [이 템플릿을 사용](https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/examples/pod-identity/v1alpha1_secretproviderclass_pod_identity.yaml)합니다. 이 개체는 키 자격 증명 모음에 대한 ID 액세스를 제공합니다.

SecretProviderClass YAML 파일 샘플에서 누락된 매개 변수를 입력합니다. 필수 매개 변수는 다음과 같습니다.

* **keyvaultName**: 키 자격 증명 모음의 이름
* **objects**: 탑재하려는 모든 비밀 콘텐츠 목록
    * **objectName**: 비밀 콘텐츠의 이름
    * **objectType**: 개체 형식(비밀, 키, 인증서)
* **tenantID**: 키 자격 증명 모음의 테넌트 ID 또는 디렉터리 ID

모든 필수 필드 및 지원되는 구성에 대한 설명서는 여기에서 확인할 수 있습니다. [링크](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

업데이트된 템플릿은 다음 코드에 표시되어 있습니다. YAML 파일로 다운로드하고, 필수 필드를 입력합니다. 이 예제에서 키 자격 증명 모음은 **contosoKeyVault5** 입니다. 여기에는 **secret1** 및 **secret2** 의 두 가지 비밀이 있습니다.

> [!NOTE] 
> pod ID를 사용하는 경우 **usePodIdentity** 값을 *true* 로 설정하고, **userAssignedIdentityID** 값을 큰따옴표 쌍( **""** )으로 설정합니다. 관리 ID를 사용하는 경우 **useVMManagedIdentity** 값을 *true* 로 설정하고, **userAssignedIdentityID** 값을 사용자 할당 ID의 clientID로 설정합니다.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
다음 이미지에서는 **az keyvault show --name contosoKeyVault5** 에 대한 콘솔 출력을 보여 주며, 관련 메타데이터가 강조 표시되어 있습니다.

!["az keyvault show --name contosoKeyVault5"에 대한 콘솔 출력을 보여 주는 스크린샷](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Azure AD(Azure Active Directory) pod ID 설치

1. 사용자가 만든 AKS 클러스터에 특정 역할을 할당합니다. 

    Azure AD(Azure Active Directory) pod ID를 사용하여 모든 필수 역할 할당에 대한 설명서는 여기에서 사용할 수 있습니다. [링크](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Azure AD(Azure Active Directory) ID를 AKS에 설치합니다.

    > [!NOTE] 
    > Kubenet 네트워크 플러그 인에서 AKS 클러스터를 사용하는 경우 이 [설명서](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/)에서 클러스터에 pod ID를 배포하는 방법을 검토하세요.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. 사용자 할당 관리 ID를 만듭니다. 나중에 사용할 수 있도록 출력에서 **clientId** 를 복사합니다.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Key Vault에서 비밀을 얻기 위한 ID 권한을 부여합니다. 사용자 할당 관리 ID의 **clientId** 를 사용합니다.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>키 자격 증명 모음에서 탑재된 비밀을 사용하여 Pod 배포

SecretProviderClass 개체를 구성하려면 다음 명령을 실행합니다.
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>관리 ID 사용

포드 ID를 사용하는 경우 이전에 만든 ID를 참조하는 *AzureIdentity* 를 클러스터에 만듭니다. 그런 다음, 사용자가 만든 AzureIdentity를 참조하는 *AzureIdentityBinding* 을 만듭니다. 다음 템플릿에서 매개 변수를 채운 다음, *podIdentityAndBinding.yaml* 로 저장합니다.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
다음 명령을 실행하여 바인딩을 구성합니다.

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

다음으로, Pod를 배포합니다. 다음 코드는 이전 단계의 Pod ID 바인딩을 사용하는 배포 YAML 파일입니다. 이 파일을 *podBindingDeployment.yaml* 로 저장합니다.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
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

### <a name="check-the-pod-status-and-secret-content"></a>Pod 상태 및 비밀 콘텐츠 확인 

배포한 Pod를 표시하려면 다음 명령을 실행합니다.
```azurecli
kubectl get pods
```

Pod 상태를 확인하려면 다음 명령을 실행합니다.
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

!["Running" Pod 상태를 표시하고 모든 이벤트를 "Normal"로 보여 주는 Azure CLI 출력의 스크린샷 ](../media/kubernetes-key-vault-6.png)

출력 창에서 배포된 Pod는 *Running(실행 중)* 상태입니다. 아래쪽의 **Events** 섹션에서 모든 이벤트 유형이 *Normal(보통)* 로 표시되어 있습니다.

Pod가 실행 중 상태로 확인되면 키 자격 증명 모음의 비밀이 Pod에 포함되어 있는지 확인할 수 있습니다.

Pod에 포함된 모든 비밀을 표시하려면 다음 명령을 실행합니다.
```azurecli
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

특정 비밀의 콘텐츠를 표시하려면 다음 명령을 실행합니다.
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

비밀의 콘텐츠가 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

키 자격 증명 모음을 복구할 수 있도록 하려면 다음을 참조하세요.
> [!div class="nextstepaction"]
> [일시 삭제 설정](./key-vault-recovery.md)
