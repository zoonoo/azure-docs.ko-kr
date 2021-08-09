---
title: 'Azure ExpressRoute: 회로 구성 워크플로'
description: 이 페이지는 ExpressRoute 회로 및 피어링을 구성하기 위한 워크플로를 보여줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721738"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로

이 문서는 높은 수준에서 서비스 프로비저닝 및 라우팅 구성 워크플로를 안내합니다. 다음 섹션에서는 ExpressRoute 회로를 엔드투엔드 프로비저닝하는 작업을 간략하게 설명합니다.

## <a name="workflow-steps"></a>워크플로 단계

### <a name="1-prerequisites"></a>1. 사전 요구 사항

필수 구성 요소가 충족되었는지 확인합니다. 전체 목록은 [필수 구성 요소 및 검사 목록](expressroute-prerequisites.md)을 참조하세요.

* Azure 구독이 만들어졌습니다.
* 물리적 연결이 ExpressRoute 파트너와 설정되었거나 ExpressRoute Direct를 통해 구성되었습니다. 위치를 검토하고 [위치 및 파트너](expressroute-locations-providers.md#partners)를 참조하여 피어링 위치에 걸쳐 ExpressRoute 파트너 및 ExpressRoute Direct 연결을 확인하세요.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. 연결 주문 또는 ExpressRoute Direct 구성

서비스 공급자에게 연결을 주문하거나 ExpressRoute Direct를 구성하세요.

#### <a name="expressroute-partner-model"></a>ExpressRoute 파트너 모델

서비스 공급자에서 연결을 정렬합니다. 이 프로세스는 다양합니다. 연결을 정렬하는 방법에 대한 자세한 내용은 연결 공급자에게 문의합니다.

* ExpressRoute 파트너 선택
* 피어링 위치를 선택합니다.
* 대역폭 선택
* 청구 모델 선택
* 표준 또는 프리미엄 추가 항목 선택

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct 모델

* 피어링 위치에서 사용 가능한 ExpressRoute Direct 용량을 봅니다.
* Azure 구독에서 ExpressRoute Direct 리소스를 만들어 포트를 예약합니다.
* 피어링 위치 공급자로부터 승인 문서를 요청 및 수신하고 물리적 교차 연결을 주문합니다.
* [Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics)를 사용하여 관리자 상태를 사용하도록 설정하고 조명 수준과 물리적 링크를 봅니다.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute 회로 만들기

#### <a name="expressroute-partner-model"></a>ExpressRoute 파트너 모델

ExpressRoute 파트너가 연결을 프로비저닝할 준비가 되었는지 확인합니다. ExpressRoute 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. [ExpressRoute 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md)의 지침을 사용하여 회로를 만듭니다.

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct 모델

물리적 링크와 관리자 상태가 두 링크에서 모두 사용하도록 설정되어 있는지 확인합니다. 지침은 [ExpressRoute Direct를 구성하는 방법](how-to-expressroute-direct-portal.md)을 참조하세요. ExpressRoute 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. [ExpressRoute 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md)의 지침을 사용하여 회로를 만듭니다.

### <a name="4-service-provider-provisions-connectivity"></a>4. 서비스 공급자는 연결을 프로비저닝합니다.

이 섹션은 ExpressRoute 파트너 연결 모델에만 적용됩니다.

* 연결 공급자에게 서비스 키(s-key)를 제공합니다.
* 연결 공급자가 필요로 하는 추가 정보(예: VPN ID)를 제공합니다.
* 공급자가 라우팅 구성을 관리하는 경우 필요한 세부 정보를 제공합니다.

PowerShell, Azure Portal 또는 CLI를 사용하여 ExpressRoute 회로 프로비저닝 상태를 확인하여 회로가 성공적으로 프로비저닝되었는지 확인할 수 있습니다.

### <a name="5-configure-routing-domains"></a>5. 라우팅 도메인 구성

라우팅 도메인을 구성합니다. 연결 공급자가 사용자를 위해 계층 3 구성을 관리하는 경우 회로에 라우팅을 구성합니다. 연결 제공업체에서 계층 2 서비스만 제공하거나 ExpressRoute Direct를 사용하는 경우 [라우팅 요구 사항](expressroute-routing.md) 및 [라우팅 구성](expressroute-howto-routing-classic.md) 문서에 설명된 가이드라인에 따라 라우팅을 구성해야 합니다.

#### <a name="for-azure-private-peering"></a>Azure 프라이빗 피어링의 경우

Azure 가상 네트워크 내에 배포된 VM 및 클라우드 서비스에 연결하기 위해 프라이빗 피어링을 사용하도록 설정합니다.

* IPv4 서브넷:
    * 경로 1에 대한 피어링 서브넷(/30)
    * 경로 2에 대한 피어링 서브넷(/30)
* IPv6 서브넷(선택 사항):
    * 경로 1의 피어링 서브넷(/126)
    * 경로 2의 피어링 서브넷(/126)
* 피어링에 대한 VLAN ID
* 피어링에 대한 ASN
* ExpressRoute ASN = 12076
* MD5 해시(선택 사항)

#### <a name="for-microsoft-peering"></a>Microsoft 피어링의 경우

Microsoft 365와 같은 Microsoft Online Services에 액세스하려면 이 옵션을 사용하도록 설정합니다. 또한 모든 Azure PaaS 서비스는 Microsoft 피어링을 통해 액세스할 수도 있습니다. 인터넷에 사용하는 것 이외에 별도 프록시/Edge를 사용하여 Microsoft에 연결해야 합니다. ExpressRoute 및 인터넷 모두에 동일한 Edge를 사용하면 비대칭 라우팅이 발생하고 네트워크에 대한 연결 중단이 발생합니다.

* IPv4 서브넷:
    * 경로 1의 피어링 서브넷(/30) - 공용 IP여야 합니다.
    * 경로 2의 피어링 서브넷(/30) - 공용 IP여야 합니다.
* IPv6 서브넷(선택 사항):
    * 경로 1의 피어링 서브넷(/126) - 공용 IP여야 합니다.
    * 경로 2의 피어링 서브넷(/126) - 공용 IP여야 합니다.
* 피어링에 대한 VLAN ID
* 피어링에 대한 ASN
* 보급된 접두사 - 공용 IP 접두사여야 합니다.
* 고객 ASN(피어링 ASN과 다른 경우 선택 사항)
* IP 및 ASN 유효성 검사를 위한 RIR / IRR
* ExpressRoute ASN = 12076
* MD5 해시(선택 사항)

### <a name="6-start-using-the-expressroute-circuit"></a>6. ExpressRoute 회로 사용 시작

* Azure 가상 네트워크를 ExpressRoute 회로에 연결하여 온-프레미스에서 Azure 가상 네트워크로의 연결을 사용하도록 설정할 수 있습니다. 지침은 [회로에 VNet 연결](expressroute-howto-linkvnet-arm.md) 문서를 참조하세요. 이러한 VNet은 ExpressRoute 회로로써 동일한 Azure 구독에 있거나 다른 구독에 있을 수 있습니다.
* Microsoft 피어링을 통해 Azure 서비스 및 Microsoft 클라우드 서비스에 연결합니다.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 파트너 회로 프로비저닝 상태

다음 섹션에서는 ExpressRoute 파트너 연결 모델에 대한 다양한 ExpressRoute 회로 상태를 간략하게 설명합니다.
각 ExpressRoute 파트너 회로에는 두 가지 상태가 있습니다.

* **ServiceProviderProvisioningState** 는 연결 공급자 측의 상태를 나타냅니다. *프로비전되지 않음*, *프로비전 중* 또는 *프로비전됨* 일 수 있습니다. 피어링을 구성하려면 ExpressRoute 회로가 프로비저닝된 상태여야 합니다. **이 상태는 ExpressRoute 파트너 회로에만 적용되며 ExpressRoute Direct 회로의 속성에는 표시되지 않습니다**.

* **상태** 는 Microsoft의 프로비저닝 상태를 나타냅니다. ExpressRoute 회로를 만들 때는 이 속성이 Enabled로 설정됩니다.

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 회로의 가능한 상태

이 섹션에서는 ExpressRoute 파트너 연결 모델에서 만든 ExpressRoute 회로의 가능한 상태를 간략하게 설명합니다.

**생성 시**

ExpressRoute 회로는 리소스 생성 시 다음 상태를 보고합니다.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**연결 공급자에서 회로를 프로비저닝하고 있는 경우**

ExpressRoute 회로는 연결 공급자가 회로를 프로비저닝하는 동안 다음 상태를 보고합니다.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**연결 공급자에서 프로비저닝 프로세스를 완료한 경우**

ExpressRoute 회로는 연결 공급자가 회로를 성공적으로 프로비저닝하면 다음 상태를 보고합니다.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**연결 공급자에서 회로 프로비저닝을 해제하는 경우**

ExpressRoute 회로를 프로비저닝 해제해야 하는 경우 서비스 공급자가 프로비저닝 해제 프로세스를 완료하면 회로에서 다음 상태를 보고합니다.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

필요에 따라 PowerShell cmdlet을 다시 사용하거나 실행하여 회로를 삭제할 수 있습니다.  

> [!IMPORTANT]
> ServiceProviderProvisioningState가 Provisioning 또는 Provisioned인 경우 회로를 삭제할 수 없습니다. 회로를 삭제하려면 연결 공급자가 회로의 프로비저닝을 해제해야 합니다. Microsoft는 Azure에서 ExpressRoute 회로 리소스가 삭제될 때까지 회로에 계속 비용을 청구합니다.
> 

## <a name="routing-session-configuration-state"></a>라우팅 세션 구성 상태

BGP 프로비저닝 상태를 사용하면 Microsoft Edge에서 BGP 세션이 사용하도록 설정되었는지 보고합니다. 프라이빗 피어링 또는 Microsoft 피어링을 사용하려면 상태를 사용하도록 설정해야 합니다.

특히 Microsoft 피어링의 경우 BGP 세션 상태를 확인하는 것이 중요합니다. BGP 프로비전 상태 외에도 *보급된 공용 접두사 상태* 를 호출하는 다른 상태가 있습니다. BGP 세션이 작동되고 라우팅이 엔드투엔드에 작동하려면 보급된 공용 접두사 상태가 *구성된* 상태여야 합니다. 

보급된 공용 접두사 상태가 *유효성 검사가 필요한* 상태로 설정된 경우 보급된 접두사가 라우팅 레지스트리의의 AS 번호와 일치하지 않기 때문에 BGP 세션을 사용하지 않습니다.

> [!IMPORTANT]
> 보급된 공용 접두사 상태가 *수동 유효성 검사* 상태인 경우 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 열고 사용자가 연관된 자치 시스템 번호와 함께 보급된 IP 주소를 소유한 증거를 제공해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계

* ExpressRoute 연결을 구성합니다.
  
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
