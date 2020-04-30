---
title: Azure Red Hat OpenShift 4 개인 클러스터 만들기
description: OpenShift 4를 실행 하는 Azure Red Hat OpenShift 전용 클러스터를 만드는 방법에 대해 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: a0f726d32f2f63cf85101254fded005fc0b5a1db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233553"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Azure Red Hat OpenShift 4 개인 클러스터 만들기

이 문서에서는 OpenShift 4를 실행 하는 Azure Red Hat OpenShift 전용 클러스터를 만들기 위해 환경을 준비 합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 필수 구성 요소를 설정 하 고 필요한 가상 네트워크 및 서브넷 만들기
> * 개인 API 서버 끝점 및 개인 수신 컨트롤러를 사용 하 여 클러스터 배포

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 자습서에서는 Azure CLI 버전 2.0.75 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="install-the-az-aro-extension"></a>' Az aro ' 확장을 설치 합니다.
`az aro` 확장을 사용 하면 Azure CLI를 사용 하 여 명령줄에서 직접 Azure Red Hat openshift 클러스터를 만들고, 액세스 하 고, 삭제할 수 있습니다.

다음 명령을 실행 하 여 `az aro` 확장을 설치 합니다.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

확장이 이미 설치 되어 있는 경우 다음 명령을 실행 하 여를 업데이트할 수 있습니다.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>리소스 공급자 등록

다음으로 구독에 `Microsoft.RedHatOpenShift` 리소스 공급자를 등록 해야 합니다.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

확장이 등록 되어 있는지 확인 합니다.

```azurecli-interactive
az -v
```

  다음과 유사한 출력이 표시 됩니다.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="obtain-a-red-hat-pull-secret-optional"></a>Red Hat pull 비밀 가져오기 (선택 사항)

Red Hat pull 비밀을 사용 하면 클러스터에서 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다. 이 단계는 선택 사항이지만 권장됩니다.

로 이동 하 여 끌어오기 비밀 https://cloud.redhat.com/openshift/install/azure/aro-provisioned *다운로드*를 클릭 하 여 끌어오기 암호를 가져옵니다.

Red Hat 계정에 로그인 하거나 비즈니스 전자 메일을 사용 하 여 새 Red Hat 계정을 만들고 사용 약관에 동의 해야 합니다.

저장 `pull-secret.txt` 된 파일을 안전한 위치에 보관 합니다. 각 클러스터 만들기에 사용 됩니다.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>두 개의 빈 서브넷을 포함 하는 가상 네트워크 만들기

다음으로 두 개의 빈 서브넷을 포함 하는 가상 네트워크를 만듭니다.

1. **다음 변수를 설정합니다.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **리소스 그룹 만들기**

    Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [Az group create] [az-group-create] 명령을 사용 하 여 리소스 그룹을 만듭니다.

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

    OpenShift 4를 실행 하는 Azure Red Hat OpenShift 클러스터에는 마스터 및 작업자 노드에 대 한 두 개의 빈 서브넷이 있는 가상 네트워크가 필요 합니다.

    이전에 만든 것과 동일한 리소스 그룹에 새 가상 네트워크를 만듭니다.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    다음 예제 출력에서는 성공적으로 생성 된 가상 네트워크를 보여 줍니다.

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

3. **마스터 노드에 대 한 빈 서브넷을 추가 합니다.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **작업자 노드에 대 한 빈 서브넷을 추가 합니다.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **마스터 서브넷에서 [서브넷 개인 끝점 정책을 사용 하지 않도록 설정](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) 합니다.** 이는 클러스터를 연결 하 고 관리 하는 데 필요 합니다.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>클러스터 만들기

다음 명령을 실행 하 여 클러스터를 만듭니다. `apiserver-visibility` 및 `ingress-visibility` 매개 변수를 확인 합니다. 필요에 따라 끌어오기 암호를 전달 하 여 클러스터에서 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다. [Red Hat OpenShift 클러스터 관리자](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) 로 이동 하 고 Pull 비밀 복사를 클릭 하 여 끌어오기 비밀에 액세스 합니다.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain aro.example.com # [OPTIONAL] custom domain
  # --pull-secret 'Pull secret from https://cloud.redhat.com/openshift/install/azure/installer-provisioned/' # [OPTIONAL]
```

>[!NOTE]
> 클러스터를 만드는 데 일반적으로 약 35 분이 소요 됩니다.

>[!IMPORTANT]
> 사용자 지정 도메인 (예: **foo.example.com**)을 지정 하도록 선택 하는 경우 openshift 콘솔은 기본 제공 도메인 `https://console-openshift-console.apps.foo.example.com` `https://console-openshift-console.apps.<random>.<location>.aroapp.io`대신와 같은 URL에서 사용할 수 있습니다.
>
> 기본적으로 OpenShift는에서 `*.apps.<random>.<location>.aroapp.io`만든 모든 경로에 대해 자체 서명 된 인증서를 사용 합니다.  클러스터에 연결한 후 사용자 지정 DNS를 선택 하는 경우 OpenShift 설명서를 따라 수신 컨트롤러 및 [API 서버용 사용자 지정 ca](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html) [에 대 한 사용자 지정 ca를 구성](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) 해야 합니다.

## <a name="connect-to-the-private-cluster"></a>개인 클러스터에 연결

사용자를 `kubeadmin` 사용 하 여 클러스터에 로그인 할 수 있습니다.  다음 명령을 실행 하 여 `kubeadmin` 사용자에 대 한 암호를 찾습니다.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

다음 예제 출력에서는 암호를에 `kubeadminPassword`표시 하는 방법을 보여 줍니다.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

다음 명령을 실행 하 여 클러스터 콘솔 URL을 찾을 수 있습니다 .이는 다음과 같습니다.`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> 개인 Azure Red Hat OpenShift 클러스터에 연결 하려면 만든 Virtual Network 또는 클러스터가 배포 된 Virtual Network에 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 된 Virtual Network에 있는 호스트에서 다음 단계를 수행 해야 하는 단계를 수행 해야 합니다.

브라우저에서 콘솔 URL을 시작 하 고 자격 증명을 `kubeadmin` 사용 하 여 로그인 합니다.

![Azure Red Hat OpenShift 로그인 화면](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>OpenShift CLI 설치

OpenShift 웹 콘솔에 로그인 **한 후에는를** 클릭 합니다. 오른쪽 위에 있는 **명령줄 도구**를 클릭 합니다. 컴퓨터에 적절 한 릴리스를 다운로드 합니다.

![Azure Red Hat OpenShift 로그인 화면](media/aro4-download-cli.png)

에서 <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>컴퓨터에 적합 한 최신 버전의 CLI를 다운로드할 수도 있습니다.

## <a name="connect-using-the-openshift-cli"></a>OpenShift CLI를 사용 하 여 연결

API 서버 주소를 검색 합니다.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> 개인 Azure Red Hat OpenShift 클러스터에 연결 하려면 만든 Virtual Network 또는 클러스터가 배포 된 Virtual Network에 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 된 Virtual Network에 있는 호스트에서 다음 단계를 수행 해야 하는 단계를 수행 해야 합니다.

다음 명령을 사용 하 여 OpenShift 클러스터의 API 서버에 로그인 합니다. ** \<Kubeadmin password>** 를 방금 검색 한 암호로 바꿉니다.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 OpenShift 4를 실행 하는 Azure Red Hat OpenShift 클러스터를 배포 했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 필수 구성 요소를 설정 하 고 필요한 가상 네트워크 및 서브넷 만들기
> * 클러스터 배포
> * 사용자를 `kubeadmin` 사용 하 여 클러스터에 연결

Azure Active Directory를 사용 하 여 인증을 위해 클러스터를 구성 하는 방법을 알아보려면 다음 문서로 이동 합니다.


* [명령줄을 사용 하 여 Azure Active Directory 인증 구성](configure-azure-ad-cli.md)


* [Azure Portal 및 OpenShift 웹 콘솔을 사용 하 여 Azure Active Directory 인증 구성](configure-azure-ad-cli.md)
