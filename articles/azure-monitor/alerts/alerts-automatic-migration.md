---
title: Azure Monitor 클래식 경고에 대 한 자동 마이그레이션 프로세스가 작동 하는 방식을 이해 합니다.
description: 자동 마이그레이션 프로세스가 작동 하는 방식을 알아봅니다.
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 92aaffcea7a7c96cd77aade318520b093eed3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045465"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>기존 경고 규칙에 대 한 자동 마이그레이션 프로세스 이해

[이전에 발표](monitoring-classic-retirement.md)한 대로 Azure Monitor의 클래식 경고는 공용 클라우드 사용자에 게 사용이 중지 되었지만 31 년 5 **월 2021** 일까 지 계속 사용 됩니다. Azure Government 클라우드 및 Azure 중국 21Vianet에 대 한 클래식 경고는 **2024 년 2 월 29 일** 에 사용 중지 됩니다.

마이그레이션 [도구](alerts-using-migration-tool.md) 는 고객이 마이그레이션 자체를 트리거하기 위해 Azure Portal에서 사용할 수 있습니다. 이 문서에서는 31 년 5 월 2021 일 이후에 시작 되는 공용 클라우드의 자동 마이그레이션 프로세스에 대해 설명 합니다. 또한 발생할 수 있는 문제 및 해결 방법에 대해 자세히 설명 합니다.

## <a name="important-things-to-note"></a>유의 해야 할 중요 사항

마이그레이션 프로세스는 기존 경고 규칙을 동등한 새 경고 규칙으로 변환 하 고 작업 그룹을 만듭니다. 준비 중에 다음 사항에 주의 해야 합니다.

- 새 경고 규칙의 알림 페이로드 형식은 더 많은 기능을 지원 하기 때문에 클래식 경고 규칙의 페이로드와 다릅니다. 논리 앱, runbook 또는 웹 후크에 대 한 클래식 경고 규칙이 있는 경우 페이로드의 차이로 인해 마이그레이션 후 예상 대로 작동 하지 않을 수 있습니다. [마이그레이션을 준비 하는 방법을 알아봅니다](alerts-prepare-migration.md).

- 일부 클래식 경고 규칙은 도구를 사용 하 여 마이그레이션할 수 없습니다. [마이그레이션할 수 없는 규칙과 수행할 작업에 대해 알아봅니다](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>공용 클라우드에서 자동 마이그레이션 프로세스를 진행 하는 동안 어떻게 되나요?

- 31 년 5 월 2021까지 새로운 클래식 경고 규칙을 만들 수 없고 클래식 경고 마이그레이션이 일괄 처리로 트리거됩니다.
- 삭제 된 대상 리소스 또는 [더 이상 지원 되지 않는 메트릭에](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) 대해 모니터링 하는 모든 클래식 경고 규칙은 잘못 된 것으로 간주 됩니다.
- 유효 하지 않은 클래식 경고 규칙은 31 년 5 월 2021 일 이후 제거 될 예정입니다.
- 구독에 대 한 마이그레이션이 시작 되 면 1 시간 이내에 완료 되어야 합니다. 고객은 [Azure Monitor의 마이그레이션 도구](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)에서 마이그레이션 상태를 모니터링할 수 있습니다.
- 구독 소유자는 마이그레이션 성공 또는 실패에 대 한 전자 메일을 받습니다.

    > [!NOTE]
    > 자동 마이그레이션 프로세스가 시작 될 때까지 기다리지 않으려면 마이그레이션 도구를 사용 하 여 마이그레이션을 자발적으로 트리거할 수 있습니다.

## <a name="what-if-the-automatic-migration-fails"></a>자동 마이그레이션이 실패 하면 어떻게 되나요?

자동 마이그레이션 프로세스가 실패 하면 구독 소유자는 문제를 알리는 전자 메일을 받게 됩니다. Azure Monitor의 마이그레이션 도구를 사용 하 여 문제의 전체 정보를 확인할 수 있습니다. 마이그레이션 중에 발생할 수 있는 문제에 대 한 도움말은 [문제 해결 가이드](alerts-understand-migration.md#common-problems-and-remedies) 를 참조 하세요.

  > [!NOTE]
  > 리소스 잠금을 일시적으로 비활성화 하거나 정책 할당을 변경 하는 등 고객의 작업이 필요한 경우 고객은 이러한 문제를 해결 해야 합니다. 문제가 해결 되지 않으면 클래식 경고를 성공적으로 마이그레이션하지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 준비](alerts-prepare-migration.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)