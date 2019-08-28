---
title: 공용 피어링을 Microsoft 피어링으로 이동 - Azure ExpressRoute | Microsoft Docs
description: 이 문서에서는 ExpressRoute에서 공용 피어링을 Microsoft 피어링으로 이동하는 단계를 보여 줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592112"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>공용 피어링을 Microsoft 피어링으로 이동

이 문서에서는 가동 중지 없이 공용 피어링 구성을 Microsoft 피어링으로 이동할 수 있습니다. ExpressRoute는 Azure 스토리지 및 Azure SQL Database와 같은 Azure PaaS 서비스에 대한 경로 필터를 사용하여 Microsoft 피어링 사용을 지원합니다. Microsoft PaaS 및 SaaS 서비스에 액세스하려면 하나의 라우팅 도메인만 있으면 됩니다. 경로 필터를 사용하여 사용하려는 Azure 지역에 대한 PaaS 서비스 접두사를 선택적으로 보급할 수 있습니다.

Azure 공용 피어 링에 각 BGP 세션에 연결 된 NAT IP 주소 1 Microsoft 피어 링 선택적 접두사 보급 알림에 대 한 경로 필터를 사용 하 여 뿐만 아니라 고유한 NAT 할당을 구성할 수 있습니다. 공용 피어 링 되는 연결을 사용 하는 단방향 서비스는 항상 Microsoft Azure 서비스에 WAN에서 시작 됩니다. Microsoft Azure 서비스가 라우팅 도메인을 통해 네트워크로의 연결을 시작할 수 없습니다.

공용 피어링을 사용하도록 설정하면 모든 Azure 서비스에 연결할 수 있습니다. Microsoft에서 경로를 보급하는 서비스는 사용자가 선택할 수 없습니다. Microsoft 피어 링에 WAN와 함께 Microsoft Azure 서비스에서 연결을 시작할 수 있는 양방향 연결을가 합니다. 라우팅 도메인 및 피어 링에 대 한 자세한 내용은 참조 하세요. [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)합니다.

## <a name="before"></a>시작하기 전에

Microsoft 피어링에 연결하려면 NAT를 설정하고 관리해야 합니다. 연결 공급자에서 NAT를 관리 서비스로 설정하고 관리할 수 있습니다. Microsoft 피어링에서 Azure PaaS 및 Azure SaaS 서비스에 액세스하려는 경우 NAT IP 풀의 크기를 올바르게 조정해야 합니다. ExpressRoute용 NAT에 대한 자세한 내용은 [Microsoft 피어링에 대한 NAT 요구 사항](expressroute-nat.md#nat-requirements-for-microsoft-peering)을 참조하세요. Azure ExpressRoute(Microsoft peering)를 통해 Microsoft에 연결할 때 Microsoft에는 여러 링크가 있습니다. 한 가지 링크는 기존 인터넷 연결이고 다른 하나는 ExpressRoute를 통한 연결입니다. Microsoft에 대한 일부 트래픽은 인터넷을 통과할 수 있지만 ExpressRoute를 통해 돌아옵니다. 혹은 그 반대입니다.

![양방향 연결](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Microsoft에 보급된 NAT IP 풀은 인터넷에 보급되지 않아야 합니다. 다른 Microsoft 서비스에 대한 연결을 중단합니다.

가리킵니다 [여러 네트워크 경로 사용 하 여 비대칭 라우팅](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) Microsoft 피어 링을 구성 하기 전에 비대칭 라우팅의 제한 사항에 대 한 합니다.

* 공용 피어링을 사용하고 있고 현재 [Azure Storage](../storage/common/storage-network-security.md)나 [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)에 액세스하는 데 사용되는 공용 IP 주소에 대한 IP 네트워크 규칙이 있는 경우, Microsoft 피어링으로 구성된 NAT IP 풀이 Azure 스토리지 계정 또는 Azure SQL 계정의 공용 IP 주소 목록에 포함되어 있는지 확인해야 합니다.<br>
* 가동 중지 없이 Microsoft 피어링으로 이동하려면 이 문서의 단계를 제시된 순서대로 사용하십시오.

## <a name="create"></a>1. Microsoft 피어링 만들기

Microsoft 피어링을 만들지 않은 경우 다음 문서 중 하나를 사용하여 Microsoft 피어링을 만듭니다. 연결 공급자에서 3계층 관리 서비스를 제공하는 경우 회로에 대해 Microsoft 피어링을 사용하도록 연결 공급자에 요청할 수 있습니다.

사용자가 계층 3 관리 되는 경우 계속 진행 하기 전에 다음 정보가 필요 합니다.

* 기본 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.<br>
* 보조 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.<br>
* 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.<br>
* 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.<br>
* 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사 목록을 제공해야 합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.<br>
* 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/IRR을 지정할 수 있습니다.

* **선택적** -고객 ASN: 피어링 AS 숫자에 등록되지 않은 광고 접두사인 경우 등록된 AS 번호를 지정할 수 있습니다.<br>
* **선택적** -하나를 사용 하기로 선택한 경우 MD5 해시입니다.

Microsoft 피어 링을 사용 하는 자세한 지침은 다음 문서에서 찾을 수 있습니다.

* [Azure Portal을 사용하여 Microsoft 피어링 만들기](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Azure Powershell을 사용하여 Microsoft 피어링 만들기](expressroute-howto-routing-arm.md#msft)<br>
* [Azure CLI를 사용하여 Microsoft 피어링 만들기](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Microsoft 피어링 사용에 대한 유효성 검사

Microsoft 피어링을 사용하도록 설정되고 보급된 공용 접두사가 구성되어 있는지 확인합니다.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. 회로에 경로 필터 구성 및 연결

기본적으로 새 Microsoft 피어 링 보급 하지 않습니다 모든 접두사 경로 필터를 회로에 연결 될 때까지. 경로 필터 규칙을 만들면 Azure PaaS 서비스에 대 한 사용 하려는 Azure 지역에 대 한 서비스 커뮤니티의 목록을 지정할 수 있습니다. 이 유연 하 게 사용자 요구 사항에 따라 경로 필터링 하려면 다음 스크린샷에 표시 된 대로:

![공용 피어링 병합](./media/how-to-move-peering/routefilter.jpg)

다음 문서 중 하나를 사용하여 경로 필터를 구성합니다.

* [Azure Portal을 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-portal.md)<br>
* [Azure PowerShell을 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-powershell.md)<br>
* [Azure CLI를 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-cli.md)

## <a name="delete"></a>4. 공용 피어링 삭제

Microsoft 피어링이 구성되어 있고 사용하려는 접두사가 Microsoft 피어링에서 올바르게 보급되었는지 확인한 후에는 공용 피어링을 삭제할 수 있습니다. 공용 피어링을 삭제하려면 다음 문서 중 하나를 사용합니다.

* [Azure Portal을 사용하여 Azure 공용 피어링 삭제](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Azure PowerShell을 사용하여 Azure 공용 피어링 삭제](expressroute-howto-routing-arm.md#deletepublic)<br>
* [CLI를 사용하여 Azure 공용 피어링 삭제](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. 피어링 보기
  
Azure Portal에서 모든 ExpressRoute 회로 및 피어링 목록을 확인할 수 있습니다. 자세한 내용은 [Microsoft 피어링 세부 정보 보기](expressroute-howto-routing-portal-resource-manager.md#getmsft)를 참조하세요.

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
