---
title: "Azure SQL Database에 대한 가상 네트워크 서비스 끝점 및 규칙 | Microsoft Docs"
description: "서브넷을 Virtual Network 서비스 끝점으로 표시합니다. 그런 다음 해당 끝점을 가상 네트워크 규칙으로 Azure SQL Database의 ACL에 추가합니다. 그러면 SQL Database가 해당 서브넷에 있는 모든 가상 컴퓨터와 다른 노드에서 보낸 통신을 수락합니다."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/30/2017
ms.author: genemi
ms.openlocfilehash: 69059b6169e1fac4d0abea2770b0de502f816113
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Azure SQL Database에 대한 Virtual Network 서비스 끝점 및 규칙 사용

*가상 네트워크 규칙*은 Azure SQL Database 서버가 가상 네트워크의 특정 서브넷에서 보낸 통신을 수락할지 여부를 제어하는 하나의 방화벽 보안 기능입니다. 이 문서에서는 경우에 따라 가상 네트워크 규칙 기능이 Azure SQL Database에 대한 통신을 안전하게 허용하기 위한 가장 좋은 옵션인 이유를 설명합니다.

가상 네트워크 규칙을 만들려면 먼저 참조할 규칙에 대한 [가상 네트워크 서비스 끝점][vm-virtual-network-service-endpoints-overview-649d]이 있어야 합니다.


> [!NOTE]
> Azure SQL Database의 경우 이 기능은 다음과 같은 Azure 지역에서 미리 보기로 제공됩니다.
>
> - WestCentralUS, WestUS2 및 EastUS.


#### <a name="how-to-create-a-virtual-network-rule"></a>가상 네트워크 규칙을 만드는 방법

가상 네트워크 규칙을 만들기만 할 경우 [이 문서의 뒷부분](#anchor-how-to-by-using-firewall-portal-59j)에 있는 단계와 설명으로 바로 건너뛸 수 있습니다.






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>용어 및 설명

**가상 네트워크:** Azure 구독과 연결된 가상 네트워크가 있을 수 있습니다.

**서브넷:** 가상 네트워크에 **서브넷**이 포함됩니다. 소유한 Azure VM(가상 컴퓨터)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 계산 노드가 포함될 수 있습니다. 액세스를 허용하도록 보안을 구성해야 가상 네트워크 외부의 계산 노드가 가상 네트워크에 액세스할 수 있습니다.

**Virtual Network 서비스 끝점:** [Virtual Network 서비스 끝점][vm-virtual-network-service-endpoints-overview-649d]은 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. 이 문서에서는 SQL Database라는 Azure 서비스를 나타내는 **Microsoft.Sql**의 형식 이름을 살펴봅니다.

**가상 네트워크 규칙:** SQL Database 서버에 대한 가상 네트워크 규칙은 SQL Database 서버의 ACL(액세스 제어 목록)에 나열된 서브넷입니다. SQL Database에 대한 ACL에 나열되려면 서브넷에 **Microsoft.Sql** 형식 이름이 있어야 합니다.

가상 네트워크 규칙은 서브넷에 있는 모든 노드에서 보낸 통신을 수락하도록 SQL Database 서버에 지시합니다.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>가상 네트워크 규칙의 이점

작업을 수행할 때까지 서브넷의 VM은 SQL Database와 통신할 수 없습니다. 통신 허용에 대한 가상 네트워크 규칙 방법을 선택하는 원리에는 방화벽에서 제공된 경쟁 보안 옵션에 관련된 비교-대비 토론이 필요합니다.

#### <a name="a-allow-access-to-azure-services"></a>A. Azure 서비스에 대한 액세스 허용

방화벽 창에는 **Azure 서비스에 대한 액세스 허용**이라고 표시된 **켜기/끄기** 단추가 있습니다. **켜기** 설정은 모든 Azure IP 주소와 모든 Azure 서브넷에서 보낸 통신을 허용합니다. 이러한 Azure IP 또는 서브넷은 사용자가 소유할 수 없습니다. 이 **켜기** 설정은 SQL Database에 필요한 것보다 더 개방적일 수 있습니다. 가상 네트워크 규칙 기능으로 훨씬 더 세밀하게 제어할 수 있습니다.

#### <a name="b-ip-rules"></a>B. IP 규칙

SQL Database 방화벽을 사용하여 SQL Database에 대한 통신이 수락되는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 개인 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 하지만 Azure 개인 네트워크 내부의 많은 노드는 *동적* IP 주소로 구성됩니다. 동적 IP 주소는, 예를 들어 VM이 다시 시작될 때 변경될 수 있습니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대한 *정적* IP 주소를 가져와서 IP 옵션을 복원할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 가상 컴퓨터에 대한 개인 IP 주소 구성][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]을 참조하세요.

그러나 정적 IP 방법은 관리가 어려워질 수 있고 대규모로 이루어질 경우 비용이 많이 듭니다. 가상 네트워크 규칙은 설정 및 관리가 더 쉽습니다.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. 아직 서브넷에 SQL Database가 있을 수 없음

Azure SQL Database 서버가 가상 네트워크에 있는 서브넷의 노드인 경우 가상 네트워크 내의 모든 노드가 SQL Database와 통신할 수 있습니다. 이 경우 VM은 가상 네트워크 규칙이나 IP 규칙이 없어도 SQL Database와 통신할 수 있습니다.

그러나 2017년 9월 현재, Azure SQL Database 서비스는 아직 서브넷에 할당될 수 없습니다.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>가상 네트워크 규칙에 대한 세부 정보

이 섹션에서는 가상 네트워크 규칙에 대한 여러 가지 세부 정보를 설명합니다.

#### <a name="only-one-geographic-region"></a>단 하나의 지리적 지역

각 Virtual Network 서비스 끝점은 하나의 Azure 지역에만 적용됩니다. 끝점을 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다.

가상 네트워크 규칙은 기본 끝점이 적용되는 지역으로 제한됩니다.

#### <a name="server-level-not-database-level"></a>데이터베이스 수준이 아님, 서버 수준

각 가상 네트워크 규칙은 서버에 있는 하나의 특정 데이터베이스가 아니라 전체 Azure SQL Database 서버에 적용됩니다. 즉, 가상 네트워크 규칙은 데이터베이스 수준이 아니라 서버 수준에서 적용됩니다.

- 이와 달리 IP 규칙은 각 수준에서 적용될 수 있습니다.

#### <a name="security-administration-roles"></a>보안 관리 역할

Virtual Network 서비스 끝점 관리에는 보안 역할 분리가 있습니다. 다음과 같은 각 역할의 작업이 필요합니다.

- **네트워크 관리자:** &nbsp; 끝점을 켭니다.
- **데이터베이스 관리자:** &nbsp; ACL(액세스 제어 목록)을 업데이트하여 제공된 서브넷을 SQL Database 서버에 추가합니다.

*RBAC 대체:* 

네트워크 관리자 및 데이터베이스 관리자 역할에는 가상 네트워크 규칙을 관리하는 데 필요한 것보다 많은 기능이 포함됩니다. 해당 기능의 하위 집합만 필요합니다.

Azure에서 [RBAC(역할 기반 액세스 제어)][rbac-what-is-813s]를 사용하여 기능의 필요한 하위 집합만 포함된 단일 사용자 지정 역할을 만들 수도 있습니다. 네트워크 관리자 또는 데이터베이스 관리자를 포함하는 대신 사용자 지정 역할을 사용할 수 있습니다. 사용자 지정 역할에 사용자를 추가할 경우 다른 두 개의 주요 관리자 역할에 사용자를 추가하는 것보다 보안 노출의 노출 영역이 감소합니다.






## <a name="limitations"></a>제한 사항

Azure SQL Database의 경우 가상 네트워크 규칙 기능에는 다음과 같은 제한이 있습니다.

- 현재 **서비스 끝점**이 켜져 있는 서브넷에서 Azure 웹앱이 아직 예상대로 작동하지 않습니다. 이 기능을 곧 사용할 수 있도록 개발하는 중입니다.
    - 이 기능이 완전히 구현될 때까지 웹앱을 SQL용 서비스 끝점이 켜져 있지 않은 다른 서브넷으로 이동하는 것이 좋습니다.

- SQL Database에 대한 방화벽에서 각 가상 네트워크 규칙은 서브넷을 참조합니다. 이렇게 참조된 모든 서브넷은 SQL Database와 동일한 지리적 위치에서 호스팅되어야 합니다.

- 각 Azure SQL Database 서버에는 특정 가상 네트워크에 대해 최대 128개 ACL 항목이 포함될 수 있습니다.

- 가상 네트워크 규칙은 Azure Resource Manager 가상 네트워크에만 적용되고 [클래식 배포 모델][arm-deployment-model-568f] 네트워크에는 적용되지 않습니다.

- 방화벽에서 IP 주소 범위는 다음 네트워킹 항목에 적용되지만 가상 네트워크 규칙에는 적용되지 않습니다.
    - [S2S(사이트 간) VPN(가상 사설망)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v]를 통한 온-프레미스

#### <a name="expressroute"></a>ExpressRoute

네트워크가 [ExpressRoute][expressroute-indexmd-744v]의 사용을 통해 Azure 네트워크에 연결된 경우 각 회로는 Microsoft Edge에서 두 개의 공용 IP 주소로 구성됩니다. 두 개의 IP 주소는 Azure 공용 피어링을 사용하여 Azure Storage와 같은 Microsoft 서비스에 연결하는 데 사용됩니다.

회로에서 Azure Database로의 통신을 허용하려면 회로의 공용 IP 주소에 대한 IP 네트워크 규칙을 만들어야 합니다. ExpressRoute 회로의 공용 IP 주소를 찾기 위해 Azure Portal을 사용하여 ExpressRoute에서 지원 티켓을 엽니다.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->





## <a name="errors-40914-and-40615"></a>오류 40914 및 40615

연결 오류 40914는 Azure Porrtal의 방화벽 창에서 지정한 대로 *가상 네트워크 규칙*과 관련이 있습니다. 40615 오류는 비슷하지만 방화벽의 *IP 주소 규칙*과 관련이 있다는 점은 다릅니다.

#### <a name="error-40914"></a>오류 40914

*메시지 텍스트*: 로그인에서 요청한 '*[server-name]*' 서버를 열 수 없습니다. 클라이언트가 서버에 액세스할 수 없습니다.

*오류 설명:* 클라이언트가 가상 네트워크 서버 끝점이 있는 서브넷에 있습니다. 그러나 Azure SQL Database 서버에는 SQL Database와의 통신 권한을 서브넷에 부여하는 가상 네트워크 규칙이 없습니다.

*오류 해결:* Azure Portal의 방화벽 창에서 가상 네트워크 규칙 제어를 사용하여 서브넷에 대한 [가상 네트워크 규칙을 추가](#anchor-how-to-by-using-firewall-portal-59j)합니다.

#### <a name="error-40615"></a>오류 40615

*메시지 텍스트:* 로그인에서 요청된 서버 '{0}'을(를) 열 수 없습니다. IP 주소가 '{1}'인 클라이언트는 서버에 액세스할 수 없습니다.

*오류 설명:* 클라이언트가 Azure SQL Database 서버에 연결할 권한이 없는 IP 주소에서 연결을 시도합니다. 서버 방화벽에 클라이언트가 주어진 IP 주소로부터 SQL Database로 통신하도록 허용하는 IP 주소 규칙이 없습니다.

*오류 해결:* 클라이언트의 IP 주소를 IP 규칙으로 입력합니다. Azure Portal의 방화벽 창을 사용하여 수행합니다.


몇 가지 SQL Database 오류 메시지 목록은 [여기][sql-database-develop-error-messages-419g]에서 설명합니다.





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>포털은 가상 네트워크 규칙을 만들 수 있습니다.

이 섹션에서는 [Azure Portal][http-azure-portal-link-ref-477t]을 사용하여 Azure SQL Database에서 *가상 네트워크 규칙*을 만드는 방법을 보여 줍니다. 이 규칙은 *Virtual Network 서비스 끝점*으로 태그가 지정된 특정 서브넷에서 보낸 통신을 수락하도록 SQL Database에 지시합니다.

#### <a name="powershell-alternative"></a>PowerShell 대체

PowerShell 스크립트로 가상 네트워크 규칙을 만들 수도 있습니다. 중요 cmdlet **New-AzureRmSqlServerVirtualNetworkRule**. 자세한 내용은 [PowerShell을 사용하여 Azure SQL Database에 대한 Virtual Network 서비스 끝점 및 규칙 만들기][sql-db-vnet-service-endpoint-rule-powershell-md-52d]를 참조하세요.

#### <a name="prerequisites"></a>필수 조건

Azure SQL Database에 관련된 특정 Virtual Network 서비스 끝점 *형식 이름*으로 태그가 지정된 서브넷이 있어야 합니다.

- 관련 끝점 형식 이름은 **Microsoft.Sql**입니다.
- 서브넷이 형식 이름으로 태그가 지정될 수 없는 경우에는 [서브넷이 끝점인지 확인][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]을 참조하세요.

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Azure Portal 단계

1. [Azure Portal][http-azure-portal-link-ref-477t]에 로그인합니다.

2. 그런 다음 Portal에서 **SQL Server** &gt; **방화벽/가상 네트워크**로 이동합니다.

3. **Azure 서비스에 대한 액세스 허용** 컨트롤을 [끄기]로 설정합니다.

    > [!IMPORTANT]
    > 컨트롤을 [켜기] 설정으로 유지하면 Azure SQL Database 서버는 서브넷에서 보낸 통신을 수락하지 않으므로 보안 관점에서 지나친 액세스가 발생할 수 있습니다. Microsoft Azure Virtual Network 서비스 끝점 기능을 SQL Database의 가상 네트워크 규칙 기능과 함께 사용하여 보안 노출 영역을 줄일 수 있습니다.

4. **가상 네트워크** 섹션에서 **+ 기존 항목 추가** 컨트롤을 클릭합니다.

    ![[기존 항목 추가]를 클릭합니다(SQL 규칙으로서 서브넷 끝점).][image-portal-firewall-vnet-add-existing-10-png]

5. 새 **만들기/업데이트** 창에서 Azure 리소스 이름으로 컨트롤을 채웁니다.
 
    > [!TIP]
    > 서브넷에 대한 정확한 **주소 접두사**를 포함해야 합니다. Portal에서 값을 찾을 수 있습니다. **모든 리소스** &gt; **모든 형식** &gt; **가상 네트워크**를 탐색합니다. 필터에 가상 네트워크가 표시됩니다. 사용 중인 가상 네트워크를 클릭하고 **서브넷**을 클릭합니다. **주소 범위** 열에 필요한 주소 접두사가 있습니다.

    ![새 규칙에 대한 필드를 입력합니다.][image-portal-firewall-create-update-vnet-rule-20-png]

6. 창 아래쪽에 있는 **확인** 단추를 클릭합니다.

7.  방화벽 창에서 결과 가상 네트워크 규칙을 확인합니다.

    ![방화벽 창에서 새 규칙을 확인합니다.][image-portal-firewall-vnet-result-rule-30-png]






<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>관련 문서

- [PowerShell을 사용하여 Azure SQL Database에 대한 Virtual Network 서비스 끝점 및 가상 네트워크 규칙 만들기][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Azure 가상 네트워크 서비스 끝점][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙][sql-db-firewall-rules-config-715d]

Microsoft Azure Virtual Network 서비스 끝점 기능과 Azure SQL Database에 대한 가상 네트워크 규칙 기능은 둘 다 2017년 말에 제공될 예정입니다.





<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->

