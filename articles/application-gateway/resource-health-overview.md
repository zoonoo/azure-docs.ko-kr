---
title: Azure 애플리케이션 Gateway Resource Health 개요
description: 이 문서는 Azure 애플리케이션 게이트웨이의 리소스 상태 기능에 대 한 개요입니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67659503"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 애플리케이션 Gateway Resource Health 개요

Azure 서비스 문제가 리소스에 영향을 미칠 때 [Azure Resource Health](../service-health/resource-health-overview.md) 를 사용하여 문제를 진단하고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 알려줍니다. 또한 문제를 완화하는 데 도움이 되는 기술 지원을 제공합니다.

Application Gateway에 대 한 Resource Health는 게이트웨이에서 내보내는 신호에 의존 하 여 정상 인지 여부를 평가 합니다. 게이트웨이가 비정상 인 경우 Resource Health는 추가 정보를 분석 하 여 문제의 원인을 확인 합니다. 또한 Microsoft에서 수행 하는 작업 또는 문제를 해결 하기 위해 수행할 수 있는 작업을 식별 합니다.

상태가 평가되는 방식에 대한 자세한 내용은 [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)에서 리소스 유형 및 상태 검사에 대한 전체 목록을 확인하세요.


Application Gateway 상태는 다음 상태 중 하나로 표시 됩니다.

## <a name="available"></a>사용 가능

**사용 가능한** 상태는 서비스에서 리소스의 상태에 영향을 주는 이벤트를 검색 하지 못했음을 의미 합니다. 게이트웨이가 지난 24 시간 동안 계획 되지 않은 가동 중지 시간에서 복구 된 경우 **최근에 해결** 된 알림이 표시 됩니다.

![사용 가능한 상태](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Unavailable

**사용할 수** 없는 상태는 서비스에서 게이트웨이의 상태에 영향을 주는 진행 중인 플랫폼 또는 비-플랫폼 이벤트를 검색 했음을 의미 합니다.

### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 호스트 재부팅)가 모두 포함됩니다.

Resource Health에는 이벤트 및 복구 프로세스에 대한 추가 세부 정보가 제공됩니다. 또한 Microsoft 지원 계약이 유효하지 않은 경우에도 지원 담당자에게 문의할 수 있습니다.

![사용할 수 없음 상태](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

**알 수 없음** 상태는 Resource Health 게이트웨이 정보를 10 분 넘게 수신 하지 못했음을 나타냅니다. 이 상태는 게이트웨이의 상태를 명확 하 게 표시 하지 않습니다. 하지만 문제 해결 프로세스의 중요 한 데이터 요소입니다.

게이트웨이가 예상 대로 실행 되는 경우 몇 분 후 상태가 **사용 가능** 으로 변경 됩니다.

문제가 발생 하는 경우 알 수 **없는** 상태는 플랫폼의 이벤트가 게이트웨이에 영향을 주는지 제안 합니다.

![알 수 없는 상태](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>성능 저하됨

성능 **저하** 상태는 게이트웨이가 여전히 사용 가능 하지만 성능 저하를 감지 했음을 나타냅니다.

![Degrated 상태](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>다음 단계

WAF (웹 응용 프로그램 방화벽) Application Gateway 문제 해결에 대해 알아보려면 [Azure 애플리케이션 게이트웨이의 WAF (웹 응용 프로그램 방화벽) 문제 해결](web-application-firewall-troubleshoot.md)을 참조 하세요.