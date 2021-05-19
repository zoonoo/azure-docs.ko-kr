---
title: Azure Application Gateway Resource Health 개요
description: 이 문서는 Azure Application Gateway의 리소스 상태 기능에 대한 개요입니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397175"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure Application Gateway Resource Health 개요

Azure 서비스 문제가 리소스에 영향을 미칠 때 [Azure Resource Health](../service-health/resource-health-overview.md) 를 사용하여 문제를 진단하고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 알려줍니다. 또한 문제를 완화하는 데 도움이 되는 기술 지원을 제공합니다.

Application Gateway의 경우, Resource Health는 게이트웨이에서 내보내는 신호에 의존하여 정상인지 여부를 평가합니다. 리소스가 정상이 아니면, Resource Health에서 추가 정보를 분석하여 문제 원인을 파악합니다. 이는 또한 Microsoft에서 수행하는 작업 또는 귀하가 문제 해결을 위해 직접 할 수 있는 작업을 식별합니다.

상태가 평가되는 방식에 대한 자세한 내용은 [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)에서 리소스 유형 및 상태 검사에 대한 전체 목록을 확인하세요.


Application Gateway 상태는 다음 상태 중 하나로 표시됩니다.

## <a name="available"></a>사용 가능

**사용 가능** 은 리소스의 상태에 영향을 미치는 이벤트가 서비스에서 검색되지 않은 상태를 말합니다. 게이트웨이가 최근 24시간 동안 계획되지 않은 가동 중지 시간에서 복구된 경우 **최근에 해결됨** 알림이 표시됩니다.

![사용 가능 상태](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Unavailable

**사용할 수 없음** 은 게이트웨이 상태에 영향을 미치는 플랫폼 또는 비-플랫폼 이벤트가 진행되고 있는 것이 서비스에서 검색되었음을 나타내는 상태입니다.

### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 호스트 재부팅)가 모두 포함됩니다.

Resource Health에는 이벤트 및 복구 프로세스에 대한 추가 세부 정보가 제공됩니다. 또한 Microsoft 지원 계약이 유효하지 않은 경우에도 지원 담당자에게 문의할 수 있습니다.

![사용할 수 없음 상태](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>알 수 없음

**알 수 없음** 은 Resource Health에서 이 게이트웨이에 대한 정보를 10분 이상 수신하지 못했음을 나타내는 상태입니다. 이 상태는 게이트웨이의 상태를 명확하게 표시하지 않습니다. 하지만 문제 해결 프로세스의 중요한 데이터 요소입니다.

게이트웨이가 예상한 대로 실행 중이면 몇 분 후 상태가 **사용 가능** 으로 변경됩니다.

문제가 발생하는 경우, 상태가 **알 수 없음** 이면, 플랫폼의 이벤트가 게이트웨이에 영향을 미치고 있다는 것을 의미할 수 있습니다.

![알 수 없음 상태](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>성능 저하됨

**성능 저하됨** 상태는 게이트웨이를 계속 사용할 수는 있지만 성능 저하가 감지되었음을 나타냅니다.

![성능 저하됨 상태](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>다음 단계

Application Gateway WAF(웹 애플리케이션 방화벽) 관련 문제의 해결에 대해 알아보려면, [Azure Application Gateway의 WAF(웹 애플리케이션 방화벽) 문제 해결](../web-application-firewall/ag/web-application-firewall-troubleshoot.md)을 참조하세요.