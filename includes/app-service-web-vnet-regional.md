---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: 54f80310f274b757d118f34542c1aa2e838ca7b9
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92082209"
---
지역 VNet 통합을 사용 하면 앱이 다음에 액세스할 수 있습니다.

* 앱과 동일한 지역에 있는 VNet의 리소스
* Vnet 피어 링의 리소스는 앱이 통합 된 VNet에 대 한 것입니다.
* 서비스 끝점 보안 서비스.
* Azure Express 경로 연결에서 리소스
* 통합 하는 VNet의 리소스.
* Azure Express 경로 연결을 포함 하는 피어 링 연결 간 리소스
* 프라이빗 엔드포인트 

동일한 지역에서 Vnet와 VNet 통합을 사용 하는 경우 다음과 같은 Azure 네트워킹 기능을 사용할 수 있습니다.

* **Nsgs (네트워크 보안 그룹)**: 통합 서브넷에 배치 된 nsgs를 사용 하 여 아웃 바운드 트래픽을 차단할 수 있습니다. VNet 통합을 사용 하 여 앱에 대 한 인바운드 액세스를 제공할 수 없으므로 인바운드 규칙이 적용 되지 않습니다.
* **UDRs (경로 테이블)**: 통합 서브넷에 경로 테이블을 추가 하 여 원하는 위치에 아웃 바운드 트래픽을 보낼 수 있습니다.

기본적으로 앱은 RFC1918 트래픽만 VNet에 라우팅합니다. 모든 아웃 바운드 트래픽을 VNet으로 라우팅하려면 앱 설정 WEBSITE_VNET_ROUTE_ALL 앱에 적용 합니다. 앱 설정을 구성 하려면:

1. 앱 포털에서 **구성** UI로 이동 합니다. **새 애플리케이션 설정**을 선택합니다.
1. **이름** 상자에 **WEBSITE_VNET_ROUTE_ALL** 를 입력 하 고 **값** 상자에 **1** 을 입력 합니다.

   ![응용 프로그램 설정 제공][4]

1. **확인**을 선택합니다.
1. **저장**을 선택합니다.

> [!NOTE]
> 모든 아웃 바운드 트래픽을 VNet으로 라우팅하는 경우 통합 서브넷에 적용 되는 NSGs 및 UDRs가 적용 됩니다. 모든 아웃 바운드 트래픽을 VNet으로 라우팅하는 경우 다른 곳에서 트래픽을 전송 하기 위한 경로를 제공 하지 않으면 아웃 바운드 주소는 여전히 앱 속성에 나열 된 아웃 바운드 주소입니다.

동일한 지역에서 Vnet와 VNet 통합을 사용 하는 경우 몇 가지 제한 사항이 있습니다.

* 전역 피어 링 연결에서 리소스에 연결할 수 없습니다.
* 이 기능은 PremiumV2 App Service 계획을 지 원하는 최신 Azure App Service 배율 단위 에서만 사용할 수 있습니다. *PremiumV2 가격 책정 계층에서 실행 해야*하는 것은 아닙니다. PremiumV2 옵션을 사용할 수 있는 App Service 계획에서 실행 되어야 한다는 것을 의미 하는 것은 아닙니다. 즉,이 VNet 통합 기능을 사용할 수 있는 최신 배율 단위 이기도 합니다.
* 통합 서브넷은 하나의 App Service 계획 에서만 사용할 수 있습니다.
* 이 기능은 App Service Environment에 있는 격리 된 계획 앱에서 사용할 수 없습니다.
* 이 기능을 사용 하려면 Azure Resource Manager VNet에서 32 주소 이상의/27 인 사용 하지 않는 서브넷이 필요 합니다.
* 앱과 VNet은 동일한 지역에 있어야 합니다.
* 통합 된 앱이 있는 VNet은 삭제할 수 없습니다. VNet을 삭제 하기 전에 통합을 제거 합니다.
* 앱과 동일한 구독에 있는 Vnet와만 통합할 수 있습니다.
* App Service 계획 마다 하나의 지역 VNet 통합만 사용할 수 있습니다. 동일한 App Service 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다.
* 지역 VNet 통합을 사용 하는 앱이 있는 경우에는 앱 또는 계획의 구독을 변경할 수 없습니다.
* 앱이 구성 변경 없이 Azure DNS Private Zones의 주소를 확인할 수 없습니다.

각 계획 인스턴스에는 하나의 주소가 사용 됩니다. 앱을 5 개의 인스턴스로 크기를 조정 하면 5 개의 주소가 사용 됩니다. 할당 후에는 서브넷 크기를 변경할 수 없으므로 앱이 도달할 수 있는 모든 규모를 수용할 수 있을 만큼 큰 서브넷을 사용 해야 합니다. 64 주소를 포함 하는/26은 권장 크기입니다. /26 64 주소는 30 개의 인스턴스가 있는 프리미엄 요금제를 수용 합니다. 계획의 크기를 조정 하거나 축소 하는 경우 짧은 시간 동안 두 번의 주소가 필요 합니다.

다른 계획의 앱이 다른 계획의 앱에 이미 연결 되어 있는 VNet에 도달 하 게 하려면 기존 VNet 통합에서 사용 중인 것과 다른 서브넷을 선택 합니다.

이 기능은 [사용자 지정 컨테이너](../articles/app-service/quickstart-custom-container.md)를 포함 하 여 Windows 및 Linux 앱 모두에 대해 완벽 하 게 지원 됩니다. 모든 동작은 Windows 앱과 Linux 앱 간에 동일 하 게 작동 합니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

지역 VNet 통합을 사용 하면 서비스 끝점을 사용할 수 있습니다. 앱에서 서비스 끝점을 사용 하려면 지역 VNet 통합을 사용 하 여 선택한 VNet에 연결한 다음 통합에 사용한 서브넷의 대상 서비스를 사용 하 여 서비스 끝점을 구성 합니다. 서비스 끝점을 통해 서비스에 액세스 하려면 다음을 수행 합니다.

1. 웹 앱과 지역 VNet 통합 구성
1. 대상 서비스로 이동 하 여 통합에 사용 되는 서브넷에 대해 서비스 끝점 구성

### <a name="network-security-groups"></a>네트워크 보안 그룹

네트워크 보안 그룹을 사용 하 여 VNet의 리소스에 대 한 인바운드 및 아웃 바운드 트래픽을 차단할 수 있습니다. 지역 VNet 통합을 사용 하는 앱은 [네트워크 보안 그룹][VNETnsg] 을 사용 하 여 VNet 또는 인터넷의 리소스에 대 한 아웃 바운드 트래픽을 차단할 수 있습니다. 공용 주소에 대 한 트래픽을 차단 하려면 응용 프로그램 설정 WEBSITE_VNET_ROUTE_ALL 1로 설정 해야 합니다. VNet 통합은 앱의 아웃 바운드 트래픽에만 영향을 주므로 NSG의 인바운드 규칙은 앱에 적용 되지 않습니다.

앱에 대 한 인바운드 트래픽을 제어 하려면 액세스 제한 기능을 사용 합니다. 통합 서브넷에 적용 되는 NSG는 통합 서브넷에 적용 된 경로에 관계 없이 적용 됩니다. WEBSITE_VNET_ROUTE_ALL 1로 설정 되 고 통합 서브넷의 공용 주소 트래픽에 영향을 주는 경로가 없는 경우 모든 아웃 바운드 트래픽은 여전히 통합 서브넷에 할당 된 NSGs에 적용 됩니다. WEBSITE_VNET_ROUTE_ALL 설정 되지 않은 경우 NSGs는 RFC1918 트래픽에만 적용 됩니다.

### <a name="routes"></a>경로

경로 테이블을 사용 하 여 앱의 아웃 바운드 트래픽을 원하는 위치로 라우팅할 수 있습니다. 기본적으로 경로 테이블은 RFC1918 대상 트래픽에만 영향을 줍니다. WEBSITE_VNET_ROUTE_ALL를 1로 설정 하면 모든 아웃 바운드 호출이 영향을 받습니다. 통합 서브넷에 설정 된 경로는 인바운드 앱 요청에 대 한 응답에 영향을 주지 않습니다. 일반적인 대상에는 방화벽 장치나 게이트웨이가 포함 될 수 있습니다.

모든 아웃 바운드 트래픽을 온-프레미스로 라우팅하는 경우 경로 테이블을 사용 하 여 모든 아웃 바운드 트래픽을 Express 경로 게이트웨이로 보낼 수 있습니다. 게이트웨이로 트래픽을 라우팅하는 경우에는 응답을 다시 보내도록 외부 네트워크에서 경로를 설정 해야 합니다.

BGP (Border Gateway Protocol) 경로도 앱 트래픽에 영향을 줍니다. Express 경로 게이트웨이와 같은 항목에서 BGP 경로를 사용할 경우 앱 아웃 바운드 트래픽이 영향을 받습니다. 기본적으로 BGP 경로는 RFC1918 대상 트래픽에만 영향을 줍니다. WEBSITE_VNET_ROUTE_ALL를 1로 설정 하면 모든 아웃 바운드 트래픽이 BGP 경로에 의해 영향을 받을 수 있습니다.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

앱이 VNet과 통합 되 면 VNet이 구성 된 것과 동일한 DNS 서버를 사용 합니다. 기본적으로 앱은 Azure DNS Private Zones에서 작동 하지 않습니다. Azure DNS Private Zones 작업 하려면 다음 앱 설정을 추가 해야 합니다.

1. WEBSITE_DNS_SERVER 값 168.63.129.16 
1. 값 1로 WEBSITE_VNET_ROUTE_ALL

이러한 설정은 앱에서 Azure DNS 전용 영역을 사용 하도록 설정 하는 것 외에도 앱의 모든 아웃 바운드 호출을 VNet으로 보냅니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트

[전용 끝점][privateendpoints]에 대 한 호출을 수행 하려면 Azure DNS Private Zones와 통합 하거나 앱에서 사용 하는 DNS 서버에서 개인 끝점을 관리 해야 합니다. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
