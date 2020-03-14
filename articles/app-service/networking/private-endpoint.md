---
title: Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결
description: Azure 개인 끝점을 사용 하 여 개인적으로 웹 앱에 연결
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136675"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Azure 웹 앱 (미리 보기)에 대 한 개인 끝점 사용

Azure 웹 앱에 대 한 개인 끝점을 사용 하 여 개인 네트워크에 있는 클라이언트가 개인 링크를 통해 앱에 안전 하 게 액세스할 수 있도록 할 수 있습니다. 개인 끝점은 Azure VNet 주소 공간의 IP 주소를 사용 합니다. 개인 네트워크의 클라이언트와 웹 앱 간의 네트워크 트래픽은 Vnet을 통해 이동 하 고 Microsoft 백본 네트워크의 개인 링크를 통해 공용 인터넷에서 노출 되는 것을 제거 합니다.

웹 앱에 대해 개인 끝점을 사용 하면 다음을 수행할 수 있습니다.

- 서비스 끝점을 구성 하 여 웹 앱을 보호 하 고 공개 노출을 제거 합니다.
- VPN 또는 Express 경로 개인 피어 링을 사용 하 여 Vnet에 연결 하는 온-프레미스 네트워크에서 웹 앱에 안전 하 게 연결 합니다.

Vnet과 웹 앱 사이에 보안 연결이 필요한 경우 서비스 끝점은 가장 간단한 솔루션입니다. Azure 게이트웨이를 통해 온-프레미스에서 웹 앱에 연결 해야 하는 경우 지역적으로 피어 링 Vnet 또는 전역적으로 피어 링 Vnet을 통해 개인 끝점은 솔루션입니다.  

[서비스 끝점][serviceendpoint] 에 대 한 자세한 내용

## <a name="conceptual-overview"></a>개념적 개요

개인 끝점은 Vnet (Virtual Network)의 서브넷에 있는 Azure 웹 앱에 대 한 특수 nic (네트워크 인터페이스)입니다.
웹 앱에 대 한 개인 끝점을 만드는 경우 개인 네트워크와 웹 앱의 클라이언트 간에 보안 연결을 제공 합니다. 개인 끝점에는 Vnet의 IP 주소 범위에서 IP 주소가 할당 됩니다.
개인 끝점과 웹 앱 간의 연결은 보안 [개인 링크][privatelink]를 사용 합니다. 개인 끝점은 웹 앱에 들어오는 흐름에만 사용 됩니다. 나가는 흐름은이 개인 끝점을 사용 하지 않지만 [Vnet 통합 기능][vnetintegrationfeature]을 통해 다른 서브넷의 네트워크에 나가는 흐름을 삽입할 수 있습니다.

개인 끝점을 연결 하는 서브넷에 다른 리소스가 있을 수 있으며, 전용 빈 서브넷이 필요 하지 않습니다.
웹 앱과 다른 지역에 개인 끝점을 배포할 수 있습니다. 

> [!Note]
>Vnet 통합 기능은 개인 끝점과 동일한 서브넷을 사용할 수 없습니다 .이는 Vnet 통합 기능에 대 한 제한 사항입니다.

보안 관점에서:

- 웹 앱에 대 한 서비스 끝점을 사용 하도록 설정 하면 모든 공용 액세스를 사용 하지 않도록 설정 됩니다.
- 다른 지역의 Vnet를 포함 하 여 다른 Vnet 및 서브넷에서 여러 개인 끝점을 사용 하도록 설정할 수 있습니다.
- 개인 끝점 NIC의 IP 주소는 동적 이어야 하지만 개인 끝점을 삭제할 때까지 동일 하 게 유지 됩니다.
- 개인 끝점의 NIC에는 연결 된 NSG가 있을 수 없습니다.
- 개인 끝점을 호스트 하는 서브넷에는 연결 된 NSG가 있을 수 있지만 개인 끝점에 대 한 네트워크 정책 적용을 사용 하지 않도록 설정 해야 합니다. [이 문서][disablesecuritype]를 참조 하세요. 결과적으로 개인 끝점에 액세스 하는 NSG를 기준으로 필터링 할 수 없습니다.
- 웹 앱에 대 한 개인 끝점을 사용 하도록 설정 하면 웹 앱의 [액세스 제한][accessrestrictions] 구성이 평가 되지 않습니다.
- Destination이 인터넷 또는 Azure 서비스 인 모든 nsg 규칙을 제거 하 여 vnet에서 데이터 반출 위험을 줄일 수 있습니다. 그러나 서브넷에 웹 App Service 끝점을 추가 하면 동일한 스탬프에서 호스트 되 고 인터넷에 노출 되는 모든 웹 앱에 연결할 수 있습니다.

웹 앱에 대 한 개인 끝점은 계층 PremiumV2 사용할 수 있으며 외부 ASE와 격리 됩니다.

웹 앱의 웹 http 로그에서 클라이언트 원본 IP를 찾을 수 있습니다. 웹 앱에 클라이언트 IP 속성을 전달 하는 TCP 프록시 프로토콜을 구현 했습니다. 자세한 내용은 [이 문서][tcpproxy](영문)를 읽어보세요.

![전역 개요][1]


## <a name="dns"></a>DNS

이 기능이 미리 보기 상태 이므로 미리 보기 중에 DNS 항목을 변경 하지 않습니다. 개인 DNS 서버 또는 Azure DNS 개인 영역에서 DNS 항목을 직접 관리 해야 합니다. 사용자 지정 DNS 이름을 사용 해야 하는 경우 웹 앱에 사용자 지정 이름을 추가 해야 합니다. 미리 보기 중에 사용자 지정 이름은 공용 DNS 확인을 사용 하 여 사용자 지정 이름과 같이 유효성을 검사 해야 합니다. [사용자 지정 DNS 유효성 검사 기술 참조][dnsvalidation]

## <a name="pricing"></a>가격

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정][pricing]을 참조하세요.

## <a name="limitations"></a>제한 사항

개인 링크 기능 및 개인 끝점을 정기적으로 개선 하 고 있습니다. 제한에 대 한 최신 정보는 [이 문서를 참조][pllimitations] 하세요.

## <a name="next-steps"></a>다음 단계

포털을 통해 웹 앱에 대 한 개인 끝점을 배포 하려면 [웹 앱에 비공개로 연결 하는 방법][howtoguide] 을 참조 하세요.


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

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
