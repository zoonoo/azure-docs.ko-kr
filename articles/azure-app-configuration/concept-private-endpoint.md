---
title: Azure 앱 구성에 개인 끝점 사용
description: 비공개 엔드포인트를 사용하여 앱 구성 스토어 보안
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366771"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Azure 앱 구성에 개인 끝점 사용

Azure 앱 구성에 대한 [개인 끝점을](../private-link/private-endpoint-overview.md) 사용하여 가상 네트워크(VNet)의 클라이언트가 [개인 링크를](../private-link/private-link-overview.md)통해 데이터에 안전하게 액세스할 수 있도록 허용할 수 있습니다. 개인 끝점은 앱 구성 저장소의 VNet 주소 공간의 IP 주소를 사용합니다. VNet과 앱 구성 저장소의 클라이언트 간의 네트워크 트래픽은 Microsoft 백본 네트워크의 개인 링크를 사용하여 VNet을 통과하므로 공용 인터넷에 노출되지 않습니다.

앱 구성 저장소에 개인 끝점을 사용하면 다음을 수행할 수 있습니다.
- 공용 끝점에서 앱 구성에 대한 모든 연결을 차단하도록 방화벽을 구성하여 응용 프로그램 구성 세부 정보를 보호합니다.
- VNet(가상 네트워크)에 대한 보안을 강화하여 데이터가 VNet에서 벗어나지 않도록 합니다.
- 프라이빗 피어링을 사용하는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes를](../expressroute/expressroute-locations.md) 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 앱 구성 저장소에 안전하게 연결합니다.

> [!NOTE]
> Azure 앱 구성은 공개 미리 보기로 개인 끝점을 사용합니다. 퍼블릭 미리 보기 제품을 통해 고객은 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.  퍼블릭 미리 보기 기능 및 서비스는 프로덕션 용도로 사용되지 않습니다.

## <a name="conceptual-overview"></a>개념적 개요

개인 끝점은 [가상](../virtual-network/virtual-networks-overview.md) 네트워크(VNet)의 Azure 서비스에 대한 특수 네트워크 인터페이스입니다. 앱 Config 저장소에 대한 개인 끝점을 만들 면 VNet의 클라이언트와 구성 저장소 간의 보안 연결을 제공합니다. 개인 끝점은 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다. 개인 끝점과 구성 저장소 간의 연결은 보안 개인 링크를 사용합니다.

VNet의 응용 프로그램은 **그렇지 않으면 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여**개인 끝점을 통해 구성 저장소에 연결할 수 있습니다. 개인 엔드포인트는 앱 구성 저장소에서 지원하는 모든 프로토콜과 함께 사용할 수 있습니다.

앱 구성은 서비스 끝점을 지원하지 않지만 [서비스](../virtual-network/virtual-network-service-endpoints-overview.md)끝점을 사용하는 서브넷에서 개인 끝점을 만들 수 있습니다. 서브넷의 클라이언트는 서비스 끝점을 사용하여 다른 사람에 액세스하는 동안 개인 끝점을 사용하여 앱 구성 저장소에 안전하게 연결할 수 있습니다.  

VNet에서 서비스에 대한 개인 엔드포인트를 만들면 서비스 계정 소유자에게 승인을 위해 동의 요청이 전송됩니다. 개인 끝점 만들기를 요청하는 사용자가 계정소유자이기도 한 경우 이 동의 요청이 자동으로 승인됩니다.

서비스 계정 소유자는 [Azure 포털의](https://portal.azure.com)구성 `Private Endpoints` 저장소 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

### <a name="private-endpoints-for-app-configuration"></a>앱 구성을 위한 비공개 끝점 

개인 끝점을 만들 때 연결 되는 앱 구성 저장소를 지정 해야 합니다. 계정 내에 앱 구성 인스턴스가 여러 개 있는 경우 각 스토어에 대해 별도의 개인 끝점이 필요합니다.

### <a name="connecting-to-private-endpoints"></a>프라이빗 엔드포인트에 연결

Azure는 DNS 확인을 사용하여 VNet에서 개인 링크를 통해 구성 저장소로 연결을 라우팅합니다. 앱 구성 저장소를 선택한 다음 **설정** > **액세스 키를**선택하여 Azure 포털에서 연결 문자열을 빠르게 찾을 수 있습니다.  

> [!IMPORTANT]
> 공용 끝점에 사용하는 것과 마찬가지로 개인 끝점을 사용하여 앱 구성 저장소에 연결하려면 동일한 연결 문자열을 사용합니다. `privatelink` 하위 도메인 URL을 사용하여 저장소 계정에 연결하지 마십시오.

## <a name="dns-changes-for-private-endpoints"></a>개인 끝점에 대한 DNS 변경

개인 끝점을 만들 면 구성 저장소에 대 한 DNS CNAME 리소스 레코드 접두사가 `privatelink`있는 하위 도메인의 별칭으로 업데이트 됩니다. 또한 Azure는 하위 도메인에 `privatelink` 해당하는 개인 [DNS 영역을](../dns/private-dns-overview.md) 만들고 개인 끝점에 대한 DNS A 리소스 레코드도 만듭니다.

VNet 외부에서 끝점 URL을 확인하면 저장소의 공용 끝점으로 확인됩니다. 개인 끝점을 호스팅하는 VNet 내에서 해결되면 끝점 URL이 개인 끝점으로 확인됩니다.

Azure 방화벽 서비스를 사용하여 공용 끝점을 통해 VNet 외부의 클라이언트에 대한 액세스를 제어할 수 있습니다.

이 방법을 사용하면 VNet 외부의 클라이언트뿐만 아니라 개인 엔드포인트를 호스팅하는 VNet의 클라이언트에 대해 **동일한 연결 문자열을 사용하여** 저장소에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 서비스 끝점에 대해 개인 엔드포인트 IP 주소로 정규화된 도메인 이름(FQDN)을 확인할 수 있어야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임하도록 DNS 서버를 `AppConfigInstanceA.privatelink.azconfig.io` 구성하거나 개인 엔드포인트 IP 주소로 A 레코드를 구성합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 `privatelink` 하위 도메인의 저장소 이름을 개인 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. 하위 도메인을 `privatelink` VNet의 개인 DNS 영역에 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하여 이 작업을 수행할 수 있습니다.

## <a name="pricing"></a>가격 책정

개인 끝점을 사용하도록 설정하려면 [표준 계층](https://azure.microsoft.com/pricing/details/app-configuration/) 앱 구성 저장소가 필요합니다.  개인 링크 가격 세부 정보에 대한 자세한 내용은 [Azure 개인 링크 가격 책정을](https://azure.microsoft.com/pricing/details/private-link)참조하십시오.

## <a name="next-steps"></a>다음 단계

앱 구성 저장소에 대한 개인 끝점을 만드는 방법에 대해 자세히 알아보려면 다음 문서를 참조하십시오.

- [Azure 포털의 개인 링크 센터를 사용하여 개인 끝점 만들기](../private-link/create-private-endpoint-portal.md)
- [Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell을 사용하여 개인 끝점 만들기](../private-link/create-private-endpoint-powershell.md)

개인 끝점으로 DNS 서버를 구성하는 방법을 알아봅니다.

- [Azure 가상 네트워크에서 리소스에 대한 이름 확인](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트용 DNS 구성](/azure/private-link/private-endpoint-overview#dns-configuration)
