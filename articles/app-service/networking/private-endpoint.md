---
title: Azure 개인 엔드포인트를 사용하여 웹 앱에 비공개로 연결
description: Azure 개인 엔드포인트를 사용하여 웹 앱에 비공개로 연결
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756284"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Azure 웹 앱에 개인 끝점 사용(미리 보기)

> [!Note]
> 미리 보기는 모든 PremiumV2 Windows 및 Linux 웹 앱 및 탄력적 프리미엄 기능에 대한 미국 동부 및 미국 서부 2 지역에서 사용할 수 있습니다. 

Azure Web App에 개인 엔드포인트를 사용하여 개인 네트워크에 있는 클라이언트가 개인 링크를 통해 앱에 안전하게 액세스할 수 있도록 허용할 수 있습니다. 개인 끝점은 Azure VNet 주소 공간의 IP 주소를 사용합니다. 개인 네트워크의 클라이언트와 웹 앱 간의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 개인 링크를 통과하여 공용 인터넷에서 노출되지 않습니다.

웹 앱에 개인 끝점을 사용하면 다음을 수행할 수 있습니다.

- 비공개 엔드포인트를 구성하여 웹 앱을 보호하여 공개 노출을 제거합니다.
- VPN 또는 ExpressRoute 개인 피어링을 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 웹 앱에 안전하게 연결합니다.

VNet과 웹 앱 간에 보안 연결이 필요한 경우 서비스 엔드포인트가 가장 간단한 솔루션입니다. 또한 Azure 게이트웨이, 지역피어 VNet 또는 전역 피어잉 VNet을 통해 온-프레미스에서 웹 앱에 도달해야 하는 경우 프라이빗 엔드포인트가 솔루션입니다.  

자세한 내용은 [서비스 끝점을][serviceendpoint]참조하십시오.

## <a name="conceptual-overview"></a>개념적 개요

개인 엔드포인트는 가상 네트워크(VNet)의 서브넷에 있는 Azure 웹 앱에 대한 특수 네트워크 인터페이스(NIC)입니다.
웹 앱에 대한 개인 엔드포인트를 만들 면 개인 네트워크의 클라이언트와 웹 앱 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트는 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다.
개인 끝점과 웹 앱 간의 연결은 보안 [개인 링크를][privatelink]사용합니다. 개인 끝점은 웹 앱으로 들어오는 흐름에만 사용됩니다. 나가는 흐름은 이 개인 엔드포인트를 사용하지 않지만 [VNet 통합 기능을][vnetintegrationfeature]통해 다른 서브넷에서 네트워크에 나가는 흐름을 삽입할 수 있습니다.

프라이빗 엔드포인트를 연결하는 서브넷에는 다른 리소스가 있을 수 있으므로 전용 빈 서브넷이 필요하지 않습니다.
웹 앱이 아닌 다른 지역에 비공개 끝점을 배포할 수도 있습니다. 

> [!Note]
>VNet 통합 기능은 개인 엔드포인트와 동일한 서브넷을 사용할 수 없으며 이는 VNet 통합 기능의 제한사항입니다.

보안 관점에서:

- 웹 앱에 비공개 끝점을 사용하도록 설정하면 모든 공용 액세스가 비활성화됩니다.
- 다른 지역의 VNet을 포함하여 다른 VNet 및 서브넷에서 여러 개인 끝점을 활성화할 수 있습니다.
- 프라이빗 엔드포인트 NIC의 IP 주소는 동적이어야 하지만 비공개 끝점을 삭제할 때까지 동일하게 유지됩니다.
- 개인 끝점의 NIC에는 NSG가 연결되어 있을 수 없습니다.
- 개인 끝점을 호스팅하는 서브넷에는 NSG가 연결될 수 있지만 개인 끝점에 대한 네트워크 [Disable network policies for private endpoints][disablesecuritype]정책 적용을 비활성화해야 합니다. 따라서 NSG에서 개인 끝점에 대한 액세스를 필터링할 수 없습니다.
- 웹 앱에 개인 끝점을 사용하도록 설정하면 웹 앱의 [액세스 제한][accessrestrictions] 구성이 평가되지 않습니다.
- 대상이 인터넷 또는 Azure 서비스에 태그되는 모든 NSG 규칙을 제거하여 VNet에서 데이터 유출 위험을 줄일 수 있습니다. 그러나 서브넷에 웹 앱 개인 엔드포인트를 추가하면 동일한 배포 스탬프에서 호스팅되고 인터넷에 노출된 모든 웹 앱에 도달할 수 있습니다.

웹 앱의 웹 HTTP 로그에서 클라이언트 원본 IP를 찾을 수 있습니다. 이는 TCP 프록시 프로토콜을 사용하여 구현되어 클라이언트 IP 속성을 웹 앱으로 전달합니다. 자세한 내용은 [TCP 프록시 v2를 사용하여 연결 정보 얻기를][tcpproxy]참조하십시오.


  > [!div class="mx-imgBorder"]
  > ![웹 앱 프라이빗 엔드포인트 글로벌 개요](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

이 기능은 미리 보기 중이기 때문에 미리 보기 중에 DNS 항목을 변경하지 않습니다. 개인 DNS 서버 또는 Azure DNS 개인 영역에서 DNS 항목을 직접 관리해야 합니다.
사용자 지정 DNS 이름을 사용해야 하는 경우 웹 앱에 사용자 지정 이름을 추가해야 합니다. 미리 보기 중에 공용 DNS 확인을 사용하여 사용자 지정 이름의 유효성을 검사해야 합니다. 자세한 내용은 [사용자 지정 DNS 유효성 검사를][dnsvalidation] 참조하십시오.

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정][pricing]을 참조하세요.

## <a name="limitations"></a>제한 사항

우리는 정기적으로 개인 링크 기능 및 개인 엔드 포인트를 개선하고, 제한에 대한 최신 정보에 [대한이 문서를][pllimitations] 확인합니다.

## <a name="next-steps"></a>다음 단계

포털을 통해 웹 앱에 대한 비공개 엔드포인트를 배포하려면 [웹 앱에 개인적으로 연결하는 방법을][howtoguide] 참조하세요.




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
