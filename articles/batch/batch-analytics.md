---
title: Azure Batch 분석
description: Batch 분석의 항목에는 Batch 서비스 리소스에 사용할 수 있는 이벤트 및 경고에 대한 참조 정보가 포함되어 있습니다.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849514"
---
# <a name="batch-analytics"></a>Batch 분석

이 섹션의 항목에는 Batch 서비스 리소스에 사용할 수 있는 이벤트 및 경고에 대 한 참조 정보가 포함 되어 있습니다.

Batch 진단 로그를 활성화하고 사용하는 방법에 대한 자세한 내용은 [Azure Batch 진단 로깅](./batch-diagnostics.md)을 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그

Azure Batch 서비스는 특정 Batch 리소스의 수명 동안 다음과 같은 진단 로그 이벤트를 내보냅니다.

### <a name="service-log-events"></a>서비스 로그 이벤트

- [풀 만들기](batch-pool-create-event.md)
- [풀 삭제 시작](batch-pool-delete-start-event.md)
- [풀 삭제 완료](batch-pool-delete-complete-event.md)
- [풀 크기 조정 시작](batch-pool-resize-start-event.md)
- [풀 크기 조정 완료](batch-pool-resize-complete-event.md)
- [풀 자동 크기 조정](batch-pool-autoscale-event.md)
- [작업 시작](batch-task-start-event.md)
- [작업 완료](batch-task-complete-event.md)
- [작업 실패](batch-task-fail-event.md)
- [작업 일정 실패](batch-task-schedule-fail-event.md)
