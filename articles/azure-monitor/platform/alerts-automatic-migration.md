---
title: Azure Monitor 클래식 경고의 자동 마이그레이션 프로세스 작동 방식 이해
description: 자동 마이그레이션 프로세스의 작동 방식에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668250"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>클래식 경고 규칙에 대한 자동 마이그레이션 프로세스 이해

[이전에 발표된](monitoring-classic-retirement.md)대로 Azure Monitor의 클래식 경고는 2019년 9월에 사용 중지됩니다(원래 2019년 7월). 사용 중지 프로세스의 일부로 Azure 포털에서 [마이그레이션 도구를](alerts-using-migration-tool.md) 사용하여 고객이 마이그레이션을 스스로 트리거할 수 있습니다. 2019년 8월 31일까지 마이그레이션 도구를 사용하지 않은 경우 Azure Monitor는 2019년 9월 1일부터 클래식 경고의 자동 마이그레이션 프로세스를 시작합니다.
이 문서에서는 자동 마이그레이션 프로세스를 살펴보고 발생할 수 있는 문제를 해결하는 데 도움을 줍니다.

  > [!NOTE]
  > 이 문서는 Azure 공용 클라우드에만 적용됩니다. Azure 정부 클라우드 및 Azure China 21Vianet의 Azure Monitor 클래식 경고에 대한 사용 중지 프로세스는 추후 발표될 예정입니다.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>자동 마이그레이션 프로세스 중에 어떤 일이 발생합니까?

- **2019년 9월 1일부터**고객은 [특정 메트릭을](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)제외한 새로운 클래식 경고 규칙을 만들 수 없습니다.
  - 예외의 경우 고객은 2020년 6월까지 새 클래식 경고 규칙을 계속 만들고 클래식 경고를 사용할 수 있습니다.
- **2019년 9월 1일부터**클래식 경고가 있는 모든 고객에 대해 클래식 경고 마이그레이션이 일괄처리됩니다.
- 자발적 마이그레이션 도구와 마찬가지로 마이그레이션할 수 없는 특정 클래식 경고 규칙은 현재 상태로 유지됩니다. 이러한 클래식 경고 규칙은 2020년 6월까지 계속 지원됩니다. 그러나 유효하지 않은 클래식 경고 규칙은 작동하지 않으므로 삭제됩니다.
삭제된 대상 리소스 또는 [더 이상 지원되지 않는 메트릭을 모니터링하는](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) 모든 클래식 경고 규칙은 유효하지 않은 것으로 간주됩니다.
- 구독에 대한 마이그레이션이 시작되면 문제가 없는 한 한 한 시간 이내에 마이그레이션을 완료해야 합니다. 고객은 [Azure Monitor.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)
- 구독 소유자는 마이그레이션을 성공적으로 완료하면 이메일을 받게 됩니다.
- 마이그레이션 중에 문제가 있는 경우 구독 소유자는 동일한 정보를 알리는 전자 메일을 받게 됩니다. 고객은 마이그레이션 블레이드를 사용하여 문제에 대한 전체 세부 정보를 볼 수 있습니다.
- 리소스 잠금을 일시적으로 사용하지 않도록 설정하거나 정책 할당변경과 같은 조치가 필요한 경우 고객은 2019년 10월 31일까지 문제를 해결해야 합니다. 그때까지 문제가 해결되지 않으면 클래식 경고의 성공적인 마이그레이션을 보장할 수 없습니다.

    > [!NOTE]
    > 자동 마이그레이션 프로세스가 시작될 때까지 기다리지 않으려면 마이그레이션 도구를 사용하여 마이그레이션을 자발적으로 트리거할 수 있습니다.

## <a name="important-things-to-note"></a>주의해야 할 중요한 사항

마이그레이션 프로세스는 클래식 경고 규칙을 동등한 새 경고 규칙으로 변환하고 작업 그룹을 만듭니다. 준비 시 다음 사항을 유의하십시오.

- 새 경고 규칙에 대한 알림 페이로드 형식은 더 많은 기능을 지원하기 때문에 클래식 경고 규칙의 형식과 다릅니다. 클래식 경고 규칙에 의해 트리거되는 논리 앱, Runbook 또는 웹후크가 있는 경우 알림 페이로드의 차이로 인해 마이그레이션이 완료되면 예상대로 작동하지 않을 수 있습니다. [마이그레이션을 준비하는 방법에 대해 알아봅니다.](alerts-prepare-migration.md)

- 일부 클래식 경고 규칙은 도구를 사용하여 마이그레이션할 수 없습니다. [마이그레이션할 수 없는 규칙과 마이그레이션할 수 있는 작업에 대해 알아봅니다.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > 마이그레이션 프로세스는 클래식 경고 규칙의 평가에 영향을 미치지 않습니다. 마이그레이션되고 새 경고 규칙이 적용될 때까지 계속 실행되고 경고를 보냅니다.

## <a name="what-if-the-automatic-migration-fails"></a>자동 마이그레이션이 실패하면 어떻게 됩니까?

자동 마이그레이션 프로세스가 실패하면 구독 소유자는 문제를 알리는 이메일을 받게 됩니다. Azure Monitor의 마이그레이션 블레이드를 사용하여 문제의 전체 세부 정보를 볼 수 있습니다.

마이그레이션 중에 직면할 수 있는 문제에 대한 도움말은 [문제 해결 가이드를](alerts-understand-migration.md#common-problems-and-remedies) 참조하세요.

  > [!NOTE]
  > 리소스 잠금을 일시적으로 사용하지 않도록 설정하거나 정책 할당변경과 같은 조치가 필요한 경우 고객은 2019년 10월 31일까지 문제를 해결해야 합니다. 그때까지 문제가 해결되지 않으면 클래식 경고의 성공적인 마이그레이션을 보장할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 준비](alerts-prepare-migration.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)
