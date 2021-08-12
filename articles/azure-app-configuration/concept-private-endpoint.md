---
title: Azure App Configuration의 프라이빗 엔드포인트 사용
description: 프라이빗 엔드포인트를 사용하여 App Configuration 저장소 보호
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: ded1007cd5d0268d68bcdbff87a3b703da247876
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108764048"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Azure App Configuration의 프라이빗 엔드포인트 사용

Azure App Configuration에 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하면 VNet(가상 네트워크)의 클라이언트가 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 안전하게 데이터에 액세스할 수 있습니다. 프라이빗 엔드포인트는 App Configuration 저장소용 VNet 주소 공간의 IP 주소를 사용합니다. VNet에 있는 클라이언트와 App Configuration 저장소 사이의 네트워크 트래픽은 Microsoft 백본 네트워크의 프라이빗 링크를 사용하여 VNet을 통과하기 때문에 공용 인터넷에 노출될 필요가 없습니다.

App Configuration 저장소에 프라이빗 엔드포인트를 사용하면 다음이 가능합니다.
- 퍼블릭 엔드포인트에서 App Configuration에 대한 모든 연결을 차단하도록 방화벽을 구성하여 애플리케이션 구성 세부 정보를 보호합니다.
- VNet(가상 네트워크)에 대한 보안을 강화하여 데이터가 VNet에서 이스케이프되지 않도록 합니다.
- 개인 피어링을 통해 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes](../expressroute/expressroute-locations.md)를 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 App Configuration 저장소에 안전하게 연결합니다.

## <a name="conceptual-overview"></a>개념적 개요

프라이빗 엔드포인트는 VNet([Virtual Network](../virtual-network/virtual-networks-overview.md))의 Azure 서비스에 대한 특별 네트워크 인터페이스입니다. App Configuration 저장소에 대한 프라이빗 엔드포인트를 만들 때 VNet의 클라이언트와 구성 저장소 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위 내에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 구성 저장소 간의 연결은 보안 프라이빗 링크를 사용합니다.

VNet의 애플리케이션은 **다른 방법으로 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여** 프라이빗 엔드포인트를 통해 구성 저장소에 연결할 수 있습니다. 프라이빗 엔드포인트는 App Configuration 저장소에서 지원하는 모든 프로토콜과 함께 사용할 수 있습니다.

App Configuration은 서비스 엔드포인트를 지원하지 않지만 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하는 서브넷에 프라이빗 엔드포인트를 만들 수 있습니다. 서브넷의 클라이언트는 프라이빗 엔드포인트를 사용하여 App Configuration 저장소에 안전하게 연결할 수 있으며 서비스 엔드포인트를 사용하여 다른 곳에 액세스할 수 있습니다.  

VNet에서 서비스에 대한 프라이빗 엔드포인트를 만들면 서비스 계정 소유자에게 승인을 위해 동의 요청이 전송됩니다. 프라이빗 엔드포인트 만들기를 요청한 사용자가 계정의 소유자인 경우 이 동의 요청이 자동으로 승인됩니다.

서비스 계정 소유자는 [Azure Portal](https://portal.azure.com)에서 App Configuration 저장소의 `Private Endpoints` 탭을 통해 동의 요청 및 프라이빗 엔드포인트를 관리할 수 있습니다.

### <a name="private-endpoints-for-app-configuration"></a>App Configuration의 프라이빗 엔드포인트 

프라이빗 엔드포인트를 만들 때 연결할 App Configuration 저장소를 지정해야 합니다. App Configuration 저장소가 여러 개 있으면 각 저장소에 대해 별도의 프라이빗 엔드포인트가 필요합니다.

### <a name="connecting-to-private-endpoints"></a>프라이빗 엔드포인트에 연결

Azure는 DNS 확인을 사용하여 프라이빗 링크를 통해 VNet에서 구성 저장소로 연결을 라우팅합니다. App Configuration 저장소를 선택한 다음, **설정**  > **액세스 키** 를 선택하여 Azure Portal에서 연결 문자열을 빠르게 찾을 수 있습니다.  

> [!IMPORTANT]
> 퍼블릭 엔드포인트에 사용하는 것처럼 프라이빗 엔드포인트를 사용하여 App Configuration 저장소에 연결하려면 동일한 연결 문자열을 사용합니다. `privatelink` 하위 도메인 URL을 사용하여 저장소에 연결하지 마십시오.

> [!NOTE]
> 기본적으로 프라이빗 엔드포인트가 App Configuration 저장소에 추가되면 공용 네트워크를 통한 App Configuration 데이터에 대한 모든 요청이 거부됩니다. 공용 네트워크 액세스는 다음 Azure CLI 명령을 사용하여 활성화할 수 있습니다. 이 시나리오에서는 공용 네트워크 액세스를 활성화하는 보안 관련 사항을 고려해야 합니다.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트용 DNS 변경

프라이빗 엔드포인트를 생성하면 구성 저장소에 대한 DNS CNAME 리소스 레코드는 `privatelink` 접두사가 있는 하위 도메인의 별칭으로 업데이트됩니다. Azure는 또한 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 `privatelink` 하위 도메인에 해당하는 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다.

프라이빗 엔드포인트를 호스트하는 VNet 내에서 엔드포인트 URL을 확인하면 저장소의 프라이빗 엔드포인트로 확인됩니다. VNet 외부에서 확인되는 경우 엔드포인트 URL은 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 만들면 퍼블릭 엔드포인트는 사용하지 않도록 설정됩니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 서비스 엔드포인트의 FQDN(정규화된 도메인 이름)을 프라이빗 엔드포인트 IP 주소로 확인할 수 있어야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 `[Your-store-name].privatelink.azconfig.io`에 대한 A 레코드를 구성합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 `privatelink` 하위 도메인의 저장소 이름을 프라이빗 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. `privatelink` 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하여 이를 수행할 수 있습니다.

## <a name="pricing"></a>가격 책정

프라이빗 엔드포인트를 사용하도록 설정하려면 [표준 계층](https://azure.microsoft.com/pricing/details/app-configuration/) App Configuration 저장소가 필요합니다.  프라이빗 링크 가격 책정 세부 정보에 대해 알아보려면 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="next-steps"></a>다음 단계

App Configuration 저장소용 프라이빗 엔드포인트를 만드는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Portal에서 Private Link 센터를 사용하여 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-portal.md)
- [Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-powershell.md)

프라이빗 엔드포인트로 DNS 서버를 구성하는 방법을 알아보세요.

- [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)
