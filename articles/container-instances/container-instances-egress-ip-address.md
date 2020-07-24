---
title: 고정 아웃 바운드 IP 구성
description: 수신 및 송신에 방화벽의 공용 IP 주소를 사용 하는 Azure Container Instances 작업에 대 한 Azure 방화벽 및 사용자 정의 경로 구성
ms.topic: article
ms.date: 07/16/2020
author: dlepow
ms.author: danlep
ms.openlocfilehash: d748e3e6239ba913afc5b8aadd7e85dcd1027c04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023707"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>컨테이너 그룹에 대 한 아웃 바운드 및 인바운드 트래픽에 대 한 단일 공용 IP 주소 구성

외부 연결 IP 주소를 사용 하 여 [컨테이너 그룹](container-instances-container-groups.md) 을 설정 하면 외부 클라이언트에서 IP 주소를 사용 하 여 그룹의 컨테이너에 액세스할 수 있습니다. 예를 들어 브라우저는 컨테이너에서 실행 되는 웹 앱에 액세스할 수 있습니다. 그러나 현재 컨테이너 그룹은 아웃 바운드 트래픽에 대해 다른 IP 주소를 사용 합니다. 이 송신 IP 주소는 프로그래밍 방식으로 노출 되지 않으며,이를 통해 컨테이너 그룹 모니터링과 클라이언트 방화벽 규칙의 구성이 더 복잡해 집니다.

이 문서에서는 [Azure 방화벽과](../firewall/overview.md)통합 된 [가상 네트워크](container-instances-virtual-network-concepts.md) 에서 컨테이너 그룹을 구성 하는 단계를 제공 합니다. 컨테이너 그룹 및 방화벽 규칙에 대 한 사용자 정의 경로를 설정 하 여 컨테이너 그룹 간에 트래픽을 라우팅하고 식별할 수 있습니다. 컨테이너 그룹 수신 및 송신은 방화벽의 공용 IP 주소를 사용 합니다. 단일 송신 IP 주소는 Azure Container Instances에 위임 된 가상 네트워크의 서브넷에 배포 된 여러 컨테이너 그룹에서 사용할 수 있습니다.

이 문서에서는 Azure CLI를 사용 하 여이 시나리오에 대 한 리소스를 만듭니다.

* [가상 네트워크의](container-instances-vnet.md) 위임 된 서브넷에 배포 된 컨테이너 그룹 
* 네트워크에 고정 공용 IP 주소를 사용 하 여 배포 된 Azure 방화벽
* 컨테이너 그룹의 서브넷에 대 한 사용자 정의 경로
* 방화벽 수신 및 송신에 대 한 응용 프로그램 규칙에 대 한 NAT 규칙

그런 다음 방화벽을 통해 예제 컨테이너 그룹의 수신 및 송신의 유효성을 검사 합니다.

## <a name="deploy-aci-in-a-virtual-network"></a>가상 네트워크에 ACI 배포

일반적으로 컨테이너 그룹을 배포할 Azure 가상 네트워크가 이미 있을 수 있습니다. 데모용으로 다음 명령은 컨테이너 그룹을 만들 때 가상 네트워크 및 서브넷을 만듭니다. 서브넷이 Azure Container Instances으로 위임 됩니다. 

컨테이너 그룹은 이미지에서 작은 웹 앱을 실행 합니다 `aci-helloworld` . 설명서의 다른 문서에 나와 있는 것 처럼이 이미지는 정적 HTML 페이지를 제공 하는 Node.js으로 작성 된 작은 웹 앱을 패키지 합니다.

하나 필요 하면 먼저 [az group create][az-group-create] 명령을 사용 하 여 Azure 리소스 그룹을 만듭니다. 예를 들어:

```azurecli
az group create --name myResourceGroup --location eastus
```

다음 명령 예제를 간소화 하려면 리소스 그룹 이름에 대해 환경 변수를 사용 합니다.

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

[Az container create][az-container-create] 명령을 사용 하 여 컨테이너 그룹을 만듭니다.

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> `--subnet address-prefix`서브넷에 필요한 IP 주소 공간에 대 한의 값을 조정 합니다. 지원 되는 가장 작은 서브넷은 8 개의 IP 주소를 제공 하는/29입니다. 일부 IP 주소는 Azure에서 사용 하도록 예약 되어 있습니다.

이후 단계에서 사용 하려면 [az container show] [az-container-show] 명령을 실행 하 여 컨테이너 그룹의 개인 IP 주소를 가져옵니다.

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>네트워크에 Azure 방화벽 배포

다음 섹션에서는 Azure CLI를 사용 하 여 가상 네트워크에 Azure 방화벽을 배포 합니다. 배경은 [자습서: Azure Portal 사용 하 여 Azure 방화벽 배포 및 구성](../firewall/deploy-cli.md)을 참조 하세요.

먼저 [az network vnet subnet create][az-network-vnet-subnet-create] 를 사용 하 여 방화벽에 대 한 AzureFirewallSubnet 이라는 서브넷을 추가 합니다. AzureFirewallSubnet는이 서브넷의 *필수* 이름입니다.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

다음 [Azure CLI 명령을](../firewall/deploy-cli.md) 사용 하 여 서브넷에서 방화벽을 만듭니다.

아직 설치 하지 않은 경우 [az extension add][az-extension-add] 명령을 사용 하 여 Azure CLI에 방화벽 확장을 추가 합니다.

```azurecli
az extension add --name azure-firewall
```

방화벽 리소스를 만듭니다.

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

[Az network firewall update][az-network-firewall-update] 명령을 사용 하 여 방화벽 구성을 업데이트 합니다.

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

[Az network firewall ip-config list][az-network-firewall-ip-config-list] 명령을 사용 하 여 방화벽의 개인 IP 주소를 가져옵니다. 이 개인 IP 주소는 이후 명령에 사용 됩니다.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
[Az network public ip show][az-network-public-ip-show] 명령을 사용 하 여 방화벽의 공용 ip 주소를 가져옵니다. 이 공용 IP 주소는 이후 명령에 사용 됩니다.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>ACI 서브넷의 사용자 정의 경로 정의

Azure 방화벽에 대 한 트래픽을 전환 하기 위해 ACI 서브넷에서 사용 정의 경로를 정의 합니다. 자세한 내용은 [네트워크 트래픽 라우팅](../virtual-network/tutorial-create-route-table-cli.md)을 참조 하세요. 

### <a name="create-route-table"></a>경로 테이블 만들기

먼저 다음 [az network route-table create][az-network-route-table-create] 명령을 실행 하 여 경로 테이블을 만듭니다. 가상 네트워크와 동일한 지역에 경로 테이블을 만듭니다.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>경로 만들기

[Az network route table route create][az-network-route-table-route-create] 를 실행 하 여 경로 테이블에 경로를 만듭니다. 트래픽을 방화벽으로 라우팅하려면 다음 홉 유형을로 설정 하 `VirtualAppliance` 고 방화벽의 개인 IP 주소를 다음 홉 주소로 전달 합니다.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>ACI 서브넷에 경로 테이블 연결

[Az network vnet subnet update][az-network-vnet-subnet-update] 명령을 실행 하 여 Azure Container Instances에 위임 된 서브넷에 경로 테이블을 연결 합니다.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>방화벽에 대 한 규칙 구성

기본적으로 Azure 방화벽은 인바운드 및 아웃 바운드 트래픽을 거부 합니다. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>방화벽에 대 한 NAT 규칙을 ACI 서브넷으로 구성

네트워크에서 이전에 시작한 응용 프로그램 컨테이너로 인바운드 인터넷 트래픽을 변환 하 고 필터링 하기 위해 방화벽에 [NAT 규칙](../firewall/rule-processing.md) 을 만듭니다. 자세한 내용은 [Azure 방화벽 dnat 사용 하 여 인바운드 인터넷 트래픽 필터링](../firewall/tutorial-firewall-dnat.md)

[Az network firewall nat-rule create][az-network-firewall-nat-rule-create] 명령을 사용 하 여 nat 규칙 및 컬렉션을 만듭니다.

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

서브넷의 다른 IP 주소에 대 한 트래픽을 필터링 하기 위해 필요에 따라 NAT 규칙을 추가 합니다. 예를 들어 서브넷의 다른 컨테이너 그룹은 인바운드 트래픽에 대 한 IP 주소를 노출 하거나 다시 시작한 후에 다른 내부 IP 주소를 컨테이너 그룹에 할당할 수 있습니다.

### <a name="create-outbound-application-rule-on-the-firewall"></a>방화벽에서 아웃 바운드 응용 프로그램 규칙 만들기

다음 [az network firewall application-rule create][az-network-firewall-application-rule-create] 명령을 실행 하 여 방화벽에서 아웃 바운드 규칙을 만듭니다. 이 샘플 규칙은 FQDN에 Azure Container Instances 위임 된 서브넷의 액세스를 허용 합니다 `checkip.dyndns.org` . 사이트에 대 한 HTTP 액세스는 Azure Container Instances에서 송신 IP 주소를 확인 하는 이후 단계에서 사용 됩니다.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>방화벽을 통해 컨테이너 그룹 액세스 테스트

다음 섹션에서는 Azure Container Instances에 위임 된 서브넷이 Azure 방화벽 뒤에서 올바르게 구성 되어 있는지 확인 합니다. 이전 단계는 서브넷으로 들어오는 트래픽과 방화벽을 통해 서브넷에서 나가는 트래픽을 모두 라우팅합니다.

### <a name="test-ingress-to-a-container-group"></a>컨테이너 그룹에 대 한 테스트 수신

방화벽의 공용 IP 주소를 탐색 하 여 가상 네트워크에서 실행 되는 *appcontainer* 에 대 한 인바운드 액세스를 테스트 합니다. 이전에는 $FW _PUBLIC_IP 변수에 공용 IP 주소를 저장 했습니다.

```bash
echo $FW_PUBLIC_IP
```

출력은 다음과 비슷합니다.

```console
52.142.18.133
```

방화벽의 NAT 규칙이 올바르게 구성 된 경우 브라우저에 방화벽의 공용 IP 주소를 입력 하면 다음과 같이 표시 됩니다.

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="방화벽의 공용 IP 주소로 이동 합니다.":::

### <a name="test-egress-from-a-container-group"></a>컨테이너 그룹에서 송신 테스트


가상 네트워크에 다음 샘플 컨테이너를 배포 합니다. 실행 되 면 단일 HTTP 요청을에 보냅니다. 그러면 `http://checkip.dyndns.org` 발신자의 ip 주소 (송신 ip 주소)가 표시 됩니다. 방화벽의 응용 프로그램 규칙이 올바르게 구성 된 경우 방화벽의 공용 IP 주소가 반환 됩니다.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

컨테이너 로그를 확인 하 여 IP 주소가 방화벽의 공용 IP 주소와 같은지 확인 합니다.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

출력은 다음과 비슷합니다.

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 방화벽 뒤에 있는 가상 네트워크에서 컨테이너 그룹을 설정 합니다. 사용자 정의 경로 및 NAT 및 방화벽에 대 한 응용 프로그램 규칙을 구성 했습니다. 이 구성을 사용 하 여 Azure Container Instances에서 수신 및 송신에 대 한 고정 IP 주소를 하나 설정 합니다.

트래픽을 관리 하 고 Azure 리소스를 보호 하는 방법에 대 한 자세한 내용은 [Azure 방화벽](../firewall/index.yml) 설명서를 참조 하세요.



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






