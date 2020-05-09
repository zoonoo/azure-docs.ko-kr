---
title: 개인 링크 설정
description: 컨테이너 레지스트리에서 개인 끝점을 설정 하 고 로컬 가상 네트워크에서 개인 링크를 통해 액세스를 사용 하도록 설정 합니다.
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982447"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Azure container registry에 대 한 Azure 개인 링크 구성 

[Azure 개인 링크](../private-link/private-link-overview.md)를 사용 하 여 레지스트리 끝점에 가상 네트워크 개인 IP 주소를 할당 하 여 레지스트리에 대 한 액세스를 제한 합니다. 가상 네트워크의 클라이언트와 레지스트리의 네트워크 트래픽이 Microsoft 백본 네트워크의 가상 네트워크와 개인 링크를 통과 하 여 공용 인터넷의 노출을 제거 합니다.

설정이 레지스트리의 할당 된 개인 IP 주소로 확인 되도록 개인 끝점에 대 한 [DNS 설정을 구성할](../private-link/private-endpoint-overview.md#dns-configuration) 수 있습니다. DNS 구성을 사용 하는 경우 네트워크의 클라이언트 및 서비스는 레지스트리의 정규화 된 도메인 이름 (예: *myregistry.azurecr.io*)에서 레지스트리에 계속 액세스할 수 있습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대 한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조 하세요.

## <a name="things-to-know"></a>알아야 할 사항

* 현재 Azure Security Center를 사용 하는 이미지 검색은 개인 끝점으로 구성 된 레지스트리에서 사용할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서의 Azure CLI 단계를 사용 하려면 Azure CLI 버전 2.2.0 이상을 사용 하는 것이 좋습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요. 또는 [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 실행 합니다.
* 컨테이너 레지스트리가 아직 없는 경우 하나 (프리미엄 계층 필요)를 만들고 Docker 허브에서와 [import](container-registry-import-images.md) `hello-world` 같은 샘플 이미지를 가져옵니다. 예를 들어 [Azure Portal][quickstart-portal] 또는 [Azure CLI][quickstart-cli] 를 사용 하 여 레지스트리를 만듭니다.
* 다른 Azure 구독의 개인 링크를 사용 하 여 레지스트리 액세스를 구성 하려면 해당 구독에 Azure Container Registry에 대 한 리소스 공급자를 등록 해야 합니다. 다음은 그 예입니다. 

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

이 문서의 Azure CLI 예제에서는 다음 환경 변수를 사용 합니다. 사용자 환경에 적합 한 값으로 대체 합니다. 모든 예제는 Bash 셸에 대해 서식 지정 됩니다.

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>개인 링크 설정-CLI

### <a name="get-network-and-subnet-names"></a>네트워크 및 서브넷 이름 가져오기

아직 없는 경우 개인 링크를 설정 하려면 가상 네트워크 및 서브넷의 이름이 필요 합니다. 이 예제에서는 VM에 대해 동일한 서브넷을 사용 하 고 레지스트리의 개인 끝점을 사용 합니다. 그러나 많은 시나리오에서는 별도의 서브넷에서 끝점을 설정 합니다. 

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 컴퓨터의 이름을 기반으로 합니다. 예를 들어 가상 컴퓨터 *Mydockervm*의 이름을 지정 하는 경우 기본 가상 네트워크 이름은 Mydockervmvnet 이며 서브넷은 *Mydockervm*입니다. *myDockerVMVNET* [Az network vnet list][az-network-vnet-list] 명령을 실행 하 여 환경 변수에서 이러한 값을 설정 합니다.

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

개인 끝점의 서브넷에서 네트워크 보안 그룹 같은 [네트워크 정책을 사용 하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md) 합니다. [Az network vnet subnet update][az-network-vnet-subnet-update]를 사용 하 여 서브넷 구성을 업데이트 합니다.

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>개인 DNS 영역 구성

개인 Azure container registry 도메인에 대 한 개인 DNS 영역을 만듭니다. 이후 단계에서는이 DNS 영역에서 레지스트리 도메인에 대 한 DNS 레코드를 만듭니다.

개인 영역을 사용 하 여 Azure container registry에 대 한 기본 DNS 확인을 재정의 하려면 영역 이름을 **privatelink.azurecr.io**로 지정 해야 합니다. 다음 [az network private-dns zone create][az-network-private-dns-zone-create] 명령을 실행 하 여 개인 영역을 만듭니다.

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>연결 링크 만들기

[Az network private-dns link vnet create][az-network-private-dns-link-vnet-create] 를 실행 하 여 개인 영역을 가상 네트워크에 연결 합니다. 이 예제에서는 *Mydnslink*라는 링크를 만듭니다.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>개인 레지스트리 끝점 만들기

이 섹션에서는 가상 네트워크에 레지스트리의 개인 끝점을 만듭니다. 먼저 레지스트리의 리소스 ID를 가져옵니다.

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

[Az network private-endpoint create][az-network-private-endpoint-create] 명령을 실행 하 여 레지스트리의 개인 끝점을 만듭니다.

다음 예제에서는 끝점 *myPrivateEndpoint* 및 서비스 연결 *myconnection*을 만듭니다. 끝점에 대 한 컨테이너 레지스트리 리소스를 지정 하려면 다음 `--group-ids registry`을 전달 합니다.

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

[Az network private endpoint show][az-network-private-endpoint-show] 를 실행 하 여 네트워크 인터페이스 ID에 대 한 끝점을 쿼리 합니다.

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

이 예제에서 네트워크 인터페이스와 연결 된 컨테이너 레지스트리에 대 한 두 개의 개인 IP 주소입니다. 하나는 레지스트리 자체를 위한 것이 고 다른 하나는 레지스트리의 데이터 끝점입니다. 다음 [az resource show][az-resource-show] 명령은 컨테이너 레지스트리와 레지스트리의 데이터 끝점에 대 한 개인 IP 주소를 가져옵니다.

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
> 레지스트리가 [지역에서 복제](container-registry-geo-replication.md)되는 경우 각 레지스트리 복제본의 추가 데이터 끝점을 쿼리 합니다.

### <a name="create-dns-records-in-the-private-zone"></a>개인 영역에서 DNS 레코드 만들기

다음 명령은 레지스트리 끝점과 해당 데이터 끝점에 대 한 DNS 레코드를 개인 영역에 만듭니다. 예를 들어 *westeurope* 지역에 `myregistry.azurecr.io` *myregistry* 라는 레지스트리가 있는 경우 끝점 이름은 및 `myregistry.westeurope.data.azurecr.io`입니다. 

> [!NOTE]
> 레지스트리가 [지역에서 복제](container-registry-geo-replication.md)되는 경우 각 복제본의 데이터 끝점 IP에 대해 추가로 DNS 레코드를 만듭니다.

첫 번째 실행 [az network private-dns record-만들기를 설정][az-network-private-dns-record-set-a-create] 하 여 레지스트리 끝점과 데이터 끝점에 대 한 빈 레코드 집합을 만듭니다.

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

[Az network private-dns record-add-record][az-network-private-dns-record-set-a-add-record] 명령을 실행 하 여 레지스트리 끝점과 데이터 끝점에 대 한 a 레코드를 만듭니다.

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

이제 개인 링크가 구성 되어 사용할 준비가 되었습니다.

## <a name="set-up-private-link---portal"></a>개인 링크 설정-포털

레지스트리를 만들 때 개인 링크를 설정 하거나 기존 레지스트리에 개인 링크를 추가 합니다. 다음 단계는 테스트를 위해 VM을 사용 하 여 가상 네트워크 및 서브넷을 이미 설정 했다고 가정 합니다. [새 가상 네트워크 및 서브넷을 만들](../virtual-network/quick-create-portal.md)수도 있습니다.

### <a name="create-a-private-endpoint---new-registry"></a>개인 끝점 만들기-새 레지스트리

1. 포털에서 레지스트리를 만들 때 **기본** 탭의 **SKU**에서 **프리미엄**을 선택 합니다.
1. **네트워킹** 탭을 선택 합니다.
1. **네트워크 연결**에서 **개인 끝점** > **+ 추가**를 선택 합니다.
1. 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 기존 그룹의 이름을 입력 하거나 새 그룹을 만듭니다.|
    | 이름 | 고유한 이름을 입력합니다. |
    | Subresource |**레지스트리** 선택|
    | **네트워킹** | |
    | 가상 네트워크| 가상 컴퓨터가 배포 된 가상 네트워크 (예: *Mydockervmvnet*)를 선택 합니다. |
    | 서브넷 | 가상 머신이 배포 되는 *Mydockervmsubnet* 과 같은 서브넷을 선택 합니다. |
    |**사설 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |*(신규) privatelink.azurecr.io* 선택 |
    |||
1. 나머지 레지스트리 설정을 구성한 다음 **검토 + 만들기**를 선택 합니다.

  ![개인 끝점을 사용 하 여 레지스트리 만들기](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>개인 끝점 만들기-기존 레지스트리

1. 포털에서 컨테이너 레지스트리로 이동 합니다.
1. **설정**에서 **네트워킹**을 선택 합니다.
1. **전용 끝점** 탭에서 **+ 개인 끝점**을 선택 합니다.
1. **기본 사항** 탭에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | Value |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 기존 그룹의 이름을 입력 하거나 새 그룹을 만듭니다.|
    | **인스턴스 세부 정보** |  |
    | 이름 | 이름을 입력합니다. |
    |지역|지역을 선택합니다.|
    |||
5. 완료되면 **다음: 리소스**를 선택합니다.
6. 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | **내 디렉터리에서 Azure 리소스에 연결을**선택 합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.containerregistry/** registry를 선택 합니다. |
    | 리소스 |레지스트리 이름 선택|
    |대상 하위 리소스 |**레지스트리** 선택|
    |||
7. 완료되면 **다음: 구성**을 선택합니다.
8. 정보를 입력 하거나 선택 합니다.

    | 설정 | Value |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| 가상 컴퓨터가 배포 된 가상 네트워크 (예: *Mydockervmvnet*)를 선택 합니다. |
    | 서브넷 | 가상 머신이 배포 되는 *Mydockervmsubnet* 과 같은 서브넷을 선택 합니다. |
    |**사설 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |*(신규) privatelink.azurecr.io* 선택 |
    |||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
2. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

개인 끝점을 만든 후 개인 영역의 DNS 설정이 포털의 **개인 끝점** 페이지에 표시 됩니다.

1. 포털에서 컨테이너 레지스트리로 이동 하 고 **설정 > 네트워킹**을 선택 합니다.
1. **개인 끝점** 탭에서 사용자가 만든 개인 끝점을 선택 합니다.
1. **개요** 페이지에서 링크 설정 및 사용자 지정 DNS 설정을 검토 합니다.

  ![끝점 DNS 설정](./media/container-registry-private-link/private-endpoint-overview.png)

이제 개인 링크가 구성 되어 사용할 준비가 되었습니다.

## <a name="disable-public-access"></a>공용 액세스 사용 안 함

대부분의 시나리오에서는 공용 네트워크에서 레지스트리 액세스를 사용 하지 않도록 설정 합니다. 이 구성을 통해 가상 네트워크 외부의 클라이언트가 레지스트리 끝점에 도달 하지 않도록 방지 합니다. 포털을 사용 하 여 공용 액세스를 사용 하지 않도록 설정 하려면:

1. 포털에서 컨테이너 레지스트리로 이동 하 고 **설정 > 네트워킹**을 선택 합니다.
1. 공용 **액세스** 탭의 **공용 액세스 허용**에서 **사용 안 함**을 선택 합니다. 그런 다음 **저장**을 선택합니다.

## <a name="validate-private-link-connection"></a>개인 링크 연결의 유효성 검사

개인 끝점의 서브넷에 있는 리소스가 개인 IP 주소를 통해 레지스트리에 연결 하 고 올바른 개인 DNS 영역 통합이 있는지 확인 해야 합니다.

개인 링크 연결의 유효성을 검사 하려면 가상 네트워크에서 설정한 가상 컴퓨터에 대해 SSH를 수행 합니다.

`nslookup` 명령을 실행 하 여 개인 링크를 통해 레지스트리의 IP 주소를 확인 합니다.

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

예제 출력에서는 서브넷의 주소 공간에 있는 레지스트리의 IP 주소를 보여 줍니다.

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

이 결과를 공용 끝점에서 동일한 레지스트리에 대 `nslookup` 한 출력의 공용 IP 주소와 비교 합니다.

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>개인 링크를 통한 레지스트리 작업

또한 서브넷의 가상 머신에서 레지스트리 작업을 수행할 수 있는지 확인 합니다. 가상 컴퓨터에 대 한 SSH 연결을 설정 하 고 [az acr login][az-acr-login] 을 실행 하 여 레지스트리에 로그인 합니다. VM 구성에 따라 다음 명령에를 접두사로 사용 `sudo`해야 할 수 있습니다.

```bash
az acr login --name $REGISTRY_NAME
```

와 `docker pull` 같은 레지스트리 작업을 수행 하 여 레지스트리에서 샘플 이미지를 끌어옵니다. 를 `hello-world:v1` 레지스트리에 적절 한 이미지 및 태그로 바꾸고 레지스트리 로그인 서버 이름 (모두 소문자)을 접두사로 사용 합니다.

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker가 성공적으로 이미지를 VM으로 끌어옵니다.

## <a name="manage-private-endpoint-connections"></a>개인 끝점 연결 관리

Azure Portal를 사용 하거나 [az acr private-endpoint-connection][az-acr-private-endpoint-connection] 명령 그룹의 명령을 사용 하 여 레지스트리의 개인 끝점 연결을 관리 합니다. 작업에는 레지스트리 개인 끝점 연결의 승인, 삭제, 나열, 거부 또는 세부 정보가 표시 됩니다.

예를 들어 레지스트리의 개인 끝점 연결을 나열 하려면 [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] 명령을 실행 합니다. 다음은 그 예입니다. 

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

이 문서의 단계를 사용 하 여 개인 끝점 연결을 설정 하면 레지스트리에 RBAC 권한이 있는 클라이언트 및 서비스의 연결이 자동으로 허용 됩니다. 연결의 수동 승인이 필요 하도록 끝점을 설정할 수 있습니다. 개인 끝점 연결을 승인 및 거부 하는 방법에 대 한 자세한 내용은 [개인 끝점 연결 관리](../private-link/manage-private-endpoint.md)를 참조 하세요.

## <a name="add-zone-records-for-replicas"></a>복제본에 대 한 영역 레코드 추가

이 문서에 나와 있는 것 처럼 레지스트리에 개인 끝점 연결을 추가 하면 레지스트리의 DNS 레코드 `privatelink.azurecr.io` 와 레지스트리가 [복제](container-registry-geo-replication.md)되는 지역에서 해당 데이터 끝점에 대해 만들어집니다. 

나중에 새 복제본을 추가 하는 경우 해당 지역에서 데이터 끝점에 대 한 새 영역 레코드를 수동으로 추가 해야 합니다. 예를 들어 *northeurope* 위치에 `myregistry.northeurope.data.azurecr.io` *myregistry* 의 복제본을 만드는 경우에 대 한 영역 레코드를 추가 합니다. 단계는이 문서의 [개인 영역에서 DNS 레코드 만들기](#create-dns-records-in-the-private-zone) 를 참조 하세요.

## <a name="clean-up-resources"></a>리소스 정리

동일한 리소스 그룹에서 모든 Azure 리소스를 만들고 더 이상 필요 하지 않은 경우에는 단일 [az group delete](/cli/azure/group) 명령을 사용 하 여 리소스를 선택적으로 삭제할 수 있습니다.

```azurecli
az group delete --name $RESOURCE_GROUP
```

포털에서 리소스를 정리 하려면 리소스 그룹으로 이동 합니다. 리소스 그룹이 로드 되 면 리소스 그룹 **삭제** 를 클릭 하 여 리소스 그룹 및 해당 그룹에 저장 된 리소스를 제거 합니다.

## <a name="next-steps"></a>다음 단계

* 개인 링크에 대 한 자세한 내용은 [Azure 개인 링크](../private-link/private-link-overview.md) 설명서를 참조 하세요.
* 클라이언트 방화벽 뒤에서 레지스트리 액세스 규칙을 설정 해야 하는 경우 [방화벽 뒤에 있는 Azure container registry에 액세스 하도록 규칙 구성](container-registry-firewall-access-rules.md)을 참조 하세요.

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
