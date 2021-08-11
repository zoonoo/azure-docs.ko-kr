---
title: Virtual WAN에서 프라이빗 링크 서비스 공유
titleSuffix: Azure Virtual WAN
description: Virtual WAN에서 Private Link를 구성하는 단계
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913758"
---
# <a name="use-private-link-in-virtual-wan"></a>Virtual WAN에서 Private Link 사용

[Azure Private Link](../private-link/private-link-overview.md)는 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 공개하여 개인 IP 주소 연결을 통해 Azure Platform-as-a-Service 제품을 연결할 수 있는 기술입니다. Azure Virtual WAN을 사용하면 가상 허브에 연결된 가상 네트워크 중 하나에 프라이빗 엔드포인트를 배포할 수 있습니다. 이렇게 하면 동일한 Virtual WAN에 연결된 다른 가상 네트워크나 분기에 대한 연결이 제공됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계에서는 하나 이상의 허브가 있는 Virtual WAN과 Virtual WAN에 연결된 두 개 이상의 가상 네트워크를 이미 배포했다고 가정합니다.

새 Virtual WAN 및 새 허브를 만들려면 다음 문서의 단계를 수행합니다.

* [Virtual WAN 만들기](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 만들기](virtual-wan-site-to-site-portal.md#hub)
* [허브에 VNet 연결](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>프라이빗 링크 엔드포인트 만들기

다양한 서비스의 프라이빗 링크 엔드포인트를 만들 수 있습니다. 이 예제에서는 Azure SQL Database를 사용합니다. [빠른 시작: Azure Portal을 사용하여 프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-portal.md)에서 Azure SQL Database의 프라이빗 엔드포인트를 만드는 방법에 관한 자세한 내용을 확인할 수 있습니다. 다음 이미지는 Azure SQL Database의 네트워크 구성을 보여 줍니다.

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="프라이빗 링크 만들기" lightbox="./media/howto-private-link/create-private-link.png":::

Azure SQL Database를 만든 후 프라이빗 엔드포인트를 검색하는 프라이빗 엔드포인트 IP 주소를 확인할 수 있습니다.

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="프라이빗 엔드포인트" lightbox="./media/howto-private-link/endpoints.png":::

만든 프라이빗 엔드포인트를 클릭하면 해당 개인 IP 주소와 FQDN(정규화된 도메인 이름)이 표시됩니다. 프라이빗 엔드포인트의 IP 주소는 프라이빗 엔드포인트가 배포된 VNet 범위에 있습니다(10.1.3.0/24).

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL 엔드포인트" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>동일한 VNet에서 연결 확인

이 예제에서는 MS SQL 도구가 설치된 Ubuntu 가상 머신에서 Azure SQL Database에 대한 연결을 확인합니다. 첫 번째 단계는 DNS 확인이 작동하고 Azure SQL Database 정규화된 도메인 이름이 프라이빗 엔드포인트가 배포된 동일한 VNet(10.1.3.0/24)의 개인 IP 주소로 확인되는지 확인하는 것입니다.

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

이전 출력에서 볼 수 있듯이 FQDN `wantest.database.windows.net`은 프라이빗 엔드포인트를 따라 생성된 프라이빗 DNS 영역이 개인 IP 주소 `10.1.3.228`로 확인되는 `wantest.privatelink.database.windows.net`에 매핑됩니다. 프라이빗 DNS 영역을 확인하여 개인 IP 주소에 매핑된 프라이빗 엔드포인트의 A 레코드가 있는지 확인합니다.

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS 영역" lightbox="./media/howto-private-link/dns-zone.png":::

올바른 DNS 확인을 확인한 후 데이터베이스에 연결해 볼 수 있습니다.

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

여기에서 볼 수 있듯이 SQL 서버가 클라이언트에서 확인하는 원본 IP 주소를 제공하는 특수 SQL 쿼리를 사용하고 있습니다. 이 경우 서버는 개인 IP(`10.1.3.75`)를 사용하는 클라이언트를 확인합니다. 즉, 트래픽이 VNet에서 직접 프라이빗 엔드포인트로 이동합니다.

이 가이드의 예제를 사용하려면 `username` 및 `password` 변수를 Azure SQL Database에 정의된 자격 증명과 일치하도록 설정해야 합니다.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>다른 VNet에서 연결

Azure Virtual WAN의 한 VNet이 프라이빗 엔드포인트에 연결되어 있으므로 이제 Virtual WAN에 연결된 다른 모든 VNet과 분기도 해당 엔드포인트에 액세스할 수 있습니다. 두 예제에 이름을 지정하려면 [Any-to-Any 시나리오](scenario-any-to-any.md) 또는 [Shared Services VNet 시나리오](scenario-shared-services-vnet.md)와 같이 Azure Virtual WAN에서 지원되는 임의 모델을 통해 연결을 제공해야 합니다.

VNet 또는 분기에서 프라이빗 엔드포인트가 배포된 VNet에 연결한 후 DNS 확인을 구성해야 합니다.

* VNet에서 프라이빗 엔드포인트에 연결하는 경우 Azure SQL Database를 사용하여 만든 것과 동일한 프라이빗 영역을 사용할 수 있습니다.
* 분기(사이트 간 VPN, 지점 및 사이트 간 VPN 또는 ExpressRoute)에서 프라이빗 엔드포인트에 연결하는 경우 온-프레미스 DNS 확인을 사용해야 합니다.

이 예제에서는 다른 VNet에서 연결하므로, 먼저 프라이빗 DNS 영역을 새 VNet에 연결하면 해당 워크로드는 Azure SQL Database 정규화된 도메인 이름을 개인 IP 주소로 확인할 수 있습니다. 이 작업을 수행하려면 프라이빗 DNS 영역을 새 VNet에 연결합니다.

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS 링크" lightbox="./media/howto-private-link/dns-link.png":::

이제 연결된 VNet의 모든 가상 머신이 Azure SQL Database FQDN을 프라이빗 링크의 개인 IP 주소로 올바르게 확인합니다.

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

이 VNet(10.1.1.0/24)이 프라이빗 엔드포인트가 구성된 원래 VNet(10.1.3.0/24)에 연결되어 있는지 다시 확인하기 위해 VNet의 가상 머신에서 유효 경로 테이블을 확인할 수 있습니다.

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="유효 경로" lightbox="./media/howto-private-link/effective-routes.png":::

여기서 볼 수 있듯이 Azure Virtual WAN에서 Virtual Network 게이트웨이에 의해 삽입된 VNet 10.1.3.0/24를 가리키는 경로가 있습니다. 이제 마지막으로 데이터베이스에 대한 연결을 테스트할 수 있습니다.

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

이 예제에서는 Virtual WAN에 연결된 VNet 중 하나에서 프라이빗 엔드포인트를 만들어 Virtual WAN의 나머지 VNet과 분기에 대한 연결을 제공하는 방법을 확인했습니다.

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
