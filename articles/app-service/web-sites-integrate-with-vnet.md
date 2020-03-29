---
title: Azure 가상 네트워크와 앱 통합
description: Azure 앱 서비스에서 앱을 Azure 가상 네트워크와 통합합니다.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673219"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure Virtual Network에 앱 통합
이 문서에서는 Azure App Service 가상 네트워크 통합 기능 및 Azure 앱 [서비스의](https://go.microsoft.com/fwlink/?LinkId=529714)앱으로 설정하는 방법에 대해 설명합니다. [Azure 가상][VNETOverview] 네트워크(VNet)를 사용하면 많은 Azure 리소스를 비인터넷 라우팅 네트워크에 배치할 수 있습니다.  

Azure 앱 서비스에는 두 가지 변형이 있습니다.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet 통합 사용 

1. 앱 서비스 포털의 네트워킹 UI로 이동합니다. VNet 통합에서 *"여기를 클릭하여 구성"을 선택합니다.* 

1. **VNet 추가**를 선택합니다.  

   ![VNet 통합 선택][1]

1. 드롭다운 목록에는 동일한 리전의 구독에 있는 모든 리소스 관리자 VNet이 포함되며, 그 아래에는 다른 모든 리전의 모든 리소스 관리자 VNet 목록이 포함됩니다. 통합할 VNet을 선택합니다.

   ![VNet 선택][2]

   * VNet이 동일한 지역에 있는 경우 새 서브넷을 만들거나 빈 기존 서브넷을 선택합니다. 

   * 다른 리전에서 VNet을 선택하려면 사이트 간 을 사용하도록 사용하도록 프로비전된 가상 네트워크 게이트웨이가 있어야 합니다.

   * 클래식 VNet과 통합하려면 VNet 드롭다운을 클릭하는 대신 여기를 클릭하여 **클래식 VNet에 연결합니다.** 원하는 클래식 VNet을 선택합니다. 대상 VNet에는 사이트 간 을 사용하도록 사용하도록 프로비전된 가상 네트워크 게이트웨이가 이미 있어야 합니다.

    ![클래식 VNet 선택][3]
    
통합하는 동안에 앱이 다시 시작됩니다.  통합이 완료되면 통합된 VNet에 대한 세부 정보가 표시됩니다. 

앱이 VNet과 통합되면 Azure DNS 개인 영역이 아니면 VNet이 구성한 것과 동일한 DNS 서버를 사용합니다. 현재 Azure DNS 개인 영역과 VNet 통합을 사용할 수 없습니다.

## <a name="regional-vnet-integration"></a>지역 VNet 통합

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>지역 별 VNet 통합 작동 방식

앱 서비스의 앱은 작업자 역할에서 호스팅됩니다. 기본 및 더 높은 가격 책정 계획은 동일한 작업자에서 실행되는 다른 고객 워크로드가 없는 전용 호스팅 계획입니다. 지역 VNet 통합은 위임된 서브넷에 주소가 있는 가상 인터페이스를 탑재하여 작동합니다. 보낸 주소는 VNet에 있기 때문에 VNet의 VM과 마찬가지로 VNet내 또는 VNet을 통해 대부분의 사물에 액세스할 수 있습니다. 네트워킹 구현은 VNet에서 VM을 실행하는 것과 다르기 때문에 이 기능을 사용하는 동안 일부 네트워킹 기능을 아직 사용할 수 없습니다.

![지역 VNet 통합 작동 방식][5]

지역 별 VNet 통합을 사용하도록 설정하면 앱은 평소와 동일한 채널을 통해 인터넷에 아웃바운드 호출을 계속 합니다. 앱 속성 포털에 나열된 아웃바운드 주소는 여전히 앱에서 사용하는 주소입니다. 앱의 변경 내용, 서비스 엔드포인트 보안 서비스 또는 RFC 1918 주소에 대한 호출은 VNet으로 이동합니다. WEBSITE_VNET_ROUTE_ALL 1로 설정된 경우 모든 아웃바운드 트래픽을 VNet으로 보낼 수 있습니다. 

이 기능은 작업자당 하나의 가상 인터페이스만 지원합니다.  작업자당 하나의 가상 인터페이스는 앱 서비스 계획당 하나의 지역 VNet 통합을 의미합니다. 동일한 앱 서비스 계획의 모든 앱은 동일한 VNet 통합을 사용할 수 있지만 추가 VNet에 연결하기 위해 앱이 필요한 경우 다른 앱 서비스 계획을 만들어야 합니다. 사용되는 가상 인터페이스는 고객이 직접 액세스할 수 있는 리소스가 아닙니다.

이 기술의 작동 방식특성상 VNet 통합에 사용되는 트래픽이 네트워크 감시자 또는 NSG 흐름 로그에 표시되지 않습니다.  

## <a name="gateway-required-vnet-integration"></a>게이트웨이 필수 VNet 통합

게이트웨이 필수 VNet 통합은 다른 리전또는 클래식 VNet에 대한 연결지원을 지원합니다. 게이트웨이 필수 VNet 통합: 

* 앱이 한 번에 1개의 VNet에만 연결할 수 있도록 합니다.
* 앱 서비스 계획 내에서 최대 5개의 VNet을 통합할 수 있습니다. 
* 앱 서비스 계획에서 사용할 수 있는 총 수에 영향을 주지 않고 앱 서비스 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다.  동일한 앱 서비스 계획에서 동일한 VNet을 사용하는 앱이 6개 있는 경우 사용 중인 VNet은 1개입니다. 
* 게이트웨이의 SLA로 인해 99.9% SLA 지원
* 앱에서 VNet이 구성한 DNS를 사용할 수 있도록 합니다.
* 앱에 연결하기 전에 SSTP 지점 간 VPN으로 구성된 가상 네트워크 경로 기반 게이트웨이가 필요합니다. 

게이트웨이 필수 VNet 통합을 사용할 수 없습니다.

* 리눅스 애플 리 케이 션으로
* 익스프레스루트와 연결된 VNet 
* 서비스 엔드포인트 보안 리소스에 액세스하려면
* ExpressRoute 및 사이트/사이트 간 VPN을 모두 지원하는 공존 게이트웨이

### <a name="set-up-a-gateway-in-your-vnet"></a>VNet에 게이트웨이 설정 ###

게이트웨이를 만들려면,

1. VNet에 [게이트웨이 서브넷을 만듭니다][creategatewaysubnet].  

1. [VPN 게이트웨이를 만듭니다.][creategateway] 경로 기반 VPN 종류를 선택합니다.

1. [지점 및 사이트 간 주소를 설정합니다][setp2saddresses]. 게이트웨이가 기본 SKU에 없는 경우 사이트 구성 지점에서 IKEV2를 사용하지 않도록 설정해야 하며 SSTP를 선택해야 합니다. 사이트 주소 공간은 RFC 1918 주소 블록, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16에 있어야 합니다.

앱 서비스 VNet 통합과 함께 사용할 게이트웨이를 만드는 경우 인증서를 업로드할 필요가 없습니다. 게이트웨이를 만드는 데 30분이 걸릴 수 있습니다. 게이트웨이가 프로비전될 때까지는 VNet과 앱을 통합할 수 없습니다. 

### <a name="how-gateway-required-vnet-integration-works"></a>게이트웨이 필수 VNet 통합 작동 방식

게이트웨이에는 사이트 VPN 기술에 기반하여 구축된 VNet 통합이 필요합니다. 사이트 VPN을 가리키면 앱이 호스팅되는 가상 시스템에만 네트워크 액세스가 제한됩니다. 앱은 하이브리드 연결 또는 VNet 통합을 통해서만 트래픽을 인터넷으로 보내도록 제한됩니다. 앱이 게이트웨이 필수 VNet 통합을 사용하도록 포털로 구성되면 게이트웨이 및 응용 프로그램 측에서 인증서를 만들고 할당하기 위해 복잡한 협상이 관리됩니다. 결과적으로 앱을 호스팅하는 데 사용되는 작업자가 선택한 VNet의 가상 네트워크 게이트웨이에 직접 연결할 수 있습니다. 

![게이트웨이 필수 VNet 통합 작동 방식][6]

### <a name="accessing-on-premises-resources"></a>온-프레미스 리소스 액세스

앱은 사이트 간 연결이 있는 VNet과 통합되어 온-프레미스 리소스에 액세스할 수 있습니다. 게이트웨이 필수 VNet 통합을 사용하는 경우 지점 간 주소 블록을 사용하여 온-프레미스 VPN 게이트웨이 경로를 업데이트해야 합니다. 사이트 간 VPN을 처음 설정하는 경우 이를 구성하는 데 사용되는 스크립트에서 경로를 올바르게 설정해야 합니다. 사이트 간 VPN을 만든 후에 지점 및 사이트 간 VPN을 추가하는 경우 해당 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행하는 방법에 대한 세부 정보는 게이트웨이마다 다르며 여기에 설명되어 있지 않습니다. BGP는 사이트 간 VPN 연결로 구성할 수 없습니다.

지역 VNet 통합 기능이 VNet을 통해 온-프레미스에 도달하는 데 필요한 추가 구성은 없습니다. ExpressRoute 또는 사이트 간 VPN을 사용하여 VNet을 온-프레미스에 연결하기만 하면 됩니다. 

> [!NOTE]
> 게이트웨이 필수 VNet 통합 기능은 익스프레스루트 게이트웨이가 있는 VNet과 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 [동시 사용 모드][VPNERCoex]로 구성되어 있더라도 VNet 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우 VNet에서 실행되는 지역 VNet 통합 기능 또는 [앱 서비스 환경을][ASE]사용할 수 있습니다. 
> 
> 

### <a name="peering"></a>피어링

지역 VNet 통합을 사용하여 피어링을 사용하는 경우 추가 구성을 수행할 필요가 없습니다. 

피어링과 함께 게이트웨이 필수 VNet 통합을 사용하는 경우 몇 가지 추가 항목을 구성해야 합니다. 앱에서 작동하도록 피어링을 구성하려면,

1. 앱이 연결되는 VNet에 피어링 연결을 추가합니다. 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **게이트 전송 허용**을 선택합니다.
1. 연결된 VNet에 피어링되는 VNet에 피어링 연결을 추가합니다. 대상 VNet에 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **원격 게이트웨이 허용**을 선택합니다.
1. 포털에서 App Service 계획 > 네트워킹 > VNet 통합 UI로 차례로 이동합니다.  앱이 연결되는 VNet을 선택합니다. 라우팅 섹션 아래에서 앱이 연결되는 VNet과 피어링되는 VNet의 주소 범위를 추가합니다.  

## <a name="managing-vnet-integration"></a>VNet 통합 관리 

VNet으로 연결하고 연결을 끊는 것은 앱 수준입니다. 여러 앱에서 VNet 통합에 영향을 줄 수 있는 작업은 App Service 계획 수준에 있습니다. 네트워킹 > > VNet 통합 포털에서 VNet에 대한 세부 정보를 얻을 수 있습니다. ASP > 네트워킹 > VNet 통합 포털의 ASP 수준에서 유사한 정보를 볼 수 있습니다.

VNet 통합의 앱 보기에 가져올 수 있는 유일한 작업은 현재 연결되어 있는 VNet과 앱의 연결을 해제하는 것입니다. VNet에서 앱의 연결을 끊으려면 **연결 끊기**를 선택합니다. VNet과의 연결을 끊으면 앱이 다시 시작됩니다. 연결을 끊더라도 VNet은 변경되지 않습니다. 서브넷 또는 게이트웨이가 제거되지 않습니다. 그런 다음 VNet을 삭제하려면 먼저 VNet에서 앱을 분리하고 게이트웨이와 같은 리소스를 삭제해야 합니다. 

ASP VNet 통합 UI는 ASP의 앱에서 사용하는 모든 VNet 통합을 보여 줍니다. 각 VNet에 대한 세부 정보를 보려면 관심 있는 VNet을 클릭합니다. 게이트웨이 필수 VNet 통합에 대해 여기에서 수행할 수 있는 두 가지 작업이 있습니다.

* **네트워크 동기화**. 동기화 네트워크 작업은 게이트웨이 종속 VNet 통합 기능에만 적합합니다. 동기화 네트워크 작업을 수행하면 인증서와 네트워크 정보가 동기화됩니다. VNet의 DNS를 추가하거나 변경하는 경우 **동기화 네트워크** 작업을 수행해야 합니다. 이 작업이 수행되면 이 VNet을 사용하는 모든 앱이 다시 시작됩니다.
* **경로 추가**. 경로를 추가하면 아웃바운드 트래픽이 VNet으로 전달됩니다. 

**게이트웨이 필수 VNet 통합 라우팅** VNet에 정의된 경로는 앱에서 VNet으로 트래픽을 직접 변환하는 데 사용됩니다. 아웃바운드 트래픽을 VNet에 추가로 보내야 하는 경우 해당 주소 블록을 여기에 추가할 수 있습니다. 이 기능은 게이트웨이에 필요한 VNet 통합에서만 작동합니다. 라우팅 테이블은 게이트웨이 필수 VNet 통합을 사용하는 경우 지역 VNet 통합과 같은 방식으로 앱 트래픽에 영향을 주지 않습니다.

**게이트웨이 필수 VNet 통합 인증서** 게이트웨이에 VNet 통합이 활성화되어 있으면 연결 보안을 보장하기 위해 인증서교환이 필요합니다. 인증서와 함께 네트워크를 설명하는 DNS 구성, 경로 및 다른 비슷한 항목이 있습니다.
인증서 또는 네트워크 정보가 변경되면 "네트워크 동기화"를 클릭해야 합니다. “네트워크 동기화”를 클릭하면 앱과 VNet 간의 연결이 잠시 중단됩니다. 앱이 다시 시작되지는 않지만, 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다. 

## <a name="pricing-details"></a>가격 정보
지역 VNet 통합 기능은 ASP 가격 책정 계층 요금 이외에 는 추가 요금이 부과되지 않습니다.

게이트웨이 필수 VNet 통합 기능 사용에 대한 세 가지 관련 요금이 있습니다.

* ASP 가격 책정 계층 요금 - 앱은 표준, 프리미엄 또는 PremiumV2 앱 서비스 요금제에 속해야 합니다. 해당 비용에 대한 자세한 정보는 [App Service 가격][ASPricing]을 참조하세요. 
* 데이터 전송 비용 - VNet이 동일한 데이터 센터에 있더라도 데이터 송신에 대한 요금이 부과됩니다. 이러한 요금은 [데이터 전송 가격 정보][DataPricing]에서 설명하고 있습니다. 
* VPN 게이트웨이 비용 - 지점 간 VPN에 필요한 VNet 게이트웨이에 대한 비용이 있습니다. 자세한 내용은 [VPN Gateway 가격 페이지][VNETPricing]에 있습니다.

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

지역 VNet 통합에 대한 CLI 지원이 있습니다. 다음 명령에 액세스하려면 [Azure CLI 설치.][installCLI] 

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

게이트웨이 필수 VNet 통합의 경우 PowerShell을 사용하여 앱 서비스를 Azure 가상 네트워크와 통합할 수 있습니다. 실행 준비 스크립트의 경우 [Azure App Service에서 Azure Virtual Network에 앱 연결](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)을 참조하세요.


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
