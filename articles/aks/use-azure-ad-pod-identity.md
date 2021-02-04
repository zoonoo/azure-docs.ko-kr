---
title: Azure Kubernetes Service에서 Azure Active Directory pod 관리 id 사용 (미리 보기)
description: Azure Kubernetes 서비스 (AKS)에서 AAD pod 관리 되는 관리 되는 id를 사용 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 22b7a03a8598aa6e4b7c392567905d467776360c
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557359"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service에서 Azure Active Directory pod 관리 id 사용 (미리 보기)

Azure Active Directory pod 관리 id는 Kubernetes 기본 형식을 사용 하 여 [Azure 리소스에 대 한 관리 되는 id][az-managed-identities] 와 AZURE ACTIVE DIRECTORY (AAD)의 id를 pod와 연결 합니다. 관리자는 id 및 바인딩을 Kubernetes 기본 형식으로 만들며,이를 통해 pod가 AAD를 id 공급자로 사용 하는 Azure 리소스에 액세스할 수 있습니다.

> [!NOTE]
> AADPODIDENTITY를 이미 설치한 경우 기존 설치를 제거 해야 합니다. 이 기능을 사용 하도록 설정 하면 MIC 구성 요소가 필요 하지 않습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스를 설치 해야 합니다.

* Azure CLI 버전 2.8.0 이상
* `azure-preview`확장 버전 0.4.68 이상

### <a name="limitations"></a>제한 사항

* 클러스터에는 최대 50 pod id가 허용 됩니다.
* 클러스터에는 최대 50 pod id 예외가 허용 됩니다.
* Pod 관리 id는 Linux 노드 풀 에서만 사용할 수 있습니다.

### <a name="register-the-enablepodidentitypreview"></a>`EnablePodIdentityPreview`를 등록합니다.

`EnablePodIdentityPreview` 기능을 등록합니다.

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Azure CLI 설치 `aks-preview`

*Aks-preview* Azure CLI 확장 버전 0.4.64 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>관리 id를 사용 하 여 AKS 클러스터 만들기

관리 id 및 pod 관리 id를 사용 하 여 AKS 클러스터를 만듭니다. 다음 명령에서는 [az group create][az-group-create] 를 사용 하 여 *myresourcegroup* 이라는 리소스 그룹을 만들고 [az aks Create][az-aks-create] 명령을 사용 하 여 *MYRESOURCEGROUP* 리소스 그룹에 *myAKSCluster* 이라는 aks 클러스터를 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

[Az aks get-help][az-aks-get-credentials] 를 사용 하 여 aks 클러스터에 로그인 합니다. 또한이 명령은 `kubectl` 개발 컴퓨터에서 클라이언트 인증서를 다운로드 하 여 구성 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>ID 만들기

[Az identity create][az-identity-create] 를 사용 하 여 id를 만들고 *IDENTITY_CLIENT_ID* 및 *IDENTITY_RESOURCE_ID* 변수를 설정 합니다.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>Pod id 만들기

을 사용 하 여 클러스터에 대 한 pod id를 만듭니다 `az aks pod-identity add` .

> [!IMPORTANT]
> `Owner`구독에서 id 및 역할 바인딩을 만들려면와 같은 적절 한 사용 권한이 있어야 합니다.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> AKS 클러스터에서 pod 관리 id를 사용 하도록 설정 하면 AzurePodIdentityException 라는 *AKS* 이름이 *kube-system* 네임 스페이스에 추가 됩니다. AzurePodIdentityException를 사용 하면 특정 레이블이 있는 pod에서 NMI (노드 관리 id) 서버를 가로채 않고도 IMDS (Azure Instance Metadata Service) 끝점에 액세스할 수 있습니다. *Aks-추가-예외* 를 사용 하면 aks를 수동으로 구성 하지 않고도 AAD pod 관리 id와 같은 자사 addons에서 작동할 수 있습니다. 필요에 따라,, 또는를 사용 하 여 AzurePodIdentityException를 추가, 제거 및 업데이트할 수 있습니다 `az aks pod-identity exception add` `az aks pod-identity exception delete` `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>샘플 애플리케이션 실행

Pod에서 AAD pod 관리 id를 사용 하려면 pod에 *AzureIdentityBinding* 의 선택기와 일치 하는 값을 가진 *aadpodidbinding* 레이블이 필요 합니다. AAD pod 관리 id를 사용 하 여 샘플 응용 프로그램을 실행 하려면 `demo.yaml` 다음 내용으로 파일을 만듭니다. *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* 및 *IDENTITY_RESOURCE_GROUP* 을 이전 단계의 값으로 바꿉니다. *SUBSCRIPTION_ID* 를 구독 ID로 바꿉니다.

> [!NOTE]
> 이전 단계에서는 *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* 및 *IDENTITY_RESOURCE_GROUP* 변수를 만들었습니다. 과 같은 명령을 사용 `echo` 하 여 변수에 대해 설정한 값 (예:)을 표시할 수 있습니다 `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Pod 정의에는 이전 단계에서 실행 한 pod id의 이름과 일치 하는 값을 가진 *aadpodidbinding* 레이블이 있습니다 `az aks pod-identity add` .

`demo.yaml`를 사용 하 여 pod id와 동일한 네임 스페이스에 배포 합니다 `kubectl apply` .

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

를 사용 하 여 샘플 응용 프로그램이 성공적으로 실행 되는지 확인 `kubectl logs` 합니다.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

토큰이 성공적으로 획득 되 고 *가져오기* 작업이 성공 했음을 로그에 표시 되는지 확인 합니다.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>정리

클러스터에서 AAD pod 관리 id를 제거 하려면 클러스터에서 샘플 응용 프로그램 및 pod id를 제거 합니다. 그런 다음 id를 제거 합니다.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>다음 단계

관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID][az-managed-identities]를 참조하세요.

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
