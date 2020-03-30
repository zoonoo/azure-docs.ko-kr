---
title: Azure 응용 프로그램 게이트웨이 리소스 상태 개요
description: 이 문서는 Azure 응용 프로그램 게이트웨이에 대한 리소스 상태 기능에 대한 개요입니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659503"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 응용 프로그램 게이트웨이 리소스 상태 개요

Azure 서비스 문제가 리소스에 영향을 미칠 때 [Azure Resource Health](../service-health/resource-health-overview.md)를 사용하여 문제를 진단하고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 알려줍니다. 또한 문제를 완화하는 데 도움이 되는 기술 지원을 제공합니다.

응용 프로그램 게이트웨이의 경우 리소스 상태는 게이트웨이에서 내보낸 신호를 사용하여 정상 여부를 평가합니다. 게이트웨이가 비정상이면 리소스 상태는 추가 정보를 분석하여 문제의 원인을 확인합니다. 또한 Microsoft에서 수행 중이거나 문제를 해결하기 위해 수행할 수 있는 작업을 식별합니다.

상태가 평가되는 방식에 대한 자세한 내용은 [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)에서 리소스 유형 및 상태 검사에 대한 전체 목록을 확인하세요.


응용 프로그램 게이트웨이의 상태 상태는 다음 상태 중 하나로 표시됩니다.

## <a name="available"></a>사용 가능

**사용 가능한** 상태는 서비스가 리소스의 상태에 영향을 주는 이벤트를 검색하지 않았음을 의미합니다. 게이트웨이가 지난 24시간 동안 계획되지 않은 가동 중지 시간에서 복구된 경우 **최근에 해결된** 알림이 표시됩니다.

![사용 가능한 상태](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>사용할 수 없음

**사용할 수 없는** 상태는 서비스가 게이트웨이의 상태에 영향을 주는 진행 중인 플랫폼 또는 비플랫폼 이벤트를 검색되었음을 의미합니다.

### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 호스트 재부팅)가 모두 포함됩니다.

Resource Health에는 이벤트 및 복구 프로세스에 대한 추가 세부 정보가 제공됩니다. 또한 Microsoft 지원 계약이 유효하지 않은 경우에도 지원 담당자에게 문의할 수 있습니다.

![사용할 수 없는 상태](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>알 수 없음

**알 수 없는** 상태는 리소스 상태가 게이트웨이에 대한 정보를 10분 이상 받지 못했음을 나타냅니다. 이 상태는 게이트웨이의 상태를 명확히 나타내는 것이 아닙니다. 그러나 문제 해결 프로세스에서 중요한 데이터 포인트입니다.

게이트웨이가 예상대로 실행중인 경우 몇 분 후에 상태가 **사용 가능으로** 변경됩니다.

문제가 발생하는 경우 알 **수 없는** 상태 상태는 플랫폼의 이벤트가 게이트웨이에 영향을 미치고 있음을 나타낼 수 있습니다.

![알 수 없는 상태](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>성능 저하됨

**성능 저하** 상태는 게이트웨이에서 성능 저하를 감지했음을 나타내지만 여전히 사용할 수 있습니다.

![디그된 상태](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>다음 단계

응용 프로그램 게이트웨이 웹 응용 프로그램 방화벽(WAF) 문제 해결에 대한 자세한 내용은 [Azure 응용 프로그램 게이트웨이에 대한 WAF(웹 응용 프로그램 방화벽) 문제를 참조하십시오.](web-application-firewall-troubleshoot.md)