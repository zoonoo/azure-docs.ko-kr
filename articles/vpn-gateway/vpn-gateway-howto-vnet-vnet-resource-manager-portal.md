---
title: "다른 VNet에 Azure 가상 네트워크 연결: 포털 | Microsoft Docs"
description: "리소스 관리자 및 Azure 포털을 사용하여 Vnet 간의 VPN 게이트웨이 연결을 만듭니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 102eab0e2e915521f8702b526dda886a2502f40b
ms.lasthandoff: 03/25/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Azure 포털에서 VNet-VNet 연결 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [클래식 - 클래식 포털](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

이 문서에서는 VPN 게이트웨이와 Azure 포털을 사용하여 Resource Manager 배포 모델의 VNet 간에 연결을 만드는 단계를 안내합니다.

Azure 포털을 사용하여 가상 네트워크에 연결할 때 Vnet이 동일한 구독에 있어야 합니다. 가상 네트워크가 서로 다른 구독에 있는 경우 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 단계를 사용하여 계속 연결할 수 있습니다.

![v2v 다이어그램](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 간 연결 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 현재 사용할 수 있는 배포 모델 및 VNet 간 구성을 위한 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 피어링**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>VNet 간 연결 정보
가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 Azure VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 연결한 VNet은 서로 다른 지역이나 서로 다른 구독에 있을 수 있습니다.

VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. 이렇게 하면 다음 다이어그램에 표시된 것처럼 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

![연결 정보](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "About connections")

### <a name="why-connect-virtual-networks"></a>가상 네트워크에 연결하는 이유
다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

* **지역 간 지리적 중복 및 지리적 상태**
  
  * 인터넷 연결 끝점으로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
  * Azure 트래픽 관리자 및 부하 분산 장치를 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.
* **분리 또는 관리 경계를 가진 지역별 다중 계층 응용 프로그램**
  
  * 같은 지역 내에서 분리 또는 관리 요구 사항 때문에 여러 가상 네트워크가 함께 연결된 다중 계층 응용 프로그램을 설정할 수 있습니다.

VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 고려 사항](#faq)을 참조하세요.

### <a name="values"></a>예제 설정
연습으로 이러한 단계를 사용하는 경우 샘플 구성 값을 사용할 수 있습니다. 예제에서는 각 VNet에 대한 여러 주소 공간을 사용합니다. 그러나 VNet 간 구성에는 여러 개의 주소 공간이 필요하지 않습니다.

**TestVNet1에 대한 값:**

* VNet 이름: TestVNet1
* 주소 공간: 10.11.0.0/16
  * 서브넷 이름: FrontEnd
  * 서브넷 주소 범위: 10.11.0.0/24
* 리소스 그룹: TestRG1
* 위치: 미국 동부
* 주소 공간: 10.12.0.0/16
  * 서브넷 이름: BackEnd
  * 서브넷 주소 범위: 10.12.0.0/24
* 게이트웨이 서브넷 이름: GatewaySubnet (포털에서 자동으로 채워짐)
  * 게이트웨이 서브넷 주소 범위: 10.11.255.0/27
* DNS 서버: DNS 서버의 IP 주소를 사용
* 가상 네트워크 게이트웨이 이름: TestVNet1GW
* 게이트웨이 유형: VPN
* VPN 유형: 경로 기반
* SKU: 사용할 게이트웨이 SKU 선택
* 공용 IP 주소 이름: TestVNet1GWIP
* 연결 값:
  * 이름: TestVNet1toTestVNet4
  * 공유 키: 공유 키를 직접 만들 수 있습니다. 이 예제에서는 abc123을 사용합니다. 중요한 사실은 Vnet 간의 연결을 만들 때 값이 일치해야 한다는 점입니다.

**TestVNet4에 대한 값:**

* VNet 이름: TestVNet4
* 주소 공간: 10.41.0.0/16
  * 서브넷 이름: FrontEnd
  * 서브넷 주소 범위: 10.41.0.0/24
* 리소스 그룹: TestRG1
* 위치: 미국 서부
* 주소 공간: 10.42.0.0/16
  * 서브넷 이름: BackEnd
  * 서브넷 주소 범위: 10.42.0.0/24
* 게이트웨이 서브넷 이름: GatewaySubnet (포털에서 자동으로 채워짐)
  * 게이트웨이 서브넷 주소 범위: 10.41.255.0/27
* DNS 서버: DNS 서버의 IP 주소를 사용
* 가상 네트워크 게이트웨이 이름: TestVNet4GW
* 게이트웨이 유형: VPN
* VPN 유형: 경로 기반
* SKU: 사용할 게이트웨이 SKU 선택
* 공용 IP 주소 이름: TestVNet4GWIP
* 연결 값:
  * 이름: TestVNet4toTestVNet1
  * 공유 키: 공유 키를 직접 만들 수 있습니다. 이 예제에서는 abc123을 사용합니다. 중요한 사실은 Vnet 간의 연결을 만들 때 값이 일치해야 한다는 점입니다.

## <a name="CreatVNet"></a>1. TestVNet1 만들기 및 구성
VNet이 이미 있는 경우 설정이 VPN 게이트웨이 설계와 호환되는지 확인합니다. 다른 네트워크와 겹칠 수 있는 서브넷에 특히 주의합니다. 겹치는 서브넷에 있으면 연결이 제대로 작동하지 않습니다. VNet이 올바른 설정으로 구성되었다면 [DNS 서버 지정](#dns) 섹션의 단계를 시작할 수 있습니다.

### <a name="to-create-a-virtual-network"></a>가상 네트워크를 만들려면
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. 다른 주소 공간 추가 및 서브넷 만들기
VNet이 만들어지면 여기에 다른 주소 공간을 추가하고 서브넷을 만들 수 있습니다.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. 게이트웨이 서브넷 만들기
가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 향후 추가적인 구성 요구 사항을 수용하기에 충분한 IP 주소를 제공하기 위하여 가능하면 /28 또는 /27 CIDR 블록을 사용하여 게이트웨이 서브넷을 만드는 것이 가장 좋습니다.

연습으로 이 구성을 만드는 경우 게이트웨이 서브넷을 만들 때 이러한 [예제 설정](#values)을 참조합니다.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>게이트웨이 서브넷을 만들려면
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. DNS 서버 지정(선택 사항)
DNS는 VNet 간 연결에 필요하지 않습니다. 하지만 가상 네트워크에 배포된 리소스에 대한 이름을 확인하려는 경우 DNS 서버를 지정해야 합니다. 이 설정을 통해 이 가상 네트워크에 대한 이름을 확인하는 데 사용하려는 DNS 서버를 지정할 수 있습니다. DNS 서버를 만들지 않습니다.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. 가상 네트워크 게이트웨이 만들기
이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. 이 단계를 완료하려면 최대 45분이 걸릴 수 있습니다. 연습으로 이 구성을 만드는 경우 [예제 설정](#values)을 참조하세요.

### <a name="to-create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이를 만들려면
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. TestVNet4 만들기 및 구성
TestVNet1를 구성한 후 TestVNet4 TestVNet4의 값을 대체하고 이전 단계를 반복하여 만듭니다. TestVNet1에 대한 가상 네트워크 게이트웨이 만들기 TestVNet4 구성하기 전에 끝날 때까지 기다릴 필요가 없습니다. 고유한 값을 사용하는 경우에 주소 공간에 연결하려는 Vnet를 사용하여 겹치지 않는지 확인합니다.

## <a name="TestVNet1Connection"></a>7. TestVNet1 연결 구성
가상 네트워크 게이트웨이 TestVNet1 및 TestVNet4를 모두 완료했을 때 게이트웨이 연결 가상 네트워크를 만들 수 있습니다. 이 섹션에서는 VNet1에서 VNet4에 연결을 만듭니다.

1. **모든 리소스**에서, VNet에 대한 가상 네트워크 게이트웨이로 이동합니다. 예를 들어 **TestVNet1GW**로 이동합니다. **TestVNet1GW**를 클릭하여 가상 네트워크 게이트웨이 블레이드를 엽니다.
   
    ![연결 블레이드](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Connections blade")
2. **+ 추가**를 클릭하여 **연결 추가** 블레이드를 엽니다.
3. **연결 추가** 블레이드에서 이름 필드에 연결에 대한 이름을 입력합니다. 예를 들어, **TestVNet1toTestVNet4**를 입력합니다.
   
    ![연결 이름](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Connection name")
4. **연결 형식**은 드롭다운에서 **VNet-VNet**을 선택합니다.
5. **첫 번째 가상 네트워크 게이트웨이** 필드 값은 지정된 가상 네트워크 게이트웨이에서 이 연결을 만들고 있으므로 자동으로 입력됩니다.
6. **두 번째 가상 네트워크 게이트웨이** 필드는 연결을 만들고자 하는 VNet의 가상 네트워크 게이트웨이입니다. **다른 가상 네트워크 게이트웨이 선택**을 클릭하여 **선택 가상 네트워크 게이트웨이** 블레이드를 엽니다.
   
    ![연결 추가](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Add a connection")
7. 이 블레이드에 나열된 가상 네트워크 게이트웨이를 봅니다. 구독에 있는 가상 네트워크 게이트웨이만 나열되어 있는지 확인합니다. 구독에 있지 않은 가상 네트워크 게이트웨이에 연결하려는 경우에 [PowerShell 문서](vpn-gateway-vnet-vnet-rm-ps.md)를 사용하세요. 
8. 연결하려는 가상 네트워크 게이트웨이를 클릭합니다.
9. **공유 키** 필드에서 연결에 대한 공유 키를 입력합니다. 이 키를 생성하거나 직접 만들 수 있습니다. 사이트 간 연결에서 사용되는 키는 온-프레미스 장치 및 가상 네트워크 게이트웨이 연결에서 사용하는 키와 정확히 일치합니다. 개념은 비슷하지만 여기에서는 VPN 장치에 연결하지 않고, 다른 가상 네트워크 게이트웨이를 연결합니다.
   
    ![공유 키](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Shared key")
10. 블레이드의 맨 아래에서 **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="TestVNet4Connection"></a>8. TestVNet4 연결 구성
다음으로 TestVNet4에서 TestVNet1에 연결을 만듭니다. TestVNet1에서 TestVNet4에 연결을 만드는 데 사용한 것과 동일한 메서드를 사용 합니다. 동일한 공유 키를 사용하고 있는지 확인합니다.

## <a name="VerifyConnection"></a>9. 연결 확인
연결을 확인합니다. 각 가상 네트워크 게이트웨이에 대해 다음을 수행합니다.

1. 가상 네트워크 게이트웨이에 대한 블레이드를 찾습니다. 예를 들어 **TestVNet4GW**를 찾습니다. 
2. 가상 네트워크 게이트웨이 블레이드에서 **연결**을 클릭하여 가상 네트워크 게이트웨이에 대한 연결 블레이드를 봅니다.

연결을 보고 상태를 확인합니다. 연결이 설정되면 상태 값으로 **Succeeded** 및 **Connected**가 표시됩니다.

![성공](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Succeeded")

연결에 대한 자세한 내용을 보려면 개별적으로 각 연결을 두 번 클릭하면 됩니다.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>VNet 간 고려 사항
VNet 간 연결에 대한 자세한 내용은 FAQ 세부 정보를 봅니다.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [가상 컴퓨터](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) 설명서를 참조하세요.

