---
title: 비공개 링크 설정
description: 컨테이너 레지스트리에 개인 끝점을 설정하고 로컬 가상 네트워크에서 개인 링크를 사용하도록 설정합니다.
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498923"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Azure 컨테이너 레지스트리에 대한 Azure 개인 링크 구성 

Azure 가상 네트워크의 클라이언트가 [개인 링크를](../private-link/private-link-overview.md)통해 레지스트리에 안전하게 액세스할 수 있도록 Azure 컨테이너 레지스트리에 대한 [개인 끝점을](../private-link/private-endpoint-overview.md) 설정할 수 있습니다. 개인 끝점은 레지스트리의 가상 네트워크 주소 공간의 IP 주소를 사용합니다. 가상 네트워크와 레지스트리의 클라이언트 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 개인 링크를 통과하므로 공용 인터넷에서 노출되지 않습니다.

설정이 레지스트리의 할당된 개인 IP 주소로 확인되도록 개인 끝점에 대한 DNS 설정을 [구성할](../private-link/private-endpoint-overview.md#dns-configuration) 수 있습니다. DNS 구성을 사용하면 네트워크의 클라이언트와 서비스가 *myregistry.azurecr.io.*

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure 컨테이너 레지스트리 SCO를](container-registry-skus.md)참조하십시오.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기 중이며 일부 [제한 사항이](#preview-limitations) 적용됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 현재 [지리적 복제 레지스트리에서](container-registry-geo-replication.md)개인 끝점이 있는 개인 링크를 설정할 수 없습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서에서 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.2.0 이상을 사용하는 것이 좋습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하십시오. 또는 [Azure 클라우드 셸에서](../cloud-shell/quickstart.md)실행합니다.
* 컨테이너 레지스트리가 아직 없는 경우 하나(프리미엄 계층 필수)를 만들고 Docker Hub와 같은 `hello-world` 샘플 이미지를 푸시합니다. 예를 들어 [Azure 포털][quickstart-portal] 또는 [Azure CLI를][quickstart-cli] 사용하여 레지스트리를 만듭니다.
* 다른 Azure 구독의 개인 링크를 사용하여 레지스트리 액세스를 구성하려면 해당 구독에서 Azure 컨테이너 레지스트리에 대한 리소스 공급자를 등록해야 합니다. 예를 들어:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

이 문서의 Azure CLI 예제에서는 다음 환경 변수를 사용합니다. 사용자 환경에 적합한 값을 대체합니다. Bash 셸에 대한 모든 예제의 서식이 지정됩니다.

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 지원 가상 컴퓨터 만들기

테스트 목적을 위해 Docker 지원 우분투 VM을 사용하여 Azure 컨테이너 레지스트리에 액세스합니다. 레지스트리에 Azure Active Directory 인증을 사용하려면 VM에 [Azure CLI도][azure-cli] 설치합니다. Azure 가상 시스템이 이미 있는 경우 이 생성 단계를 건너뜁니다.

가상 컴퓨터 및 컨테이너 레지스트리에 동일한 리소스 그룹을 사용할 수 있습니다. 이 설정은 마지막에 정리를 단순화하지만 필요하지 않습니다. 가상 컴퓨터 및 가상 네트워크에 대해 별도의 리소스 그룹을 만들도록 선택한 경우 [az 그룹 만들기를 실행합니다.][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

이제 [az vm create를][az-vm-create]사용하여 기본 우분투 Azure 가상 컴퓨터를 배포합니다. 다음 예제는 *myDockerVM이라는 VM을*만듭니다.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분 정도 걸립니다. 명령이 완료되면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소를 사용하여 VM에 SSH 연결을 만듭니다.

### <a name="install-docker-on-the-vm"></a>VM에 Docker 설치

VM이 실행된 후 VM에 SSH 연결을 만듭니다. *publicIpAddress*를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@publicIpAddress
```

우분투 VM에 Docker를 설치 하려면 다음 명령을 실행 합니다.

```bash
sudo apt-get update
sudo apt install docker.io -y
```

설치 후 다음 명령을 실행하여 VM에서 Docker가 제대로 실행되는지 확인합니다.

```bash
sudo docker run -it hello-world
```

출력:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI 설치

[apt를 사용하여 Azure CLI 설치](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)의 단계를 따라 Ubuntu 가상 머신에 Azure CLI를 설치합니다. 예를 들어:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

SSH 연결을 종료합니다.

## <a name="set-up-private-link---cli"></a>개인 링크 설정 - CLI

### <a name="get-network-and-subnet-names"></a>네트워크 및 서브넷 이름 받기

아직 없는 경우 개인 링크를 설정하려면 가상 네트워크 및 서브넷의 이름이 필요합니다. 이 예제에서는 VM 및 레지스트리의 개인 끝점에 대해 동일한 서브넷을 사용합니다. 그러나 많은 시나리오에서 별도의 서브넷에서 끝점을 설정합니다. 

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 시스템의 이름을 기반으로 합니다. 예를 들어 가상 *컴퓨터myDockerVM의*이름을 지정하면 기본 가상 네트워크 이름은 *myDockerVMSubnet이라는*서브넷이 있는 *myDockerVMVNET입니다.* [az 네트워크 vnet 목록][az-network-vnet-list] 명령을 실행하여 환경 변수에서 이러한 값을 설정합니다.

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>서브넷에서 네트워크 정책 비활성화

개인 끝점에 대한 서브넷의 네트워크 보안 그룹과 같은 네트워크 [정책을 사용하지 않도록 설정합니다.](../private-link/disable-private-endpoint-network-policy.md) [az 네트워크 vnet 서브넷 업데이트로 서브넷][az-network-vnet-subnet-update]구성 업데이트:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>개인 DNS 영역 구성

Azure 컨테이너 레지스트리 도메인에 대한 개인 DNS 영역을 만듭니다. 이후 단계에서는 이 DNS 영역 내에서 레지스트리 도메인에 대한 DNS 레코드를 만듭니다.

개인 영역을 사용하여 Azure 컨테이너 레지스트리의 기본 DNS 해상도를 재정의하려면 영역의 이름을 **privatelink.azurecr.io.** 다음 [az 네트워크 개인 dns 영역 만들기][az-network-private-dns-zone-create] 명령을 실행하여 개인 영역을 만듭니다.

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>연결 링크 만들기

[az 네트워크 개인 DNS 링크 vnet 만들기를][az-network-private-dns-link-vnet-create] 실행하여 개인 영역을 가상 네트워크와 연결합니다. 이 예제는 *myDNSLink*라는 링크를 만듭니다.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>개인 레지스트리 끝점 만들기

이 섹션에서는 가상 네트워크에서 레지스트리의 개인 끝점을 만듭니다. 먼저 레지스트리의 리소스 ID를 가져옵니다.

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

az [네트워크 개인 끝점 만들기][az-network-private-endpoint-create] 명령을 실행하여 레지스트리의 개인 끝점을 만듭니다.

다음 예제는 끝점 *myPrivateEndpoint* 및 서비스 연결 *myConnection를*만듭니다. 끝점에 대한 컨테이너 레지스트리 리소스를 지정하려면 다음을 전달합니다. `--group-ids registry`

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>개인 IP 주소 받기

[az 네트워크 개인 끝점 표시를][az-network-private-endpoint-show] 실행하여 네트워크 인터페이스 ID의 끝점을 쿼리합니다.

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

네트워크 인터페이스와 연결된 두 개의 컨테이너 레지스트리에 대한 개인 IP 주소는 레지스트리 자체에 대한 주소와 레지스트리의 데이터 끝점에 대한 주소입니다. 다음 [az 리소스 표시][az-resource-show] 명령을 실행하여 컨테이너 레지스트리 및 레지스트리의 데이터 끝점에 대한 개인 IP 주소를 가져옵니다.

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>개인 영역에서 DNS 레코드 만들기

다음 명령은 레지스트리 끝점 및 해당 데이터 끝점에 대한 개인 영역에 DNS 레코드를 만듭니다. 예를 *들어, 서유럽* 지역에 *myregistry라는* 레지스트리가 있는 경우 끝점 `myregistry.azurecr.io` `myregistry.westeurope.data.azurecr.io`이름은 및 . 

첫 번째 실행 [az 네트워크 개인 dns 레코드 레코드 를 생성][az-network-private-dns-record-set-a-create] 하여 레지스트리 끝점 및 데이터 끝점에 대한 빈 A 레코드 집합을 만듭니다.

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

az [네트워크 개인 dns 레코드 를 실행-추가 레코드][az-network-private-dns-record-set-a-add-record] 명령을 설정 하여 레지스트리 끝점 및 데이터 끝점에 대 한 A 레코드를 만듭니다.

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

이제 개인 링크가 구성되고 사용할 준비가 되었습니다.

## <a name="set-up-private-link---portal"></a>개인 링크 설정 - 포털

다음 단계에서는 테스트를 위해 VM을 통해 가상 네트워크 및 서브넷을 이미 설정했다고 가정합니다. [새 가상 네트워크 및 서브넷을 만들](../virtual-network/quick-create-portal.md)수도 있습니다.

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정에서** **개인 끝점 연결(미리 보기)을**선택합니다.
1. **+ 개인 끝점을**선택합니다.
1. **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | 기존 그룹의 이름을 입력하거나 새 그룹을 만듭니다.|
    | **인스턴스 세부 정보** |  |
    | 이름 | 고유한 이름을 입력합니다. |
    |지역|지역을 선택합니다.|
    |||
5. **다음 을 선택합니다.**
6. 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | **내 디렉터리에서 Azure 리소스에 연결 선택**합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.Container레지스트리/레지스트리를 선택합니다.** |
    | 리소스 |레지스트리 이름 선택|
    |대상 하위 리소스 |**레지스트리** 선택|
    |||
7. **다음 을 선택합니다: 구성.**
8. 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| *myDockerVMVNET과*같이 가상 시스템이 배포되는 가상 네트워크를 선택합니다. |
    | 서브넷 | 가상 시스템이 배포되는 *myDockerVMSubnet과* 같은 서브넷을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |*선택(새로 privatelink.azurecr.io)* |
    |||

1. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
2. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.**

개인 끝점을 만든 후 개인 영역의 DNS 설정이 끝점의 **개요** 페이지에 나타납니다.

![엔드포인트 DNS 설정](./media/container-registry-private-link/private-endpoint-overview.png)

이제 개인 링크가 구성되고 사용할 준비가 되었습니다.

## <a name="validate-private-link-connection"></a>개인 링크 연결 유효성 검사

개인 끝점의 서브넷 내의 리소스가 개인 IP 주소를 통해 레지스트리에 연결되는지 확인하고 올바른 개인 DNS 영역 통합이 있는지 확인해야 합니다.

개인 링크 연결의 유효성을 검사하기 위해 가상 네트워크에서 설정한 가상 시스템에 대한 SSH입니다.

`nslookup` 명령을 실행하여 개인 링크를 통해 레지스트리의 IP 주소를 확인합니다.

```bash
nslookup $registryName.azurecr.io
```

예제 출력은 서브넷의 주소 공간에 레지스트리의 IP 주소를 보여 주며, 다음을 보여 주십니다.

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

이 결과를 공용 끝점에서 `nslookup` 동일한 레지스트리에 대한 출력의 공용 IP 주소와 비교합니다.

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>개인 링크를 통해 레지스트리 작업

또한 서브넷의 가상 컴퓨터에서 레지스트리 작업을 수행할 수 있는지 확인합니다. 가상 컴퓨터에 SSH 연결을 만들고 [az acr 로그인을][az-acr-login] 실행하여 레지스트리에 로그인합니다. VM 구성에 따라 `sudo`다음 명령을 로 접두사를 해야 할 수 있습니다.

```bash
az acr login --name $registryName
```

레지스트리에서 샘플 이미지를 `docker pull` 가져오는 등의 레지스트리 작업을 수행합니다. 레지스트리 `hello-world:v1` 로그인 서버 이름(모든 소문자)이 붙은 레지스트리에 적합한 이미지 및 태그로 바꿉니다.

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker가 이미지를 VM으로 성공적으로 가져옵니다.

## <a name="manage-private-endpoint-connections"></a>개인 엔드포인트 연결 관리

Azure 포털을 사용하거나 [az acr 개인][az-acr-private-endpoint-connection] 끝점 연결 명령 그룹의 명령을 사용하여 레지스트리의 개인 끝점 연결을 관리합니다. 작업에는 레지스트리의 개인 끝점 연결에 대한 승인, 삭제, 목록, 거부 또는 표시가 포함됩니다.

예를 들어 레지스트리의 개인 끝점 연결을 나열하려면 [az acr 개인 끝점 연결 목록][az-acr-private-endpoint-connection-list] 명령을 실행합니다. 예를 들어:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

이 문서의 단계를 사용하여 개인 끝점 연결을 설정하면 레지스트리는 레지스트리에 RBAC 권한이 있는 클라이언트 및 서비스의 연결을 자동으로 수락합니다. 연결의 수동 승인이 필요하도록 끝점을 설정할 수 있습니다. 개인 끝점 연결을 승인하고 거부하는 방법에 대한 자세한 내용은 [개인 끝점 연결 관리를](../private-link/manage-private-endpoint.md)참조하십시오.

## <a name="clean-up-resources"></a>리소스 정리

동일한 리소스 그룹에 있는 모든 Azure 리소스를 만들었는데 더 이상 필요하지 않은 경우 단일 [az 그룹 삭제](/cli/azure/group) 명령을 사용하여 선택적으로 리소스를 삭제할 수 있습니다.

```azurecli
az group delete --name $resourceGroup
```

포털에서 리소스를 정리하려면 리소스 그룹으로 이동합니다. 리소스 그룹이 로드되면 리소스 **그룹 삭제를** 클릭하여 리소스 그룹과 리소스 그룹에 저장된 리소스를 제거합니다.

## <a name="next-steps"></a>다음 단계

* 개인 링크에 대한 자세한 내용은 [Azure 개인 링크](../private-link/private-link-overview.md) 설명서를 참조하십시오.
* 개인 링크의 대안은 레지스트리 액세스를 제한하기 위해 네트워크 액세스 규칙을 설정하는 것입니다. 자세한 내용은 [Azure 가상 네트워크 또는 방화벽 규칙을 사용하여 Azure 컨테이너 레지스트리에 대한 액세스 제한을](container-registry-vnet.md)참조하십시오.

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
