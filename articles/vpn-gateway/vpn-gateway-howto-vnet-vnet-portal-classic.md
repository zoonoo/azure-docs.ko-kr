---
title: 'Vnet: 클래식: Azure Portal 간의 연결을 만듭니다.'
description: PowerShell 및 Azure Portal을 사용하여 Azure 가상 네트워크를 함께 연결합니다.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103223"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet-VNet 연결(클래식) 구성

이 문서는 가상 네트워크 간에 VPN 게이트웨이 연결을 만드는 데 도움이 됩니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="클래식 VNet 간 아키텍처를 보여 주는 다이어그램":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

이 문서의 단계는 클래식 배포 모델 및 Azure Portal에 적용됩니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [클래식](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [다른 배포 모델의 Vnet 연결](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>VNet 간 연결 정보

VPN 게이트웨이를 사용하여 클래식 배포 모델에서 가상 네트워크를 다른 가상 네트워크(VNet-VNet)에 연결하는 것은 가상 네트워크를 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다.

연결하는 VNet은 서로 다른 구독 및 지역에 있을 수 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수 있습니다. 이렇게 하면 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="클래식 VNet 간 아키텍처를 보여 주는 다이어그램":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>가상 네트워크에 연결하는 이유

다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

* **지역 간 지리적 중복 및 지리적 상태**

  * 인터넷 연결 엔드포인트로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
  * Azure 부하 분산 장치 및 Microsoft 또는 타사 클러스터링 기술을 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.
* **분리 경계가 뚜렷한 지역별 다중 계층 애플리케이션**

  * 같은 지역 내에서 뚜렷한 경계를 적용해 여러 VNet이 함께 연결된 다중 계층 애플리케이션을 설정하고 계층 간 통신을 보호할 수 있습니다.
* **Azure의 구독 간/조직 간 통신**

  * Azure 구독이 여러 개인 경우 이제 가상 네트워크 간에 여러 구독의 작업을 안전하게 연결할 수 있습니다.
  * 엔터프라이즈 또는 서비스 공급자의 경우 이제 Azure 내의 보안 VPN 기술을 사용하여 조직 간 통신을 사용하도록 설정할 수 있습니다.

VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 고려 사항](#faq)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

대부분의 단계에서 포털을 사용하지만 PowerShell을 사용하여 VNet 간의 연결을 만들어야 합니다. 포털에서 공유 키를 지정할 수 있는 방법이 없기 때문에 Azure Portal를 사용 하 여 연결을 만들 수 없습니다. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>계획

가상 네트워크를 구성하는 데 사용할 범위를 결정하는 것이 중요합니다. 이 구성의 경우 VNet 범위가 서로 간에 또는 연결된 로컬 네트워크와 겹치지 않는지 확인해야 합니다.

### <a name="vnets"></a><a name="vnet"></a>Vnet

이 연습에서는 다음 예제 값을 사용 합니다.

**TestVNet1에 대한 값**

이름: TestVNet1<br>
주소 공간: 10.11.0.0/16, 10.12.0.0/16(선택 사항)<br>
서브넷 이름: 기본값<br>
서브넷 주소 범위: 10.11.0.0/24<br>
리소스 그룹: ClassicRG<br>
위치: 미국 동부<br>
게이트웨이 서브넷: 10.11.1.0/27

**TestVNet4에 대한 값**

이름: TestVNet4<br>
주소 공간: 10.41.0.0/16, 10.42.0.0/16(선택 사항)<br>
서브넷 이름: 기본값<br>
서브넷 주소 범위: 10.41.0.0/24<br>
리소스 그룹: ClassicRG<br>
위치: 미국 서부<br>
게이트웨이 서브넷: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Connections

다음 표에서는 Vnet를 연결 하는 방법의 예를 보여 줍니다. 범위는 지침으로만 참고하고, 실제 가상 네트워크에 사용할 범위를 기록합니다. 이 정보는 이후 단계에서 필요합니다.

이 예제에서 TestVNet1는 ' VNet4Local ' 이라는 사용자가 만든 로컬 네트워크 사이트에 연결 됩니다. VNet4Local에 대한 설정에는 TestVNet4의 주소 접두사가 포함됩니다.
각 VNet의 로컬 사이트는 다른 VNet입니다. 다음 예제 값은 여기의 구성에 사용됩니다.

**예제**

| Virtual Network | 주소 공간 | 위치 | 로컬 네트워크 사이트에 연결 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |미국 동부 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |미국 서부 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>가상 네트워크 만들기

이 단계에서는 TestVNet1 및 TestVNet4의 두 가지 클래식 가상 네트워크를 만듭니다. 이 문서를 연습으로 사용 하는 경우 [예제 값](#vnet)을 사용 합니다.

**VNet을 만드는 경우 다음 설정에 유념하세요.**

* **Virtual Network 주소 공간** – Virtual Network 주소 공간 페이지에서 가상 네트워크에 사용할 주소 범위를 지정합니다. 가상 네트워크에 배포하는 VM 및 다른 역할 인스턴스에 할당될 동적 IP 주소입니다.<br>선택하는 주소 공간은 이 VNet이 연결될 다른 VNet 또는 온-프레미스 위치의 주소 공간과 겹칠 수 없습니다.

* **위치** – 가상 네트워크를 만들 때 Azure 위치(지역)와 연결합니다. 예를 들어 실제로 West US에 배치할 가상 네트워크에 VM을 배포하려는 경우 해당 위치를 선택합니다. 가상 네트워크를 만든 후에는 가상 네트워크에 연결된 위치를 변경할 수 없습니다.

**VNet을 만든 후에는 다음 설정을 추가할 수 있습니다.**

* **주소 공간** – 이 구성에는 주소 공간이 더 필요하지 않지만 VNet을 만든 후 주소 공간을 더 추가할 수 있습니다.

* **서브넷** – 이 구성에는 서브넷이 더 필요하지 않지만 다른 역할 인스턴스와 분리된 서브넷에 VM을 두려는 경우가 있을 수 있습니다.

* **DNS 서버** – DNS 서버 이름 및 IP 주소를 입력합니다. 이 설정은 DNS 서버를 만들지 않습니다. 이렇게 하면 이 가상 네트워크에 대한 이름 확인에 사용하려는 DNS 서버를 지정할 수 있습니다.

### <a name="to-create-a-classic-virtual-network"></a>클래식 가상 네트워크를 만들려면

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>사이트 및 게이트웨이 구성

Azure는 각 로컬 네트워크 사이트에 지정된 설정을 사용하여 VNet 간에 트래픽을 라우팅하는 방법을 결정합니다. 각 VNet은 트래픽을 라우팅하려는 각 로컬 네트워크를 가리켜야 합니다. 각 로컬 네트워크 사이트를 참조하는 데 사용할 이름을 확인합니다. 설명이 포함된 이름을 사용하는 것이 가장 좋습니다.

예를 들어 TestVNet1은 'VNet4Local'이라는 이름으로 만든 로컬 네트워크 사이트에 연결됩니다. VNet4Local에 대한 설정에는 TestVNet4의 주소 접두사가 포함됩니다.

각 VNet의 로컬 사이트는 다른 VNet을 염두에 두어야 합니다.

| Virtual Network | 주소 공간 | 위치 | 로컬 네트워크 사이트에 연결 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |미국 동부 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |미국 서부 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>사이트를 구성 하려면

로컬 사이트는 일반적으로 온-프레미스 위치를 가리킵니다. VPN 디바이스의 IP 주소를 포함하며 여기에서 VPN 디바이스에 대한 VPN Gateway를 통해 라우팅되는 연결 및 IP 주소 범위를 만듭니다.

1. VNet에 대 한 페이지의 **설정**아래에서 **사이트 간 연결**을 선택 합니다.
1. 사이트 간 연결 페이지에서 **+ 추가**를 선택 합니다.
1. **VPN 연결 및 게이트웨이 구성** 페이지에서 **연결 형식**으로 **사이트 간** 을 선택 된 상태로 둡니다.

   * **VPN Gateway IP 주소:** 온-프레미스 네트워크에 대한 VPN 디바이스의 공용 IP 주소입니다. 이 연습에서는 다른 사이트에 대 한 VPN gateway의 IP 주소가 아직 없으므로 더미 주소를 입력할 수 있습니다. 예를 들면 5.4.3.2입니다. 나중에 다른 VNet에 대 한 게이트웨이를 구성한 후이 값을 조정할 수 있습니다.

   * **클라이언트 주소 공간:** 이 게이트웨이를 통해 다른 VNet으로 라우팅할 IP 주소 범위를 나열 합니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 자체 가상 네트워크가 연결된 다른 네트워크의 범위 또는 가상 네트워크 자체의 주소 범위와 겹치지 않도록 합니다.
1. 페이지 맨 아래에서 검토 + 만들기를 선택 하지 않습니다. 대신 **다음: 게이트웨이>** 를 선택 합니다.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>가상 네트워크 게이트웨이를 구성 하려면

1. **게이트웨이** 페이지에서 다음 값을 선택 합니다.

   * **크기:** 가상 네트워크 게이트웨이를 만드는 데 사용 하는 게이트웨이 SKU입니다. 클래식 VPN 게이트웨이는 이전(레거시) 게이트웨이 SKU를 사용합니다. 레거시 게이트웨이 SKU에 대한 자세한 내용은 [가상 네트워크 게이트웨이 SKU(이전 SKU) 작업](vpn-gateway-about-skus-legacy.md)을 참조하세요. 이 연습에서 **Standard** 를 선택할 수 있습니다.

   * **라우팅 유형:** 게이트웨이의 라우팅 형식을 선택 합니다. VPN 유형이라고도 합니다. 게이트웨이를 한 형식에서 다른 형식으로 변환할 수 없기 때문에 올바른 유형을 선택 하는 것이 중요 합니다. VPN 디바이스는 선택한 라우팅 유형과 호환되어야 합니다. 라우팅 유형에 대 한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#vpntype)를 참조 하세요. '경로 기반' 및 '정책 기반' VPN 유형을 참조하는 문서를 볼 수 있습니다. '동적'은 '경로 기반'에 해당하고 '고정'은 '정책 기반'에 해당합니다. 이 구성의 경우 **동적**을 선택 합니다.

   * **게이트웨이 서브넷:** 지정 하는 게이트웨이 서브넷의 크기는 만들려는 VPN gateway 구성에 따라 달라 집니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 /27 또는 /28을 사용하는 것이 좋습니다. 이렇게 하면 더 많은 주소를 포함하는 큰 서브넷이 만들어집니다. 더 큰 게이트웨이 서브넷을 사용하면 향후 구성을 수용할 수 있을 만큼 충분한 IP 주소를 확보할 수 있습니다.

1. 페이지 맨 아래에서 **검토 + 만들기** 를 선택 하 여 설정의 유효성을 검사 합니다. **만들기** 를 선택 하 여 배포 합니다. 선택한 게이트웨이 SKU에 따라 가상 네트워크 게이트웨이를 만드는 데 최대 45 분이 걸릴 수 있습니다.
1. 이 게이트웨이가 생성 되는 동안 다음 단계로 진행을 시작할 수 있습니다.

### <a name="configure-testvnet4-settings"></a>TestVNet4 설정 구성

[사이트 및 게이트웨이 만들기](#localsite) 단계를 반복 하 여 TestVNet4를 구성 하 고 필요한 경우 값을 대체 합니다. 연습으로이 작업을 수행 하는 경우 [예제 값](#planning)을 사용 합니다.

## <a name="update-local-sites"></a><a name="updatelocal"></a>로컬 사이트 업데이트

두 Vnet에 대해 가상 네트워크 게이트웨이를 만든 후에는 **VPN GATEWAY IP 주소**에 대 한 로컬 사이트 속성을 조정 해야 합니다.

|VNet 이름|연결된 사이트|게이트웨이 IP 주소|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4의 VPN 게이트웨이 IP 주소|
|TestVNet4|VNet1Local|TestVNet1의 VPN 게이트웨이 IP 주소|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1부 - 가상 네트워크 게이트웨이 공용 IP 주소 가져오기

1. **리소스 그룹** 으로 이동 하 고 가상 네트워크를 선택 하 여 VNet으로 이동 합니다.
1. 가상 네트워크에 대 한 페이지의 오른쪽에 있는 **Essentials** 창에서 **게이트웨이 IP 주소** 를 찾아 클립보드로 복사 합니다.

### <a name="part-2---modify-the-local-site-properties"></a>2 부-로컬 사이트 속성 수정

1. 사이트 간 연결에서 연결을 선택 합니다. 예를 들면 SiteVNet4입니다.
1. 사이트 간 연결에 대 한 **속성** 페이지에서 **로컬 사이트 편집**을 선택 합니다.
1. **Vpn GATEWAY ip 주소** 필드에 이전 섹션에서 복사한 VPN gateway ip 주소를 붙여넣습니다.
1. **확인**을 선택합니다.
1. 시스템에서 필드가 업데이트 됩니다. 이 방법을 사용 하 여이 사이트에 라우팅하는 데 사용할 추가 IP 주소를 추가할 수도 있습니다.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>3 부-다른 VNet에 대 한 단계 반복

TestVNet4에 대 한 단계를 반복 합니다.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>구성 값 검색

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>연결 만들기

위의 모든 단계를 완료한 후에는 IPsec/IKE 사전 공유 키를 설정하고 연결을 만들 수 있습니다. 이번 단계에서는 PowerShell을 사용합니다. 포털에서 공유 키를 지정할 수 없기 때문에 Azure Portal에서 클래식 배포 모델에 대 한 VNet 간 연결을 구성할 수 없습니다.

아래 예제에서 공유 키가 정확히 동일한지 확인합니다. 공유 키는 항상 일치해야 합니다. 이 예제의 값을 VNet과 로컬 네트워크 사이트의 정확한 이름으로 변경해야 합니다.

1. TestVNet1 대 TestVNet4 연결을 만듭니다. 값을 변경 해야 합니다.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. TestVNet4 대 TestVNet1 연결을 만듭니다.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. 연결이 초기화될 때까지 기다립니다. 게이트웨이가 초기화된 후에는 상태가 ‘성공’입니다.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>FAQ 및 고려 사항

이러한 고려 사항은 클래식 가상 네트워크 및 클래식 가상 네트워크 게이트웨이에 적용 됩니다.

* 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독에 있을 수도 있습니다.
* 가상 네트워크는 같은 Azure 지역(위치)에 있을 수도 있고 다른 Azure 지역(위치)에 있을 수도 있습니다.
* 클라우드 서비스 또는 부하 분산 끝점은 서로 연결 되어 있더라도 가상 네트워크에 걸쳐 있을 수 없습니다.
* 여러 가상 네트워크를 연결할 때 VPN 디바이스는 필요하지 않습니다.
* VNet 간 연결은 Azure Virtual Network 연결을 지원합니다. 그러나 가상 네트워크에 배포되지 않은 가상 머신 또는 클라우드 서비스 연결은 지원하지 않습니다.
* VNet-VNet을 위해서는 동적 라우팅 게이트웨이가 필요합니다. Azure 정적 라우팅 게이트웨이는 지원되지 않습니다.
* 가상 네트워크 연결을 다중 사이트 VPN과 동시에 사용할 수 있습니다. 가상 네트워크 VPN 게이트웨이당 최대 10개의 VPN 터널을 다른 가상 네트워크 또는 온-프레미스 사이트에 연결할 수 있습니다.
* 가상 네트워크 및 온-프레미스 로컬 네트워크 사이트의 주소 공간이 겹쳐서는 안 됩니다. 주소 공간이 겹치면 가상 네트워크 만들기 또는 netcfg 구성 파일 업로드가 실패합니다.
* 가상 네트워크 한 쌍 간의 중복 터널은 지원되지 않습니다.
* P2S VPN을 비롯한 VNet의 모든 VPN 터널은 VPN 게이트웨이의 사용 가능한 대역폭 및 Azure의 동일 VPN 게이트웨이 작동 시간 SLA를 공유합니다.
* VNet-VNet 트래픽은 Azure 백본 전체에서 이동됩니다.

## <a name="next-steps"></a>다음 단계

연결을 확인합니다. [VPN Gateway 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.
