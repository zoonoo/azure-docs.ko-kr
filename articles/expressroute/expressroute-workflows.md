---
title: 'Azure 익스프레스라우팅: 회로 구성 워크플로우'
description: 이 페이지는 ExpressRoute 회로 및 피어링을 구성하기 위한 워크플로를 보여줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864369"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로
이 페이지에서는 높은 수준에서 구성 워크플로 프로비전 및 라우팅 서비스를 안내합니다.

![회로 워크플로](./media/expressroute-workflows/expressroute-circuit-workflow.png)

다음 그림과 해당 단계는 ExpressRoute 회로를 종단 간 프로비전하는 작업을 간략하게 설명합니다. 

1. PowerShell을 사용하여 ExpressRoute 회로를 구성합니다. 자세한 세부 사항은 [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 문서의 지침을 수행합니다.
2. 서비스 공급자에서 연결을 정렬합니다. 이 프로세스는 다양합니다. 연결을 정렬하는 방법에 대한 자세한 내용은 연결 공급자에게 문의합니다.
3. PowerShell 통해 상태를 프로비전하는 ExpressRoute 회로를 확인하여 회로를 성공적으로 프로비전했는지 확인합니다. 
4. 라우팅 도메인을 구성합니다. 연결 공급자가 계층 3 구성을 관리하는 경우 회로에 대한 라우팅을 구성합니다. 연결 공급자가 계층 2 서비스만 제공하는 경우 라우팅 요구 사항 및 라우팅 [구성](expressroute-howto-routing-classic.md) 페이지에 설명된 지침에 따라 [라우팅을](expressroute-routing.md) 구성해야 합니다.
   
   * Azure 프라이빗 피어링 사용 - 이 피어링을 사용하여 가상 네트워크 내에 배포된 VM/클라우드 서비스에 연결합니다.

   * Microsoft 피어링 사용 - Office 365와 같은 Microsoft 온라인 서비스에 액세스할 수 있도록 설정합니다. 모든 Azure PaaS 서비스는 Microsoft 피어링을 통해 액세스할 수 있습니다.
     
     > [!IMPORTANT]
     > 인터넷에 사용하는 것 이외에 별도 프록시/Edge를 사용하여 Microsoft에 연결해야 합니다. ExpressRoute 및 인터넷 모두에 동일한 Edge를 사용하면 비대칭 라우팅이 발생하고 네트워크에 대한 연결 중단이 발생합니다.
     > 
     > 
     
     ![라우팅 워크플로](./media/expressroute-workflows/routing-workflow.png)
5. 가상 네트워크를 ExpressRoute 회로에 연결 - 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다. 지침을 수행하여 회로에 [VNet을 연결](expressroute-howto-linkvnet-arm.md) 합니다. 이러한 VNet은 ExpressRoute 회로로써 동일한 Azure 구독에 있거나 다른 구독에 있을 수 있습니다.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 회로 상태 프로비전
각 ExpressRoute 회로에는 두 가지 상태가 있습니다.

* 서비스 공급자 프로비전 상태
* 상태

상태는 Microsoft의 프로비전 상태를 나타냅니다. Express 경로 회로를 만들 때는 이 속성이 Enabled로 설정됩니다.

연결 공급자 프로비전 상태는 연결 공급자 측의 상태를 나타냅니다. *프로비전되지 않음*, *프로비전 중* 또는 *프로비전됨*일 수 있습니다. ExpressRoute 회로는 피어링을 구성하기 위해 프로비저닝된 상태여야 합니다.

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 회로의 가능한 상태
이 섹션에서는 ExpressRoute 회로의 가능한 상태를 나열합니다.

**생성 시**

ExpressRoute 회로는 리소스 생성 시 다음 상태를 보고합니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**연결 공급자가 회로를 프로비전하는 과정인 경우**

ExpressRoute 회로는 연결 공급자가 회로를 프로비전하는 동안 다음 상태를 보고합니다.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**연결 공급자가 프로세스를 프로비전하기를 완료한 경우**

ExpressRoute 회로는 연결 공급자가 회로를 성공적으로 프로비전하면 다음 상태를 보고합니다.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**연결 공급자가 회로 프로비전을 해제하는 경우**

ExpressRoute 회로를 프로비저닝 해제해야 하는 경우 서비스 공급자가 프로비저닝 해제 프로세스를 완료하면 회로가 다음 상태를 보고합니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


필요에 따라 PowerShell cmdlet을 다시 사용하거나 실행하여 회로를 삭제할 수 있습니다.  

> [!IMPORTANT]
> 서비스프로비저프로비저닝상태가 프로비저닝 또는 프로비전된 경우 회로를 삭제할 수 없습니다. 연결 공급자는 회로를 삭제하려면 회로를 디프로비저닝해제해야 합니다. Microsoft는 Azure에서 ExpressRoute 회로 리소스가 삭제될 때까지 회로에 계속 청구합니다.
> 

## <a name="routing-session-configuration-state"></a>라우팅 세션 구성 상태
BGP 프로비저닝 상태는 Microsoft 에지에서 BGP 세션을 사용하도록 설정한 경우 보고합니다. 개인 또는 Microsoft 피어링을 사용하도록 상태를 사용하도록 설정해야 합니다.

특히 Microsoft 피어링의 경우 BGP 세션 상태를 확인하는 것이 중요합니다. BGP 프로비전 상태 외에도 *보급된 공용 접두사 상태*를 호출하는 다른 상태가 있습니다. 보급된 공용 접두사 상태는 BGP 세션이 최대이고 라우팅이 종단 간 작동하려면 *모두 구성된* 상태여야 합니다. 

보급된 공용 접두사 상태가 *유효성 검사가 필요한* 상태로 설정된 경우 보급된 접두사가 라우팅 레지스트리의의 AS 번호와 일치하지 않기 때문에 BGP 세션을 사용하지 않습니다. 

> [!IMPORTANT]
> 보급된 공용 접두사 상태가 *수동 유효성 검사* 상태인 경우 [Microsoft 지원으로](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 지원 티켓을 열고 관련 자율 시스템 번호와 함께 광고된 IP 주소를 소유하고 있다는 증거를 제공해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* ExpressRoute 연결을 구성합니다.
  
  * [익스프레스루트 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)

