---
title: Azure 프라이빗 엔드포인트를 사용하여 비공개로 웹앱에 연결
description: Azure 프라이빗 엔드포인트를 사용하여 비공개로 웹앱에 연결
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846956"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Azure 웹앱용 프라이빗 엔드포인트 사용(미리 보기)

> [!Note]
> Microsoft에서는 미리 보기 새로 고침으로 데이터 반출 방지 기능을 출시했습니다.
> 이 미리 보기는 미국 동부 및 미국 서부 2 지역에서 모든 PremiumV2 Windows 및 Linux 웹앱과 탄력적 프리미엄 함수에 사용할 수 있습니다. 

Azure 웹앱용 프라이빗 엔드포인트를 사용하여 프라이빗 네트워크에 있는 클라이언트가 Private Link를 통해 앱에 안전하게 액세스할 수 있도록 할 수 있습니다. 프라이빗 엔드포인트는 Azure VNet 주소 공간의 IP 주소를 사용합니다. 프라이빗 네트워크에 있는 클라이언트와 웹앱 간의 네트워크 트래픽은 VNet을 통해 이동하고 Microsoft 백본 네트워크의 Private Link를 통해 공용 인터넷 노출을 방지합니다.

웹앱용 프라이빗 엔드포인트를 사용하면 다음을 수행할 수 있습니다.

- 프라이빗 엔드포인트를 구성하여 웹앱을 보호함으로써 공개 노출을 방지합니다.
- VPN 또는 ExpressRoute 프라이빗 피어링을 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 웹앱에 안전하게 연결합니다.
- VNet에서 데이터 반출을 방지합니다. 

VNet과 웹앱 사이에 보안 연결이 필요한 경우 서비스 엔드포인트가 가장 간단한 솔루션입니다. Azure 게이트웨이, 지역 피어링 VNet 또는 전역 피어링 VNet을 통해 온-프레미스에서 웹앱에 연결해야 하는 경우 프라이빗 엔드포인트가 그 솔루션입니다.  

자세한 내용은 [서비스 엔드포인트][serviceendpoint]를 참조하세요.

## <a name="conceptual-overview"></a>개념적 개요

프라이빗 엔드포인트는 VNet(가상 네트워크)의 서브넷에 있는 Azure 웹앱을 위한 특수 NIC(네트워크 인터페이스)입니다.
웹앱용 프라이빗 엔드포인트를 만들면 프라이빗 네트워크의 클라이언트와 웹앱 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다.
프라이빗 엔드포인트와 웹앱 간의 연결은 보안 [Private Link][privatelink]를 사용합니다. 프라이빗 엔드포인트는 웹앱으로 들어오는 흐름에만 사용됩니다. 나가는 흐름은 이 프라이빗 엔드포인트를 사용하지 않지만 [VNet 통합 기능][vnetintegrationfeature]을 통해 다른 서브넷의 네트워크에 나가는 흐름을 주입할 수 있습니다.

프라이빗 엔드포인트를 연결하는 서브넷에 다른 리소스가 있을 수 있고 빈 전용 서브넷이 필요하지 않습니다.
웹앱과는 다른 지역에 프라이빗 엔드포인트를 배포할 수도 있습니다. 

> [!Note]
>VNet 통합 기능은 프라이빗 엔드포인트와 동일한 서브넷을 사용할 수 없습니다. 이는 VNet 통합 기능의 제한 사항입니다.

보안 관점에서

- 웹앱용 프라이빗 엔드포인트를 사용하도록 설정하면 모든 공용 액세스가 사용 안 함으로 설정됩니다.
- 다른 지역의 VNet을 포함하여 다른 VNet 및 서브넷에서 여러 프라이빗 엔드포인트를 사용하도록 설정할 수 있습니다.
- 프라이빗 엔드포인트 NIC의 IP 주소는 동적이어야 하지만 프라이빗 엔드포인트를 삭제할 때까지 동일하게 유지됩니다.
- 프라이빗 엔드포인트의 NIC에는 연결된 NSG가 있을 수 없습니다.
- 프라이빗 엔드포인트를 호스트하는 서브넷에는 연결된 NSG가 있을 수 있지만 프라이빗 엔드포인트에 대한 네트워크 정책 적용을 해제해야 합니다. [프라이빗 엔드포인트에 대한 네트워크 정책 사용 안 함][disablesecuritype]을 참조하세요. 따라서 NSG를 기준으로 프라이빗 엔드포인트에 대한 액세스를 필터링할 수 없습니다.
- 웹앱용 프라이빗 엔드포인트를 사용하도록 설정하면 웹앱의 [액세스 제한][accessrestrictions] 구성이 평가되지 않습니다.
- 대상이 태그 인터넷 또는 Azure 서비스인 모든 NSG 규칙을 제거하여 VNet에서 데이터 반출 위험을 제거할 수 있습니다. 웹앱용 프라이빗 엔드포인트를 배포하는 경우 프라이빗 엔드포인트를 통해서만 이 특정 웹앱에 연결할 수 있습니다. 다른 웹앱이 있는 경우 이 다른 웹앱에 대한 다른 전용 프라이빗 엔드포인트를 배포해야 합니다.

웹앱의 웹 HTTP 로그에서 클라이언트 원본 IP를 찾을 수 있습니다. 이는 클라이언트 IP 속성을 웹앱까지 전달하는 TCP 프록시 프로토콜을 사용하여 구현됩니다. 자세한 내용은 [TCP 프록시 v2를 사용하여 연결 정보 가져오기][tcpproxy]를 참조하세요.


  > [!div class="mx-imgBorder"]
  > ![웹앱 프라이빗 엔드포인트 전체 개요](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

이 기능은 미리 보기 상태이므로 Microsoft에서는 미리 보기 중에 DNS 항목을 변경하지 않습니다. 프라이빗 DNS 서버 또는 Azure DNS 프라이빗 영역에서 DNS 항목을 직접 관리해야 합니다.
사용자 지정 DNS 이름을 사용해야 하는 경우 웹앱에 사용자 지정 이름을 추가해야 합니다. 미리 보기 중에 사용자 지정 이름은 모든 사용자 지정 이름과 마찬가지로 퍼블릭 DNS 확인을 사용하여 유효성을 검사해야 합니다. 자세한 내용은 [사용자 지정 DNS 유효성 검사][dnsvalidation]를 참조하세요.

Kudu 콘솔 또는 Kudu REST API(예: Azure DevOps 자체 호스팅 에이전트로 배포)를 사용해야 하는 경우 Azure DNS 프라이빗 영역 또는 사용자 지정 DNS 서버에서 두 개의 레코드를 만들어야 합니다. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정][pricing]을 참조하세요.

## <a name="limitations"></a>제한 사항

탄력적 프리미엄 요금제에서 Azure 함수를 프라이빗 엔드포인트와 함께 사용하는 경우 Azure 웹 포털에서 함수를 실행하려면 직접 네트워크에 액세스할 수 있어야 합니다. 그렇지 않으면 HTTP 403 오류가 발생합니다. 즉, 브라우저가 Azure 웹 포털에서 함수를 실행하기 위해 프라이빗 엔드포인트에 연결할 수 있어야 합니다. 

미리 보기 중에는 프로덕션 슬롯만 프라이빗 엔드포인트 뒤에 표시되며, 다른 슬롯은 퍼블릭 엔드포인트로 연결되어야 합니다.

Private Link 기능 및 프라이빗 엔드포인트는 정기적으로 개선되고 있습니다. 제한 사항에 대한 최신 정보는 [이 문서][pllimitations]를 확인하세요.

## <a name="next-steps"></a>다음 단계

포털을 통해 웹앱용 프라이빗 엔드포인트를 배포하려면 [웹앱에 비공개로 연결하는 방법][howtoguide]을 참조하세요.




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
