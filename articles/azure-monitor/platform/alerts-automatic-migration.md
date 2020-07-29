---
title: Azure Monitor 클래식 경고에 대 한 자동 마이그레이션 프로세스가 작동 하는 방식을 이해 합니다.
description: 자동 마이그레이션 프로세스가 작동 하는 방식을 알아봅니다.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77668250"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>기존 경고 규칙에 대 한 자동 마이그레이션 프로세스 이해

[이전에 발표](monitoring-classic-retirement.md)한 대로 Azure Monitor의 클래식 경고는 9 월 2019 (원래 7 월 2019 일)에 사용 중지 됩니다. 사용 중지 프로세스의 일환으로 마이그레이션 [도구](alerts-using-migration-tool.md) 는 고객이 마이그레이션 자체를 트리거하기 위해 Azure Portal에서 사용할 수 있습니다. 2019 년 8 월 31 일까 지 마이그레이션 도구를 사용 하지 않은 경우 Azure Monitor는 2019 년 9 월 1 일부 터 클래식 경고의 자동 마이그레이션 프로세스를 시작 합니다.
이 문서에서는 자동 마이그레이션 프로세스를 안내 하 고 발생할 수 있는 모든 문제를 해결 하는 데 도움을 줍니다.

  > [!NOTE]
  > 이 문서는 Azure 공용 클라우드에만 적용 됩니다. Azure Government 클라우드 및 Azure 중국 21Vianet에서 Azure Monitor 클래식 경고에 대 한 사용 중지 프로세스는 미래의 날짜에 발표 될 예정입니다.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>자동 마이그레이션 프로세스 중에는 어떻게 되나요?

- **2019 년 9 월 1**일부 터 고객은 [특정 메트릭을](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)제외 하 고 새로운 클래식 경고 규칙을 만들 수 없습니다.
  - 예외의 경우 고객은 계속 해 서 새 클래식 경고 규칙을 만들고 6 월 2020 일까 야 클래식 경고를 사용할 수 있습니다.
- **2019 년 9 월 1**일부 터 클래식 경고가 있는 모든 고객에 대해 클래식 경고의 마이그레이션이 일괄 처리로 트리거됩니다.
- 자발적 마이그레이션 도구와 마찬가지로 마이그레이션할 수 있고 되지 않는 특정 클래식 경고 규칙은 그대로 유지 됩니다. 이러한 클래식 경고 규칙은 6 월 2020 일까 지 계속 지원 됩니다. 그러나 잘못 된 클래식 경고 규칙은 작동 하지 않을 때 삭제 됩니다.
삭제 된 대상 리소스 또는 [더 이상 지원 되지 않는 메트릭에](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) 대해 모니터링 하는 모든 클래식 경고 규칙은 잘못 된 것으로 간주 됩니다.
- 구독에 대 한 마이그레이션이 시작 되 면 문제가 없는 한 한 시간 이내에 마이그레이션이 완료 됩니다. 고객은 [Azure Monitor의 마이그레이션 블레이드에서](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)마이그레이션 상태를 모니터링할 수 있습니다.
- 마이그레이션이 성공적으로 완료 되 면 구독 소유자가 전자 메일을 받게 됩니다.
- 마이그레이션 중에 문제가 발생 하는 경우 구독 소유자는 동일한을 알리는 전자 메일도 받게 됩니다. 고객은 마이그레이션 블레이드를 사용 하 여 문제에 대 한 전체 세부 정보를 볼 수 있습니다.
- 리소스 잠금을 일시적으로 비활성화 하거나 정책 할당을 변경 하는 등 고객의 작업이 필요한 경우 고객은 2019 년 10 월 31 일까 지 문제를 해결 해야 합니다. 문제가 해결 되지 않으면 클래식 경고에 대 한 성공적인 마이그레이션을 보장할 수 없습니다.

    > [!NOTE]
    > 자동 마이그레이션 프로세스가 시작 될 때까지 기다리지 않으려면 마이그레이션 도구를 사용 하 여 마이그레이션을 자발적으로 트리거할 수 있습니다.

## <a name="important-things-to-note"></a>유의 해야 할 중요 사항

마이그레이션 프로세스는 기존 경고 규칙을 동등한 새 경고 규칙으로 변환 하 고 작업 그룹을 만듭니다. 준비 중에 다음 사항에 주의 해야 합니다.

- 새 경고 규칙의 알림 페이로드 형식은 더 많은 기능을 지원 하기 때문에 클래식 경고 규칙과 다릅니다. 클래식 경고 규칙에 의해 트리거되는 논리 앱, runbook 또는 webhook가 있는 경우 알림 페이로드의 차이로 인해 마이그레이션이 완료 되 면 예상 대로 작동 하지 않을 수 있습니다. [마이그레이션을 준비 하는 방법을 알아봅니다](alerts-prepare-migration.md).

- 일부 클래식 경고 규칙은 도구를 사용 하 여 마이그레이션할 수 없습니다. [마이그레이션할 수 없는 규칙과 수행할 작업에 대해 알아봅니다](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > 마이그레이션 프로세스는 클래식 경고 규칙의 평가에 영향을 주지 않습니다. 이러한 작업은 마이그레이션할 때까지 계속 실행 되 고 경고를 보냅니다.

## <a name="what-if-the-automatic-migration-fails"></a>자동 마이그레이션이 실패 하면 어떻게 되나요?

자동 마이그레이션 프로세스가 실패 하면 구독 소유자는 문제를 알리는 전자 메일을 받게 됩니다. Azure Monitor의 마이그레이션 블레이드를 사용 하 여 문제의 전체 세부 정보를 확인할 수 있습니다.

마이그레이션 중에 발생할 수 있는 문제에 대 한 도움말은 [문제 해결 가이드](alerts-understand-migration.md#common-problems-and-remedies) 를 참조 하세요.

  > [!NOTE]
  > 리소스 잠금을 일시적으로 비활성화 하거나 정책 할당을 변경 하는 등 고객의 작업이 필요한 경우 고객은 2019 년 10 월 31 일까 지 문제를 해결 해야 합니다. 문제가 해결 되지 않으면 클래식 경고를 성공적으로 마이그레이션하지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 준비](alerts-prepare-migration.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)
