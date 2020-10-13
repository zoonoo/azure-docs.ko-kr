---
title: 프라이빗 링크 설정
description: 컨테이너 레지스트리에서 개인 끝점을 설정 하 고 로컬 가상 네트워크에서 개인 링크를 통해 액세스를 사용 하도록 설정 합니다. 개인 링크 액세스는 프리미엄 서비스 계층의 기능입니다.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 793003edea853922f78b36f0dc1a6e35205cdadb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743644"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Azure 개인 링크를 사용 하 여 Azure container registry에 비공개로 연결


[Azure 개인 링크](../private-link/private-link-overview.md)를 사용 하 여 가상 네트워크 개인 IP 주소를 레지스트리 끝점에 할당 하 여 레지스트리에 대 한 액세스를 제한 합니다. 가상 네트워크의 클라이언트와 레지스트리의 개인 끝점 간의 네트워크 트래픽은 Microsoft 백본 네트워크에서 가상 네트워크와 개인 링크를 순회 하 여 공용 인터넷의 노출을 제거 합니다. 또한 개인 링크를 통해 [Azure express](../expressroute/expressroute-introduction.MD) 경로 개인 피어 링 또는 [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 통해 온-프레미스에서 개인 레지스트리 액세스를 사용할 수 있습니다.

레지스트리의 전용 끝점에 대 한 [DNS 설정을 구성할](../private-link/private-endpoint-overview.md#dns-configuration) 수 있습니다. 그러면 설정이 레지스트리의 할당 된 개인 IP 주소로 확인 됩니다. DNS 구성을 사용하는 경우, 네트워크의 클라이언트 및 서비스는 *myregistry.azurecr.io*와 같은 레지스트리의 정규화된 도메인 이름(FQDN)에서 레지스트리에 계속 액세스할 수 있습니다. 

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 현재 레지스트리에 대해 최대 10 개의 개인 끝점을 설정할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조하세요.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서에서 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.6.0 이상이 권장됩니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요. 또는 [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 실행합니다.
* 컨테이너 레지스트리가 아직 없는 경우, 하나(프리미엄 계층 필요)를 만들고 Docker Hub의 `hello-world`와 같은 샘플 이미지 [가져옵니다](container-registry-import-images.md). 예를 들어 [Azure Portal][quickstart-portal] 또는 [Azure CLI][quickstart-cli]를 사용하여 레지스트리를 만듭니다.
* 다른 Azure 구독의 프라이빗 링크를 사용하여 레지스트리 액세스를 구성하려면 해당 구독에 Azure Container Registry에 대한 리소스 공급자를 등록해야 합니다. 다음은 그 예입니다.

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

이 문서의 Azure CLI 예제에서는 다음과 같은 환경 변수들을 사용합니다. 사용자 환경에 적절한 값으로 대체합니다. 모든 예제는 다음과 같이 Bash 셸용으로 서식이 지정되어 있습니다.

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>프라이빗 링크 설정 - CLI

### <a name="get-network-and-subnet-names"></a>네트워크 및 서브넷 이름 가져오기

가상 네트워크 및 서브넷의 이름이 아직 없다면 프라이빗 링크를 설정하기 위해 필요합니다. 이 예제에서는 VM에 대해 동일한 서브넷을 사용하며 레지스트리의 프라이빗 엔드포인트를 사용합니다. 그러나 많은 시나리오에서는 별도의 서브넷에서 엔드포인트를 설정합니다. 

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에서 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 머신의 이름을 기반으로 합니다. 예를 들어 가상 머신의 이름을 *myDockerVM*으로 지정하는 경우, 기본 가상 네트워크 이름은 *myDockerVMVNET*으로 설정되며 서브넷 이름은 *myDockerVMSubnet*으로 지정됩니다. [az network vnet list][az-network-vnet-list] 명령을 실행하여 환경 변수에서 이러한 값들을 다음과 같이 설정합니다.

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>서브넷에서 네트워크 정책 사용 안 함

프라이빗 엔드포인트에 대한 서브넷의 네트워크 보안 그룹과 같은 [네트워크 정책을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)합니다. [az 네트워크 vnet 서브넷 업데이트][az-network-vnet-subnet-update]를 사용해 서브넷 구성을 다음과 같이 업데이트합니다.

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

개인 Azure container registry 도메인에 대 한 [개인 DNS 영역](../dns/private-dns-privatednszone.md) 을 만듭니다. 이후 단계에서는 이 DNS 영역에서 레지스트리 도메인에 대한 DNS 레코드를 만듭니다.

프라이빗 영역을 사용하여 Azure Container Registry에 대한 기본 DNS 확인을 재정의하려면 영역 이름을 **privatelink.azurecr.io**로 지정해야 합니다. 다음과 같은 [az network private-dns zone create][az-network-private-dns-zone-create] 명령을 실행하여 프라이빗 영역을 만듭니다.

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>연결 링크 만들기

[az network private-dns link vnet create][az-network-private-dns-link-vnet-create]를 실행하여 프라이빗 영역을 가상 네트워크와 연결합니다. 이 예제에서는 *myDNSLink*라는 링크를 만듭니다.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>프라이빗 레지스트리 엔드포인트 만들기

이 섹션에서는 가상 네트워크에 레지스트리의 프라이빗 엔드포인트를 만듭니다. 먼저 레지스트리의 리소스 ID를 가져옵니다.

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

[az network private-endpoint create][az-network-private-endpoint-create] 명령을 실행하여 레지스트리의 프라이빗 엔드포인트를 만듭니다.

다음 예제에서는 엔드포인트 *myPrivateEndpoint*와 서비스 연결 *myConnection*을 만듭니다. 엔드포인트에 대한 컨테이너 레지스트리 리소스를 지정하려면 다음과 같이 `--group-ids registry`를 전달합니다.

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>개인 IP 주소 가져오기

[az network private-endpoint show][az-network-private-endpoint-show]를 실행하여 네트워크 인터페이스 ID에 대한 엔드포인트를 쿼리합니다.

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

이 예제에서 네트워크 인터페이스와 연결된 것은 컨테이너 레지스트리에 대한 두 개의 개인 IP 주소인데, 하나는 레지스트리 자체에 대한 주소이며 다른 하나는 레지스트리의 데이터 엔드포인트에 대한 주소입니다. 다음 [az resource show][az-resource-show] 명령을 실행하면 컨테이너 레지스트리와 레지스트리의 데이터 엔드포인트에 대한 개인 IP 주소를 가져옵니다.

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> 레지스트리가 [지역에서 복제](container-registry-geo-replication.md)된 경우 각 레지스트리 복제본의 추가 데이터 엔드포인트를 쿼리합니다.

### <a name="create-dns-records-in-the-private-zone"></a>프라이빗 영역에서 DNS 레코드 만들기

다음 명령을 실행하면 레지스트리 엔드포인트와 그 데이터 엔드포인트에 대한 DNS 레코드가 프라이빗 영역에 생성됩니다. 예를 들어 *westeurope* 지역에 *myregistry*라는 이름의 레지스트리가 있다면 엔드포인트 이름은 `myregistry.azurecr.io` 및 `myregistry.westeurope.data.azurecr.io`입니다. 

> [!NOTE]
> 레지스트리가 [지역에서 복제](container-registry-geo-replication.md)된 경우, 각 복제본의 데이터 엔드포인트 IP에 대해 DNS 레코드를 추가로 만듭니다.

먼저 [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] 명령을 실행하여 레지스트리 엔드포인트 및 데이터 엔드포인트에 대해 빈 A 레코드 집합을 만듭니다.

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

[az network private-dns record-set add-record][az-network-private-dns-record-set-a-add-record] 명령을 실행하여 레지스트리 엔드포인트 및 데이터 엔드포인트에 대한 A 레코드를 만듭니다.

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

이제 프라이빗 링크가 구성되었으며 사용할 준비가 되었습니다.

## <a name="set-up-private-link---portal"></a>프라이빗 링크 설정 - 포털

레지스트리를 만들 때 프라이빗 링크를 설정하거나 기존 레지스트리에 프라이빗 링크를 추가합니다. 다음 단계는 테스트를 위해 VM을 사용하여 가상 네트워크 및 서브넷을 이미 설정했다고 가정합니다. [새 가상 네트워크 및 서브넷을 만들](../virtual-network/quick-create-portal.md) 수도 있습니다.

### <a name="create-a-private-endpoint---new-registry"></a>프라이빗 엔드포인트 만들기 - 새 레지스트리

1. 포털에서 레지스트리를 만들 때 **기본** 탭의 **SKU**에서 **프리미엄**을 선택합니다.
1. **네트워킹** 탭을 선택합니다.
1. **네트워크 연결**에서 **프라이빗 엔드포인트** >  **+ 추가**를 선택합니다.
1. 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 기존 그룹의 이름을 입력하거나 새 그룹을 만듭니다.|
    | 속성 | 고유한 이름을 입력합니다. |
    | 하위 리소스 |**레지스트리**를 선택합니다.|
    | **네트워킹** | |
    | 가상 네트워크| 가상 머신이 배포된 가상 네트워크(예: *myDockerVMVNET*)를 선택합니다. |
    | 서브넷 | 가상 머신이 배포된 서브넷(예: *myDockerVMSubnet*)을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |(새) *privatelink.azurecr.io*를 선택합니다. |
    |||
1. 나머지 레지스트리 설정을 구성한 다음, **검토 + 만들기**를 선택합니다.

  ![프라이빗 엔드포인트를 사용하여 레지스트리 만들기](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>프라이빗 엔드포인트 만들기 - 기존 레지스트리

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정**에서 **네트워킹**을 선택합니다.
1. **프라이빗 엔드포인트** 탭에서 **+ 프라이빗 엔드포인트**를 선택합니다.
1. **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 기존 그룹의 이름을 입력하거나 새 그룹을 만듭니다.|
    | **인스턴스 세부 정보** |  |
    | 속성 | 이름을 입력합니다. |
    |지역|지역을 선택합니다.|
    |||
5. 완료되면 **다음: 리소스**를 선택합니다.
6. 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | **내 디렉터리의 Azure 리소스에 연결**을 선택합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.ContainerRegistry/registries**를 선택합니다. |
    | 리소스 |레지스트리의 이름을 선택|
    |대상 하위 리소스 |**레지스트리**를 선택합니다.|
    |||
7. 완료되면 **다음: 구성**을 선택합니다.
8. 다음과 같이 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| 가상 머신이 배포된 가상 네트워크(예: *myDockerVMVNET*)를 선택합니다. |
    | 서브넷 | 가상 머신이 배포된 서브넷(예: *myDockerVMSubnet*)을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |(새) *privatelink.azurecr.io*를 선택합니다. |
    |||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
2. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

프라이빗 엔드포인트를 만든 후 프라이빗 영역의 DNS 설정은 포털의 **프라이빗 엔드포인트** 페이지에 다음과 같이 표시됩니다.

1. 포털에서 컨테이너 레지스트리로 이동하고 **설정 > 네트워킹**을 선택합니다.
1. 사용자가 만든 프라이빗 엔드포인트를 **프라이빗 엔드포인트** 탭에서 선택합니다.
1. **개요** 페이지에서 링크 설정 및 사용자 지정 DNS 설정을 검토합니다.

  ![엔드포인트 DNS 설정](./media/container-registry-private-link/private-endpoint-overview.png)

이제 프라이빗 링크가 구성되었으며 사용할 준비가 되었습니다.

## <a name="disable-public-access"></a>공용 액세스 사용 안 함

상당수 시나리오의 경우, 공용 네트워크의 레지스트리 액세스를 사용하지 않도록 설정합니다. 이 구성을 적용하면 가상 네트워크 외부의 클라이언트가 레지스트리 엔드포인트에 도달하지 않습니다. 

### <a name="disable-public-access---cli"></a>공용 액세스 사용 안 함 - CLI

Azure CLI를 통해 공용 액세스를 사용하지 않으려면 [az acr update][az-acr-update]를 실행하고 `--public-network-enabled`를 `false`로 설정합니다. 

> [!NOTE]
> `public-network-enabled` 인수에는 Azure CLI 2.6.0 이상이 필요합니다. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>공용 액세스 사용 안 함 - 포털

1. 포털에서 컨테이너 레지스트리로 이동하고 **설정 > 네트워킹**을 선택합니다.
1. **공용 액세스** 탭의 **공용 네트워크 액세스 허용**에서 **사용 안 함**을 선택합니다. 그런 다음 **저장**을 선택합니다.

## <a name="validate-private-link-connection"></a>프라이빗 링크 연결 유효성 검사

프라이빗 엔드포인트의 동일한 서브넷 내에 있는 리소스가 개인 IP 주소를 통해 레지스트리에 연결되고 프라이빗 DNS 영역이 올바르게 통합되었는지 확인해야 합니다.

프라이빗 링크 연결의 유효성을 검사하려면 가상 네트워크에서 설정한 가상 머신에 대해 SSH를 수행합니다.

또는와 같은 유틸리티를 `nslookup` 실행 `dig` 하 여 개인 링크를 통해 레지스트리의 IP 주소를 조회 합니다. 예를 들면 다음과 같습니다.

```bash
dig $REGISTRY_NAME.azurecr.io
```

예제 출력은 서브넷의 주소 공간에 있는 레지스트리의 IP 주소를 다음과 같이 표시합니다.

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

이 결과를 공용 엔드포인트에서 동일한 레지스트리에 대한 `dig` 출력의 공용 IP 주소와 다음과 같이 비교합니다.

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>프라이빗 링크를 통한 레지스트리 작업

또한 서브넷의 가상 머신에서 레지스트리 작업을 수행할 수 있는지 확인합니다. 가상 머신에 대한 SSH 연결을 설정하고 [az acr login][az-acr-login] 명령을 실행하여 레지스트리에 로그인합니다. VM 구성에 따라 다음 명령에 `sudo`를 접두사로 붙여야 할 수도 있습니다.

```bash
az acr login --name $REGISTRY_NAME
```

`docker pull`과 같은 레지스트리 작업을 수행하여 레지스트리에서 샘플 이미지를 끌어옵니다. `hello-world:v1`을 레지스트리에 적합한 이미지 및 태그로 바꾸고 레지스트리 로그인 서버 이름(모두 소문자)을 접두사로 사용합니다.

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker는 이미지를 VM으로 끌어오는 데 성공합니다.

## <a name="manage-private-endpoint-connections"></a>프라이빗 엔드포인트 연결 관리

Azure Portal을 사용하여 레지스트리의 프라이빗 엔드포인트 연결을 관리하거나 [az acr private-endpoint-connection][az-acr-private-endpoint-connection] 명령 그룹의 명령을 사용하여 이 연결을 관리합니다. 작업에는 레지스트리의 프라이빗 엔드포인트 연결의 승인, 삭제, 나열, 거부 또는 세부 정보 표시가 포함됩니다.

예를 들어, 레지스트리의 프라이빗 엔드포인트 연결을 나열하려면 [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] 명령을 실행합니다. 다음은 그 예입니다.

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

이 문서의 단계를 사용하여 프라이빗 엔드포인트 연결을 설정하면 레지스트리에 대해 RBAC 권한이 있는 클라이언트 및 서비스의 연결이 자동으로 허용됩니다. 연결의 수동 승인을 요구하도록 엔드포인트를 설정할 수 있습니다. 프라이빗 엔드포인트 연결을 승인 및 거부하는 방법에 대한 자세한 내용은 [프라이빗 엔드포인트 연결 관리](../private-link/manage-private-endpoint.md)를 참조하세요.

## <a name="add-zone-records-for-replicas"></a>복제본에 대한 영역 레코드 추가

이 문서에 표시 된 것 처럼 레지스트리에 개인 끝점 연결을 추가 하는 경우 레지스트리에 `privatelink.azurecr.io` 대 한 영역 및 레지스트리가 [복제](container-registry-geo-replication.md)된 지역에서 해당 데이터 끝점에 DNS 레코드를 만듭니다. 

나중에 새 복제본을 추가하는 경우, 해당 지역에서 데이터 엔드포인트에 대한 새 영역 레코드를 수동으로 추가해야 합니다. 예를 들어 *northeurope* 위치에서 *myregistry*의 복제본을 만드는 경우, `myregistry.northeurope.data.azurecr.io`에 대한 영역 레코드를 추가합니다. 단계에 관한 설명은 이 문서의 [프라이빗 영역에서 DNS 레코드 만들기](#create-dns-records-in-the-private-zone)를 참조하세요.

## <a name="dns-configuration-options"></a>DNS 구성 옵션

이 예제의 개인 끝점은 기본 가상 네트워크와 연결 된 개인 DNS 영역과 통합 됩니다. 이 설치 프로그램은 Azure에서 제공 하는 DNS 서비스를 직접 사용 하 여 레지스트리의 공용 FQDN을 가상 네트워크의 개인 IP 주소에 대해 확인 합니다. 

개인 링크는 사용자 지정 DNS 솔루션을 포함 하 여 개인 영역을 사용 하는 추가 DNS 구성 시나리오를 지원 합니다. 예를 들어 가상 네트워크에 배포 된 사용자 지정 DNS 솔루션이 있거나 네트워크의 온-프레미스에서 VPN gateway를 사용 하 여 가상 네트워크에 연결 하는 경우가 있을 수 있습니다. 이러한 시나리오에서 레지스트리의 공용 FQDN을 개인 IP 주소로 확인 하려면 Azure DNS 서비스 (168.63.129.16)에 대 한 서버 수준 전달자를 구성 해야 합니다. 정확한 구성 옵션 및 단계는 기존 네트워크 및 DNS에 종속 됩니다. 예제는 [Azure 개인 끝점 DNS 구성](../private-link/private-endpoint-dns.md)을 참조 하세요.

## <a name="clean-up-resources"></a>리소스 정리

동일한 리소스 그룹에서 모든 Azure 리소스를 만들었으며 이 리소스가 더 이상 필요하지 않은 경우, 단일한 [az group delete](/cli/azure/group) 명령을 사용하여 리소스를 선택적으로 삭제할 수 있습니다.

```azurecli
az group delete --name $RESOURCE_GROUP
```

포털에서 리소스를 정리하려면 리소스 그룹으로 이동합니다. 리소스 그룹이 로드되면 **리소스 그룹 삭제**를 클릭하여 리소스 그룹 및 이 그룹에 저장된 리소스를 제거합니다.

## <a name="next-steps"></a>다음 단계

* Private Link에 대해 자세히 알아보려면 [Azure Private Link](../private-link/private-link-overview.md) 설명서를 참조하세요.
* 클라이언트 방화벽 뒤에서 레지스트리 액세스 규칙을 설정해야 할 경우, [방화벽 뒤의 Azure 컨테이너 레지스트리에 액세스하기 위한 규칙 구성](container-registry-firewall-access-rules.md)을 참조하세요.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
