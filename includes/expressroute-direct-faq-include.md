---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f4a1937062f7e59cb3ef3f38610e077e8d36a3ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47005556"
---
### <a name="what-is-expressroute-direct"></a>ExpressRoute Direct란?

ExpressRoute Direct는 고객에게 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. ExpressRoute Direct는 대규모로 활성/활성 연결을 지원하는 이중 100Gbps 연결을 제공합니다. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>고객이 ExpressRoute Direct에 연결하려면 어떻게 해야 하나요? 

고객은 ExpressRoute 라우터에 연결하여 ExpressRoute Direct를 활용하려면 로컬 이동 통신 사업자 및 공동 배치 공급자와 협력해야 합니다.

### <a name="what-locations-will-the-100gbps-expressroute-direct-be-available-for-public-preview"></a>공개 미리 보기의 경우 100Gbps ExpressRoute Direct를 사용할 수 있는 위치는 어디인가요? 

공개 미리 보기에서는 선택한 수의 ExpressRoute 피어링 위치가 이를 지원합니다. 사용 가능한 포트는 동적이며 PowerShell에서 용량을 보는 데 사용할 수 있습니다. ‘가용성에 따라 변경될 수 있는’ 이 위치는 다음과 같습니다.

* 암스테르담
* 캔버라
* 시카코
* 워싱턴 DC
* 댈러스 
* 홍콩
* 로스앤젤레스
* New York City
* 파리
* 샌안토니오
* 실리콘밸리
* 싱가포르 

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct에 대한 SLA란 무엇인가요?

ExpressRoute Direct는 동일한 [엔터프라이즈급 ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)를 사용합니다. 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>고객이 ExpressRoute Direct 사용을 고려해야 하는 시나리오는 무엇인가요?  

ExpressRoute Direct는 Microsoft 글로벌 백본에 대한 직접 100Gbps 포트 쌍을 고객에게 제공합니다. 고객에게 최상의 이점을 제공하는 시나리오에는 대량 데이터 수집, 규제 시장에 대한 물리적 격리 및 버스트 시나리오(예: 렌더링)에 대한 전용 용량이 포함됩니다. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct에 대한 청구 모델은 무엇인가요? 

ExpressRoute Direct는 고정 금액으로 포트 쌍에 대한 요금이 청구됩니다. 표준 회로는 추가 시간 없이 포함되며 프리미엄에는 약간의 추가 요금이 포함됩니다. 피어링 위치의 영역에 따라 회로별로 송신에 대한 요금이 청구됩니다.