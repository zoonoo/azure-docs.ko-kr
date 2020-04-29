---
title: Azure Virtual Network와 앱 통합
description: Azure App Service의 앱을 Azure virtual network와 통합 합니다.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770851"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure 가상 네트워크와 앱 통합

이 문서에서는 Azure App Service VNet 통합 기능 및 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)앱으로 설정 하는 방법을 설명 합니다. [Azure Virtual Network][VNETOverview] (vnet)를 사용 하 여 인터넷 라우팅할 수 없는 네트워크에 많은 azure 리소스를 둘 수 있습니다.

Azure App Service는 두 가지 변형이 있습니다.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet 통합 사용

> [!NOTE]
> Linux 앱의 메뉴에서 "네트워킹" 블레이드를 사용 하지 않도록 설정 (회색으로 표시) 한 경우 해당 기능을 현재 사용할 수 없음을 의미 합니다.
>

1. App Service 포털에서 **네트워킹** UI로 이동 합니다. **VNet 통합**에서 **구성 하려면 여기를 클릭**하세요 .를 선택 합니다.

1. **VNet 추가**를 선택합니다.

   ![VNet 통합 선택][1]

1. 드롭다운 목록에는 동일한 지역에 있는 구독의 모든 Azure Resource Manager 가상 네트워크가 포함 됩니다. 그 아래에는 다른 모든 지역에서 리소스 관리자 가상 네트워크가 나열 됩니다. 통합 하려는 VNet을 선택 합니다.

   ![VNet 선택][2]

   * VNet이 동일한 지역에 있는 경우 새 서브넷을 만들거나 빈 기존 서브넷을 선택 합니다.
   * 다른 지역에서 VNet을 선택 하려면 point to site를 사용 하도록 설정 된 VNet 게이트웨이를 프로 비전 해야 합니다.
   * 클래식 VNet과 통합 하려면 **Virtual Network** 드롭다운 목록을 선택 하는 대신 **클래식 vnet에 연결 하려면 여기를 클릭**하세요 .를 선택 합니다. 원하는 클래식 가상 네트워크를 선택 합니다. 대상 VNet에는 지점 및 사이트 간 사용으로 프로 비전 된 Virtual Network 게이트웨이가 이미 있어야 합니다.

    ![클래식 VNet 선택][3]

통합하는 동안에 앱이 다시 시작됩니다. 통합이 완료 되 면 통합 된 VNet에 대 한 세부 정보가 표시 됩니다.

## <a name="regional-vnet-integration"></a>지역 VNet 통합

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>지역 VNet 통합의 작동 방식

App Service의 앱은 작업자 역할에서 호스팅됩니다. 기본 및 더 높은 가격 책정 요금제는 동일한 작업자에서 실행 되는 다른 고객의 작업이 없는 전용 호스팅 계획입니다. 지역 VNet 통합은 위임 된 서브넷의 주소를 사용 하 여 가상 인터페이스를 탑재 하는 방식으로 작동 합니다. 보낸 사람 주소는 VNet에 있기 때문에 vnet의 VM과 마찬가지로 vnet을 통해 또는의 모든 항목에 액세스할 수 있습니다. 네트워킹 구현은 VNet에서 VM을 실행 하는 것과는 다릅니다. 이러한 이유로 일부 네트워킹 기능은 아직이 기능을 사용할 수 없습니다.

![지역 VNet 통합의 작동 방식][5]

지역 VNet 통합을 사용 하도록 설정 하면 앱에서 평소와 동일한 채널을 통해 인터넷에 대 한 아웃 바운드 호출을 수행 합니다. 앱 속성 포털에 나열 된 아웃 바운드 주소는 앱에서 여전히 사용 되는 주소입니다. 앱에 대 한 변경 내용은 서비스 끝점 보안 서비스에 대 한 호출 또는 RFC 1918 주소가 VNet으로 이동 하는 것입니다. WEBSITE_VNET_ROUTE_ALL를 1로 설정 하면 모든 아웃 바운드 트래픽이 VNet으로 전송 될 수 있습니다.

이 기능은 작업자 당 하나의 가상 인터페이스만 지원 합니다. 작업자 당 하나의 가상 인터페이스는 App Service 계획 당 하나의 지역 VNet 통합을 의미 합니다. 동일한 App Service 계획의 모든 앱은 동일한 VNet 통합을 사용할 수 있습니다. 추가 VNet에 연결 하기 위해 앱이 필요한 경우 다른 App Service 계획을 만들어야 합니다. 사용 되는 가상 인터페이스는 고객이 직접 액세스할 수 있는 리소스가 아닙니다.

이 기술이 작동 하는 방법의 특성상 VNet 통합에 사용 되는 트래픽은 Azure Network Watcher 또는 NSG 흐름 로그에 표시 되지 않습니다.

## <a name="gateway-required-vnet-integration"></a>게이트웨이-필수 VNet 통합

게이트웨이-필요한 VNet 통합은 다른 지역의 VNet 또는 클래식 가상 네트워크에 대 한 연결을 지원 합니다. 게이트웨이-필수 VNet 통합:

* 앱이 한 번에 하나의 VNet에만 연결할 수 있도록 합니다.
* App Service 계획 내에서 최대 5 개의 Vnet를 통합할 수 있습니다.
* App Service 계획에서 사용할 수 있는 총 개수에 영향을 주지 않고 App Service 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다. 동일한 App Service 계획에서 동일한 VNet을 사용 하는 앱이 6 개 있는 경우 사용 되는 하나의 VNet으로 계산 됩니다.
* 게이트웨이의 SLA로 인해 99.9% SLA를 지원 합니다.
* 앱에서 VNet이 구성 된 DNS를 사용할 수 있도록 합니다.
* 를 사용 하려면 SSTP 지점 및 사이트 간 VPN을 사용 하 여 구성 된 Virtual Network 경로 기반 게이트웨이가 앱에 연결 될 수 있어야 합니다.

게이트웨이 필수 VNet 통합을 사용할 수 없습니다.

* Linux 앱을 사용 합니다.
* Azure Express 경로와 연결 된 VNet 사용.
* 서비스 끝점의 보안 된 리소스에 액세스 하는 데 사용 됩니다.
* Express 경로와 지점 및 사이트 간 또는 사이트 간 Vpn을 모두 지 원하는 공존 게이트웨이를 사용 합니다.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Azure 가상 네트워크에서 게이트웨이 설정 ###

게이트웨이를 만들려면,

1. VNet에 [게이트웨이 서브넷을 만듭니다][creategatewaysubnet].  

1. [VPN gateway를 만듭니다][creategateway]. 경로 기반 VPN 종류를 선택합니다.

1. [지점 및 사이트 간 주소를 설정][setp2saddresses]합니다. 게이트웨이가 기본 SKU에 없으면 지점 및 사이트 간 구성에서 IKEV2를 사용하지 않도록 설정해야 하며 SSTP를 선택해야 합니다. 지점 및 사이트 간 주소 공간은 RFC 1918 주소 블록 10.0.0.0/8, 172.16.0.0/12 및 192.168.0.0/16에 있어야 합니다.

App Service VNet 통합에 사용할 게이트웨이를 만드는 경우 인증서를 업로드할 필요가 없습니다. 게이트웨이를 만드는 데 30분이 걸릴 수 있습니다. 게이트웨이가 프로 비전 될 때까지 VNet에 앱을 통합할 수 없습니다.

### <a name="how-gateway-required-vnet-integration-works"></a>게이트웨이 필수 VNet 통합 작동 방법

게이트웨이-필수 VNet 통합은 지점 및 사이트 간 VPN 기술을 기반으로 합니다. 지점 및 사이트 간 Vpn은 앱을 호스트 하는 가상 컴퓨터에 대 한 네트워크 액세스를 제한 합니다. 앱은 하이브리드 연결 또는 VNet 통합을 통해서만 인터넷으로 트래픽을 전송 하도록 제한 됩니다. 앱이 포털을 사용 하 여 게이트웨이 필수 VNet 통합을 사용 하도록 구성 된 경우 사용자를 대신해 복잡 한 협상이 관리 되어 게이트웨이 및 응용 프로그램 쪽에서 인증서를 만들고 할당 합니다. 따라서 앱을 호스트 하는 데 사용 되는 작업자는 선택한 VNet의 가상 네트워크 게이트웨이에 직접 연결할 수 있습니다.

![게이트웨이 필수 VNet 통합 작동 방법][6]

### <a name="access-on-premises-resources"></a>온-프레미스 리소스 액세스

앱은 사이트 간 연결이 있는 VNet과 통합되어 온-프레미스 리소스에 액세스할 수 있습니다. 게이트웨이 필수 VNet 통합을 사용 하는 경우 지점 및 사이트 간 주소 블록을 사용 하 여 온-프레미스 VPN gateway 경로를 업데이트 합니다. 사이트 간 VPN을 처음 설정하는 경우 이를 구성하는 데 사용되는 스크립트에서 경로를 올바르게 설정해야 합니다. 사이트 간 VPN을 만든 후에 지점 및 사이트 간 VPN을 추가하는 경우 해당 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행 하는 방법에 대 한 세부 정보는 게이트웨이 마다 다르며 여기에 설명 되어 있지 않습니다. BGP를 사이트 간 VPN 연결로 구성할 수 없습니다.

VNet을 통해 온-프레미스 리소스에 연결 하기 위해 지역 VNet 통합 기능에 대 한 추가 구성은 필요 하지 않습니다. Express 경로 또는 사이트 간 VPN을 사용 하 여 VNet을 온-프레미스 리소스에 연결 하기만 하면 됩니다.

> [!NOTE]
> 게이트웨이 필수 VNet 통합 기능은 Express 경로 게이트웨이가 있는 VNet에 앱을 통합 하지 않습니다. Express 경로 게이트웨이가 [공존 모드][VPNERCoex]에서 구성 된 경우에도 VNet 통합은 작동 하지 않습니다. Express 경로 연결을 통해 리소스에 액세스 해야 하는 경우에는 VNet에서 실행 되는 지역 VNet 통합 기능 또는 [App Service Environment][ASE]을 사용 합니다.
> 
> 

### <a name="peering"></a>피어링

지역 VNet 통합에서 피어 링을 사용 하는 경우 추가 구성을 수행할 필요가 없습니다.

피어 링과 함께 게이트웨이 필수 VNet 통합을 사용 하는 경우 몇 가지 추가 항목을 구성 해야 합니다. 앱에서 작동하도록 피어링을 구성하려면,

1. 앱이 연결되는 VNet에 피어링 연결을 추가합니다. 피어 링 연결을 추가 하는 경우 **가상 네트워크 액세스 허용** 을 사용 하도록 설정 하 고 **전달 된 트래픽 허용** 및 **게이트웨이 전송 허용**을 선택 합니다.
1. 연결 된 VNet에 피어 링 되는 VNet에서 피어 링 연결을 추가 합니다. 대상 VNet에서 피어 링 연결을 추가 하는 경우 **가상 네트워크 액세스 허용** 을 사용 하도록 설정 하 고 **전달 된 트래픽 허용** 및 **원격 게이트웨이 허용**을 선택 합니다.
1. 포털에서 **App Service 계획** > **네트워킹** > **VNet 통합** UI로 이동 합니다. 앱이 연결되는 VNet을 선택합니다. 라우팅 섹션 아래에서 앱이 연결 된 VNet에 피어 링 된 VNet의 주소 범위를 추가 합니다.

## <a name="manage-vnet-integration"></a>VNet 통합 관리

VNet에 연결 하 고 연결을 끊는 것은 앱 수준입니다. 여러 앱에서 VNet 통합에 영향을 줄 수 있는 작업은 App Service 계획 수준에 있습니다. 앱 > **네트워킹** > **vnet 통합** 포털에서 vnet에 대 한 세부 정보를 가져올 수 있습니다. **App Service plan** > **네트워킹** > **VNet 통합** 포털의 App Service 계획 수준에서 유사한 정보를 볼 수 있습니다.

VNet 통합 인스턴스의 앱 보기에서 수행할 수 있는 유일한 작업은 현재 연결 되어 있는 VNet에서 앱을 연결 해제 하는 것입니다. VNet에서 앱의 연결을 끊으려면 **연결 끊기**를 선택합니다. VNet에서 연결을 끊으면 앱이 다시 시작 됩니다. 연결을 끊더라도 VNet은 변경되지 않습니다. 서브넷 또는 게이트웨이가 제거 되지 않습니다. 그런 다음 VNet을 삭제 하려면 먼저 VNet에서 앱을 분리 하 고 게이트웨이 등의 리소스를 삭제 합니다.

App Service 계획 VNet 통합 UI에는 App Service 계획의 앱에서 사용 하는 모든 VNet 통합이 표시 됩니다. 각 VNet에 대 한 세부 정보를 보려면 관심 있는 VNet을 선택 합니다. 게이트웨이 필수 VNet 통합에 대해 여기에서 수행할 수 있는 두 가지 작업이 있습니다.

* **동기화 네트워크**: 네트워크 동기화 작업은 게이트웨이 종속 VNet 통합 기능에만 사용 됩니다. 네트워크 동기화 작업을 수행 하면 인증서와 네트워크 정보가 동기화 됩니다. VNet의 DNS를 추가 하거나 변경 하는 경우 동기화 네트워크 작업을 수행 합니다. 이 작업은이 VNet을 사용 하는 모든 앱을 다시 시작 합니다.
* **경로 추가**: 경로 추가는 VNet에 아웃 바운드 트래픽을 구동 합니다.

### <a name="gateway-required-vnet-integration-routing"></a>게이트웨이-필수 VNet 통합 라우팅
VNet에 정의 된 경로는 앱에서 VNet으로 트래픽을 전달 하는 데 사용 됩니다. 추가 아웃 바운드 트래픽을 VNet에 보내려면 여기에 이러한 주소 블록을 추가 합니다. 이 기능은 게이트웨이 필수 VNet 통합 에서만 작동 합니다. 경로 테이블은 지역 VNet 통합을 사용 하는 방식으로 게이트웨이 필수 VNet 통합을 사용 하는 경우 앱 트래픽에 영향을 주지 않습니다.

### <a name="gateway-required-vnet-integration-certificates"></a>게이트웨이-필요한 VNet 통합 인증서
게이트웨이 필수 VNet 통합이 사용 하도록 설정 된 경우 연결의 보안을 보장 하기 위해 필요한 인증서 교환이 필요 합니다. 인증서와 함께 네트워크를 설명하는 DNS 구성, 경로 및 다른 비슷한 항목이 있습니다.

인증서 또는 네트워크 정보가 변경 되 면 **네트워크 동기화**를 선택 합니다. **네트워크 동기화**를 선택 하면 앱과 VNet 간의 연결이 잠시 중단 될 수 있습니다. 앱이 다시 시작되지는 않지만, 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다.

## <a name="pricing-details"></a>가격 정보
지역 VNet 통합 기능에는 App Service 계획 가격 책정 계층 요금 외에 추가 요금이 부과 되지 않습니다.

게이트웨이-필수 VNet 통합 기능 사용과 관련 하 여 세 가지 요금이 청구 됩니다.

* **App Service 계획 가격 책정 계층 요금**: 앱은 Standard, Premium 또는 PremiumV2 App Service 계획에 있어야 합니다. 이러한 비용에 대 한 자세한 내용은 [App Service 가격 책정][ASPricing]을 참조 하세요.
* **데이터 전송 비용**: VNet이 동일한 데이터 센터에 있더라도 데이터 송신 요금이 부과 됩니다. 이러한 요금은 [데이터 전송 가격 정보][DataPricing]에 설명 되어 있습니다.
* **VPN gateway 비용**: 지점 및 사이트 간 vpn에 필요한 가상 네트워크 게이트웨이에 대 한 비용이 발생 합니다. 자세한 내용은 [VPN gateway 가격 책정][VNETPricing]을 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI 지원은 지역 VNet 통합에 사용할 수 있습니다. 다음 명령에 액세스 하려면 [Azure CLI를 설치][installCLI]합니다.

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

게이트웨이 필수 VNet 통합의 경우 PowerShell을 사용 하 여 Azure 가상 네트워크와 App Service를 통합할 수 있습니다. 바로 실행 가능한 스크립트는 [Azure 가상 네트워크에 Azure App Service에 앱 연결](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)을 참조 하세요.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
[privateendpoints]: networking/private-endpoint.md
