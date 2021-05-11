---
title: 고정 아웃바운드 IP 구성
description: 방화벽의 공용 IP 주소를 수신 및 송신에 사용하는 Azure Container Instances 워크로드에 대한 Azure Firewall 및 사용자 정의 경로 구성
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89566571"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>컨테이너 그룹의 아웃바운드 및 인바운드 트래픽에 대한 단일 공용 IP 주소 구성

외부 연결 IP 주소를 사용하여 [컨테이너 그룹](container-instances-container-groups.md)을 설정하면 외부 클라이언트에서 IP 주소를 사용하여 그룹의 컨테이너에 액세스할 수 있습니다. 예를 들어 브라우저가 컨테이너에서 실행되는 웹앱에 액세스할 수 있습니다. 그러나 현재 컨테이너 그룹은 아웃바운드 트래픽에 다른 IP 주소를 사용합니다. 이 송신 IP 주소는 프로그래밍 방식으로 노출되지 않으므로 컨테이너 그룹 모니터링과 클라이언트 방화벽 규칙 구성이 더 복잡해집니다.

이 문서에서는 [Azure Firewall](../firewall/overview.md)과 통합된 [가상 네트워크](container-instances-virtual-network-concepts.md)에서 컨테이너 그룹을 구성하는 단계를 제공합니다. 컨테이너 그룹에 대한 사용자 정의 경로 및 방화벽 규칙을 설정하여 컨테이너 그룹으로 들어오고 나가는 트래픽을 라우팅하고 식별할 수 있습니다. 컨테이너 그룹 수신 및 송신은 방화벽의 공용 IP 주소를 사용합니다. Azure Container Instances에 위임된 가상 네트워크 서브넷에 배포된 여러 컨테이너 그룹에서 단일 송신 IP 주소를 사용할 수 있습니다.

이 문서에서는 Azure CLI를 사용하여 다음 시나리오를 위한 리소스를 만듭니다.

* 위임된 [가상 네트워크](container-instances-vnet.md) 서브넷에 배포된 컨테이너 그룹 
* 고정 공용 IP 주소를 사용하는 네트워크에 배포된 Azure Firewall
* 컨테이너 그룹 서브넷의 사용자 정의 경로
* 방화벽 수신용 NAT 규칙 및 송신용 애플리케이션 규칙

그런 다음 방화벽을 통해 예제 컨테이너 그룹의 수신 및 송신의 유효성을 검사합니다.

## <a name="deploy-aci-in-a-virtual-network"></a>가상 네트워크에 ACI 배포

일반적으로 컨테이너 그룹을 배포할 Azure 가상 네트워크가 이미 있을 수 있습니다. 데모를 위해 다음 명령은 컨테이너 그룹을 만들 때 가상 네트워크 및 서브넷을 만듭니다. 서브넷이 Azure Container Instances에 위임됩니다. 

컨테이너 그룹은 `aci-helloworld` 이미지의 작은 웹앱을 실행합니다. 설명서의 다른 문서에 표시된 것처럼 이 이미지는 정적 HTML 페이지를 제공하는, Node.js로 작성된 작은 웹앱을 패키지합니다.

필요한 경우 먼저 [az group create][az-group-create] 명령을 사용하여 Azure 리소스 그룹을 만듭니다. 예를 들면 다음과 같습니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

다음 명령 예제를 간소화하려면 리소스 그룹 이름에 환경 변수를 사용합니다.

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

[az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 만듭니다.

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
> 서브넷에서 필요한 IP 주소 공간에 맞게 `--subnet address-prefix`의 값을 조정합니다. 지원되는 가장 작은 서브넷은 8개의 IP 주소를 제공하는 /29입니다. 일부 IP 주소는 Azure에서 사용하도록 예약되어 있습니다.

이후 단계에서 사용하기 위해 [az container show][az-container-show] 명령을 실행하여 컨테이너 그룹의 개인 IP 주소를 가져옵니다.

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>네트워크에 Azure Firewall 배포

다음 섹션에서는 Azure CLI를 사용하여 가상 네트워크에 Azure Firewall을 배포합니다. 배경 정보는 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](../firewall/deploy-cli.md)을 참조하세요.

먼저 [az network vnet subnet create][az-network-vnet-subnet-create]를 사용하여 AzureFirewallSubnet이라는 방화벽용 서브넷을 추가합니다. AzureFirewallSubnet은 이 서브넷의 ‘필수’ 이름입니다.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

다음 [Azure CLI 명령](../firewall/deploy-cli.md)을 사용하여 서브넷에서 방화벽을 만듭니다.

아직 설치하지 않은 경우 [az extension add][az-extension-add] 명령을 사용하여 Azure CLI에 방화벽 확장을 추가합니다.

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

[az network firewall update][az-network-firewall-update] 명령을 사용하여 방화벽 구성을 업데이트합니다.

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

[az network firewall ip-config list][az-network-firewall-ip-config-list] 명령을 사용하여 방화벽의 개인 IP 주소를 가져옵니다. 이 개인 IP 주소는 이후 명령에서 사용됩니다.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
[az network public-ip show][az-network-public-ip-show] 명령을 사용하여 방화벽의 공용 IP 주소를 가져옵니다. 이 공용 IP 주소는 이후 명령에서 사용됩니다.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>ACI 서브넷에서 사용자 정의 경로 정의

트래픽을 Azure Firewall로 보내기 위해 ACI 서브넷에서 사용 정의 경로를 정의합니다. 자세한 내용은 [네트워크 트래픽 라우팅](../virtual-network/tutorial-create-route-table-cli.md)을 참조하세요. 

### <a name="create-route-table"></a>경로 테이블 만들기

먼저 [az network route-table create][az-network-route-table-create] 명령을 실행하여 경로 테이블을 만듭니다. 가상 네트워크와 동일한 지역에 경로 테이블을 만듭니다.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>경로 만들기

[az network-route-table route create][az-network-route-table-route-create]를 실행하여 경로 테이블에 경로를 만듭니다. 트래픽을 방화벽으로 라우팅하려면 다음 홉 유형을 `VirtualAppliance`로 설정하고 방화벽의 개인 IP 주소를 다음 홉 주소로 전달합니다.

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

[az network vnet subnet update][az-network-vnet-subnet-update] 명령을 실행하여 Azure Container Instances에 위임된 서브넷과 경로 테이블을 연결합니다.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>방화벽에서 규칙 구성

기본적으로 Azure Firewall은 인바운드 및 아웃바운드 트래픽을 거부(차단)합니다. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>ACI 서브넷에 대한 방화벽에서 NAT 규칙 구성

네트워크에서 이전에 시작한 애플리케이션 컨테이너로 들어오는 인바운드 인터넷 트래픽을 변환하고 필터링하기 위해 방화벽에서 [NAT 규칙](../firewall/rule-processing.md)을 만듭니다. 자세한 내용은 [Azure Firewall DNAT를 사용하여 인바운드 인터넷 트래픽 필터링](../firewall/tutorial-firewall-dnat.md)을 참조하세요.

[az network firewall nat-rule create][az-network-firewall-nat-rule-create] 명령을 사용하여 NAT 규칙 및 컬렉션을 만듭니다.

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

서브넷의 다른 IP 주소로 이동하는 트래픽을 필터링하기 위해 필요에 따라 NAT 규칙을 추가합니다. 예를 들어 서브넷의 다른 컨테이너 그룹이 인바운드 트래픽용 IP 주소를 노출하거나 다시 시작한 후 컨테이너 그룹에 다른 내부 IP 주소가 할당될 수 있습니다.

### <a name="create-outbound-application-rule-on-the-firewall"></a>방화벽에서 아웃바운드 애플리케이션 규칙 만들기

[az network firewall application-rule create][az-network-firewall-application-rule-create] 명령을 실행하여 방화벽에서 아웃바운드 규칙을 만듭니다. 이 샘플 규칙은 Azure Container Instances에 위임된 서브넷에서 FQDN `checkip.dyndns.org`에 액세스할 수 있도록 허용합니다. 사이트에 대한 HTTP 액세스는 이후 단계에서 Azure Container Instances의 송신 IP 주소를 확인하는 데 사용됩니다.

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

다음 섹션에서는 Azure Container Instances에 위임된 서브넷이 Azure Firewall 뒤에 올바르게 구성되었는지 확인합니다. 이전 단계에서는 서브넷으로 들어오는 트래픽과 서브넷에서 나가는 트래픽 모두 방화벽을 통해 라우팅했습니다.

### <a name="test-ingress-to-a-container-group"></a>컨테이너 그룹으로의 수신 테스트

방화벽의 공용 IP 주소로 이동하여 가상 네트워크에서 실행되는 *appcontainer* 에 대한 인바운드 액세스를 테스트합니다. 이전에는 공용 IP 주소를 $FW _PUBLIC_IP 변수에 저장했습니다.

```bash
echo $FW_PUBLIC_IP
```

출력은 다음과 비슷합니다.

```console
52.142.18.133
```

방화벽의 NAT 규칙이 올바르게 구성된 경우 브라우저에 방화벽의 공용 IP 주소를 입력하면 다음과 같이 표시됩니다.

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="방화벽의 공용 IP 주소로 이동":::

### <a name="test-egress-from-a-container-group"></a>컨테이너 그룹에서의 송신 테스트


가상 네트워크에 다음 샘플 컨테이너를 배포합니다. 컨테이너를 실행하면 단일 HTTP 요청을 `http://checkip.dyndns.org`에 보냅니다. 발신자의 IP 주소(송신 IP 주소)가 표시됩니다. 방화벽의 애플리케이션 규칙이 올바르게 구성된 경우 방화벽의 공용 IP 주소가 반환됩니다.

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

컨테이너 로그를 살펴보고 IP 주소가 방화벽의 공용 IP 주소와 같은지 확인합니다.

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

이 문서에서는 Azure Firewall 뒤에 있는 가상 네트워크에서 컨테이너 그룹을 설정했습니다. 방화벽에서 사용자 정의 경로와 NAT 및 애플리케이션 규칙을 구성했습니다. 이 구성을 사용하여 Azure Container Instances의 수신 및 송신에 대해 단일 고정 IP 주소를 설정했습니다.

트래픽을 관리하고 Azure 리소스를 보호하는 방법에 대한 자세한 내용은 [Azure Firewall](../firewall/index.yml) 설명서를 참조하세요.



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






