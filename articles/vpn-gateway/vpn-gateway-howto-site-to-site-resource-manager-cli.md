---
title: "온-프레미스 네트워크를 Azure 가상 네트워크에 연결: 사이트 간 VPN: CLI | Microsoft Docs"
description: "공용 인터넷을 통해 온-프레미스 네트워크에서 Azure Virtual Network에 IPsec을 만드는 단계입니다. 이 단계는 CLI를 사용하여 크로스-프레미스 사이트 간 VPN Gateway 연결을 만드는 데 도움이 됩니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: af85e4921a2b81c71f1d132c6df591acbe5d3764
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>CLI를 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기

이 문서에서는 Azure CLI를 사용하여 온-프레미스 네트워크에서 VNet으로 사이트 간 VPN Gateway 연결을 만드는 방법을 보여줍니다. 이 문서의 단계는 Resource Manager 배포 모델에 적용됩니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.<br>

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [클래식 - 클래식 포털](vpn-gateway-site-to-site-create.md)
> 
>


![사이트 간 VPN 게이트웨이 크로스-프레미스 연결 다이어그램](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure 가상 네트워크에 연결하는 데 사용됩니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 장치를 필요로 합니다. VPN Gateway 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* Resource Manager 배포 모델을 사용할 것인지 확인합니다. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 호환되는 VPN 장치 및 구성할 수 있는 사람. 호환되는 VPN 장치 및 장치 구성에 대한 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* VPN 장치에 대한 외부 연결 공용 IPv4 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
* 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다. 이 구성을 만들 때 Azure가 온-프레미스 위치에 라우팅할 IP 주소 범위 접두사를 지정해야 합니다. 온-프레미스 네트워크의 어떤 서브넷도 사용자가 연결하려는 가상 네트워크 서브넷과 중첩될 수 없습니다. 
* 최신 버전의 CLI 명령(2.0 이상). CLI 명령 설치에 대한 자세한 내용은 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

### <a name="example-values"></a>예제 값

다음 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. 구독에 연결

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a>2. 리소스 그룹 만들기

다음 예제에서는 'eastus' 위치에 'TestRG1'이라는 리소스 그룹을 만듭니다. VNet을 만들려는 지역에 이미 리소스 그룹이 있는 경우 그 리소스 그룹을 대신 사용할 수 있습니다.

```azurecli
az group create --name TestRG1 --location eastus
```

## <a name="VNet"></a>3. 가상 네트워크 만들기

아직 가상 네트워크가 없으면 [az network vnet create](/cli/azure/network/vnet#create) 명령을 사용하여 만듭니다. 가상 네트워크를 만들 때 지정하는 주소 공간이 온-프레미스 네트워크에 있는 주소 공간과 겹치지 않는지 확인하세요. 

다음 예제에서는 'TestVNet1'이라는 가상 네트워크와 'Subnet1'이라는 서브넷을 만듭니다.

```azurecli
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.12.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>게이트웨이 서브넷 만들기

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

이 구성에는 게이트웨이 서브넷도 필요합니다. 가상 네트워크 게이트웨이는 VPN Gateway 서비스에서 사용하는 IP 주소가 포함된 게이트웨이 서브넷을 사용합니다. 게이트웨이 서브넷을 만들 때 이름을 'GatewaySubnet'으로 지정해야 합니다. 다른 이름을 지정하는 경우 서브넷을 만들지만 Azure에서는 게이트웨이 서브넷으로 취급하지 않습니다.

지정하는 게이트웨이 서브넷의 크기는 만들려는 VPN Gateway 구성에 따라 달라집니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 /27 또는 /28을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 더 큰 게이트웨이 서브넷을 사용하면 향후 구성을 수용할 수 있을 만큼 충분한 IP 주소를 확보할 수 있습니다.

[az network vnet subnet create](/cli/azure/network/vnet/subnet#create) 명령을 사용하여 게이트웨이 서브넷을 만듭니다.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. Azure가 참조할 수 있는 사이트 이름을 지정한 다음 연결을 만들 온-프레미스 VPN 장치의 IP 주소를 지정합니다. 또한 VPN Gateway를 통해 VPN 장치로 라우팅될 IP 주소 접두사를 지정합니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다. 온-프레미스 네트워크가 변경되면 이러한 접두사를 쉽게 업데이트할 수 있습니다.

다음 값을 사용합니다.

* *--gateway-ip-address*는 온-프레미스 VPN 장치의 IP 주소입니다. VPN 장치는 NAT 뒤에 배치할 수 없습니다.
* *--local-address-prefixes*는 온-프레미스 주소 공간입니다.

[az network local-gateway create](/cli/azure/network/local-gateway#create) 명령을 사용하여 주소 접두사가 여러 개인 로컬 네트워크 게이트웨이를 추가합니다.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. 공용 IP 주소 요청

VPN Gateway에 공용 IP 주소가 있어야 합니다. 먼저 IP 주소 리소스를 요청하고, 가상 네트워크 게이트웨이를 만들 때 참조합니다. VPN Gateway가 생성될 때 IP 주소는 리소스에 동적으로 할당됩니다. 현재 VPN Gateway는 *동적* 공용 IP 주소 할당만 지원합니다. 고정 공용 IP 주소 할당을 요청할 수 없습니다. 하지만 IP 주소가 VPN Gateway에 할당된 후 변경되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

[az network public-ip create](/cli/azure/network/public-ip#create) 명령을 사용하여 동적 공용 IP 주소를 요청합니다.

```azurecli
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. VPN Gateway 만들기

가상 네트워크 VPN Gateway 만들기. VPN Gateway 만들기를 완료하는 데 45분 이상 걸릴 수 있습니다.

다음 값을 사용합니다.

* 사이트 간 구성에 대한 *--gateway-type*은 *Vpn*입니다. 게이트웨이 유형은 항상 구현하는 구성에 따라 다릅니다. 자세한 내용은 [게이트웨이 유형](vpn-gateway-about-vpn-gateway-settings.md#gwtype)을 참조하세요.
* *--vpn-type*은 *경로 기반*(일부 설명서에서는 동적 게이트웨이라고도 함)이거나 *정책 기반*(일부 설명서에서는 고정 게이트웨이라고도 함)일 수 있습니다. 설정은 연결하는 장치의 요구 사항에 따라 좌우됩니다. VPN Gateway 유형에 대한 자세한 내용은 [VPN Gateway 구성 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#vpntype)를 참조하세요.
* *--sku*는 기본, 표준 또는 HighPerformance일 수 있습니다. 특정 SKU에 대한 구성 제한이 있습니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpngateways.md#gateway-skus)를 참조하세요.

[az network vnet-gateway create](/cli/azure/network/vnet-gateway#create) 명령을 사용하여 VPN Gateway를 만듭니다. '--no-wait' 매개 변수를 사용하여 이 명령을 실행하면 피드백 또는 출력이 보이지 않습니다. 이 매개 변수는 백그라운드에서 게이트웨이를 만드는 것을 허용합니다. 게이트웨이를 만들 때까지 약 45분 정도가 걸립니다.

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. VPN 장치 구성

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
 가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 [az network public-ip list](/cli/azure/network/public-ip#list) 명령을 사용합니다. 읽기 쉽도록 공용 IP 목록을 테이블 형식으로 표시하도록 출력 형식이 지정됩니다.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```

## <a name="CreateConnection"></a>9. VPN 연결 만들기

가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만듭니다. VPN 장치에 구성된 공유 키 값과 일치하도록 공유 키 값에 특히 주의해야 합니다.

[az network vpn-connection create](/cli/azure/network/vpn-connection#create) 명령을 사용하여 연결을 만듭니다.

```azurecli
az network vpn-connection create --name VNet1toSite2 -resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

잠시 후, 연결이 설정됩니다.

## <a name="toverify"></a>10. VPN 연결 확인

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

다른 방법을 사용하여 연결을 확인하려면 [VPN Gateway 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.

## <a name="common-tasks"></a>일반 작업

이 섹션에는 사이트 간 구성을 작업할 때 도움이 되는 일반적인 명령이 포함되어 있습니다. CLI 네트워킹 명령의 전체 목록은 [Azure CLI - 네트워킹](/cli/azure/network)을 참조하세요.

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)] 

## <a name="next-steps"></a>다음 단계

*  연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.
* BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.
* 강제 터널링에 대한 내용은 [강제 터널링 구성](vpn-gateway-forced-tunneling-rm.md)을 참조하세요.
* 네트워킹 Azure CLI 명령 목록은 [Azure CLI](https://docs.microsoft.com/cli/azure/network)를 참조하세요.
