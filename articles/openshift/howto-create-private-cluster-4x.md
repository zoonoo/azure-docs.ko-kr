---
title: Azure Red Hat OpenShift 4 프라이빗 클러스터 만들기
description: OpenShift 4를 실행하는 Azure Red Hat OpenShift 프라이빗 클러스터를 만드는 방법 알아보기
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: georgewallace
ms.author: gwallace
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: eedd796e3223c6cbcd0844cc4e814a35172d6c7e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078530"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Azure Red Hat OpenShift 4 프라이빗 클러스터 만들기

이 문서에서는 OpenShift 4를 실행하는 Azure Red Hat OpenShift 프라이빗 클러스터를 만들기 위한 환경을 준비합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 필수 구성 요소를 설정하고 필요한 가상 네트워크 및 서브넷 만들기
> * 프라이빗 API 서버 엔드포인트 및 프라이빗 수신 컨트롤러를 사용하여 클러스터 배포

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="register-the-resource-providers"></a>리소스 공급자 등록

1. 여러 Azure 구독이 있는 경우 관련 구독 ID를 지정합니다.

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. `Microsoft.RedHatOpenShift` 리소스 공급자를 등록합니다.

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```

1. `Microsoft.Compute` 리소스 공급자를 등록합니다.

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```

1. `Microsoft.Storage` 리소스 공급자를 등록합니다.

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat 풀 비밀 가져오기(선택 사항)

Red Hat 풀 비밀을 사용하면 클러스터에서 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다. 이 단계는 선택 사항이지만 권장됩니다.

1. **[Red Hat OpenShift 클러스터 관리자 포털로 이동](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)하여 로그인합니다.**

   회사 이메일로 Red Hat 계정에 로그인하거나 새 Red Hat 계정을 만들고 사용 약관에 동의해야 합니다.

2. **풀 비밀 다운로드를 클릭합니다.**

저장된 `pull-secret.txt` 파일을 안전한 위치에 보관해 두세요. 각 클러스터 만들기에 사용됩니다.

`az aro create` 명령을 실행하는 경우 `--pull-secret @pull-secret.txt` 매개 변수를 사용하여 풀 비밀을 참조할 수 있습니다. `pull-secret.txt` 파일을 저장한 디렉터리에서 `az aro create`를 실행합니다. 그렇지 않으면 `@pull-secret.txt`를 `@<path-to-my-pull-secret-file`로 바꿉니다.

풀 비밀을 복사하거나 다른 스크립트에서 참조하는 경우에는 풀 비밀을 유효한 JSON 문자열로 포맷해야 합니다.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>두 개의 빈 서브넷이 있는 가상 네트워크 만들기

다음으로, 두 개의 빈 서브넷이 있는 가상 네트워크를 만들겠습니다.

1. **다음 변수를 설정합니다.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
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

5. **마스터 서브넷에서 [서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정](../private-link/disable-private-link-service-network-policy.md)합니다.** 클러스터에 연결하고 관리하는 데 필요합니다.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>클러스터 만들기

다음 명령을 실행하여 클러스터를 만듭니다. 필요에 따라 [Red Hat 풀 비밀을 전달](#get-a-red-hat-pull-secret-optional)할 수 있습니다. 그러면 클러스터에서 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다.

>[!NOTE]
> 복사/붙여넣기 명령 및 선택적 매개 변수 중 하나를 사용하는 경우 초기 해시 태그 및 후행 주석 텍스트를 삭제해야 합니다. 또한 뒤에 오는 백슬래시를 사용하여 명령의 이전 줄에서 인수를 닫습니다.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

`az aro create` 명령을 실행한 후 클러스터를 만드는 데 일반적으로 약 35분이 소요됩니다.

>[!IMPORTANT]
> 사용자 지정 도메인(예: **foo.example.com**)을 지정하도록 선택하면 기본 제공 도메인 `https://console-openshift-console.apps.<random>.<location>.aroapp.io` 대신 `https://console-openshift-console.apps.foo.example.com` 같은 URL에서 OpenShift 콘솔을 사용할 수 있습니다.
>
> 기본적으로 OpenShift는 `*.apps.<random>.<location>.aroapp.io`에서 생성되는 모든 경로에 자체 서명된 인증서를 사용합니다.  클러스터에 연결한 후 사용자 지정 DNS를 선택하는 경우 OpenShift 설명서에 따라 [수신 컨트롤러의 사용자 지정 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) 및 [API 서버의 사용자 지정 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)를 구성해야 합니다.

## <a name="connect-to-the-private-cluster"></a>프라이빗 클러스터에 연결

`kubeadmin` 사용자를 사용하여 클러스터에 로그인할 수 있습니다.  다음 명령을 실행하여 `kubeadmin` 사용자의 암호를 찾습니다.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

다음 예제 출력에서는 암호가 `kubeadminPassword`에 있는 것을 보여줍니다.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

다음 명령을 실행하여 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 형식의 클러스터 콘솔 URL을 찾을 수 있습니다.

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> 프라이빗 Azure Red Hat OpenShift 클러스터에 연결하려면 사용자가 만든 Virtual Network 또는 클러스터가 배포된 Virtual Network로 [피어링](../virtual-network/virtual-network-peering-overview.md)된 Virtual Network가 있는 호스트에서 다음 단계를 수행해야 합니다.

브라우저에서 콘솔 URL을 시작하고 `kubeadmin` 자격 증명을 사용하여 로그인합니다.

![Azure Red Hat OpenShift 로그인 화면](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>OpenShift CLI 설치

OpenShift 웹 콘솔에 로그인했으면 오른쪽 위에서 **?** 를 클릭한 다음, **명령줄 도구**를 클릭합니다. 머신에 맞는 릴리스를 다운로드합니다.

![Azure Red Hat OpenShift 로그인 화면](media/aro4-download-cli.png)

<https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>에서 현재 사용하는 머신에 맞는 최신 CLI 릴리스를 다운로드할 수도 있습니다.

## <a name="connect-using-the-openshift-cli"></a>OpenShift CLI를 사용하여 연결

API 서버 주소를 검색합니다.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> 프라이빗 Azure Red Hat OpenShift 클러스터에 연결하려면 사용자가 만든 Virtual Network 또는 클러스터가 배포된 Virtual Network로 [피어링](../virtual-network/virtual-network-peering-overview.md)된 Virtual Network가 있는 호스트에서 다음 단계를 수행해야 합니다.

다음 명령을 사용하여 OpenShift 클러스터의 API 서버에 로그인합니다. **\<kubeadmin password>** 를 방금 검색한 암호로 바꿉니다.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터가 배포되었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 필수 구성 요소를 설정하고 필요한 가상 네트워크 및 서브넷 만들기
> * 클러스터 배포
> * `kubeadmin` 사용자를 사용하여 클러스터에 연결

다음 문서로 이동하여 Azure Active Directory를 통해 인증하도록 클러스터를 구성하는 방법을 알아봅니다.


* [명령줄을 사용하여 Azure Active Directory로 인증 구성](configure-azure-ad-cli.md)


* [Azure Portal 및 OpenShift 콘솔을 사용하여 Azure Active Directory로 인증 구성](configure-azure-ad-cli.md)