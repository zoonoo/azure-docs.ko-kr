---
title: 자습서 - Azure Red Hat OpenShift 4 클러스터 만들기
description: Azure CLI를 사용하여 Microsoft Azure Red Hat OpenShift 클러스터를 만드는 방법 알아보기
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509723"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>자습서: Azure Red Hat OpenShift 4 클러스터 만들기

총 3부 중 1부에 해당하는 이 자습서에서는 OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터를 만들 환경을 준비한 후 클러스터를 만들겠습니다. 다음 방법을 알게 됩니다.
> [!div class="checklist"]
> * 필수 구성 요소를 설정하고 필요한 가상 네트워크 및 서브넷 만들기
> * 클러스터 배포

## <a name="before-you-begin"></a>시작하기 전에

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.75 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

### <a name="install-the-az-aro-extension"></a>`az aro` 확장 설치
`az aro` 확장을 설치하면 Azure CLI를 사용하여 명령줄에서 바로 Azure Red Hat OpenShift 클러스터를 만들고, 액세스하고, 삭제할 수 있습니다.

`az aro` 확장을 설치하려면 다음 명령을 실행합니다.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

확장이 이미 설치되어 있는 경우 다음 명령을 실행하여 업데이트할 수 있습니다.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>리소스 공급자 등록

다음으로, `Microsoft.RedHatOpenShift` 리소스 공급자를 구독에 등록해야 합니다.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

확장이 등록되었는지 확인하세요.

```azurecli-interactive
az -v
```

  아래와 비슷한 출력이 표시됩니다.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat 풀 비밀 가져오기(선택 사항)

Red Hat 풀 비밀을 사용하면 클러스터에서 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다. 이 단계는 선택 사항이지만 권장됩니다.

https://cloud.redhat.com/openshift/install/azure/aro-provisioned 로 이동한 후 *풀 비밀 다운로드*를 클릭하여 풀 비밀을 얻습니다.

회사 이메일로 Red Hat 계정에 로그인하거나 새 Red Hat 계정을 만들고 사용 약관에 동의해야 합니다.

저장된 `pull-secret.txt` 파일을 안전한 위치에 보관해 두세요. 각 클러스터 만들기에 사용됩니다.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>두 개의 빈 서브넷이 있는 가상 네트워크 만들기

다음으로, 두 개의 빈 서브넷이 있는 가상 네트워크를 만들겠습니다.

1. **다음 변수를 설정합니다.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **리소스 그룹 만들기**

    Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **가상 네트워크를 만듭니다.**

    OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터에는 마스터 및 작업자 노드에 사용할 두 개의 빈 서브넷이 있는 가상 네트워크가 필요합니다.

    이전에 만든 동일한 리소스 그룹에 새 가상 네트워크를 만듭니다.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    다음 예제 출력을 보면 가상 네트워크가 성공적으로 만들어졌습니다.

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **마스터 노드에 사용할 빈 서브넷을 추가합니다.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **작업자 노드에 사용할 빈 서브넷을 추가합니다.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **마스터 서브넷에서 [서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)합니다.** 클러스터에 연결하고 관리하는 데 필요합니다.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>클러스터 만들기

다음 명령을 실행하여 클러스터를 만듭니다. 선택 사항으로 풀 비밀을 전달할 수 있습니다. 그러면 클러스터에서 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다. [Red Hat OpenShift 클러스터 관리자](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)로 이동한 후 **풀 비밀 복사**를 클릭하여 풀 비밀에 액세스합니다.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> 클러스터를 만드는 데 보통 35분 정도 걸립니다.

>[!IMPORTANT]
> 사용자 지정 도메인(예: **foo.example.com**)을 지정하도록 선택하면 기본 제공 도메인 `https://console-openshift-console.apps.<random>.<location>.aroapp.io` 대신 `https://console-openshift-console.apps.foo.example.com` 같은 URL에서 OpenShift 콘솔을 사용할 수 있습니다.
>
> 기본적으로 OpenShift는 `*.apps.<random>.<location>.aroapp.io`에 생성되는 모든 경로에 자체 서명된 인증서를 사용합니다.  클러스터에 연결한 후 사용자 지정 DNS를 사용하도록 선택하면 OpenShift 설명서에 따라 [수신 컨트롤러의 사용자 지정 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) 및 [API 서버의 사용자 지정 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)를 구성해야 합니다.
>

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 필수 구성 요소를 설정하고 필요한 가상 네트워크 및 서브넷 만들기
> * 클러스터 배포

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터에 연결](tutorial-connect-cluster.md)
