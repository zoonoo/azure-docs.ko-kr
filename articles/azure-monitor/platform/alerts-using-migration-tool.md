---
title: 자발적 마이그레이션 도구를 사용 하 여 Azure Monitor에서 클래식 경고 마이그레이션
description: 자발적 마이그레이션 도구를 사용 하 여 클래식 경고 규칙을 마이그레이션하는 방법에 대해 알아봅니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 537d84639523a74cbd9403d4ad25c34a798b7061
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665105"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>자발적 마이그레이션 도구를 사용 하 여 클래식 경고 규칙 마이그레이션

[이전에 발표](monitoring-classic-retirement.md)한 대로 Azure Monitor의 클래식 경고는 9 월 2019 (원래 7 월 2019 일)에 사용 중지 됩니다. 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객과 마이그레이션 자체를 트리거하는 고객에 게 Azure Portal에서 사용할 수 있습니다. 이 문서에서는 9 월 2019에 자동 마이그레이션을 시작 하기 전에 마이그레이션 도구를 사용 하 여 기존 경고 규칙을 자발적으로 마이그레이션하는 방법을 설명 합니다.

> [!NOTE]
> 마이그레이션 도구의 롤아웃 지연으로 인해 클래식 경고 마이그레이션의 사용 중지 날짜가 원래 발표 된 날짜 6 월 30 일까 지 [2019 년 8 월 31](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) 2019 일로 연장 되었습니다.

## <a name="benefits-of-new-alerts"></a>새 경고의 이점

클래식 경고는 Azure Monitor의 통합 된 새로운 경고로 대체 되 고 있습니다. 새 경고 플랫폼의 이점은 다음과 같습니다.

- 다양 한 [Azure 서비스](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)에 대 한 다양 한 다차원 메트릭에 대해 경고할 수 있습니다.
- 새 메트릭 경고는 여러 규칙을 관리 하는 오버 헤드를 크게 줄이는 [다중 리소스 경고 규칙](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) 을 지원 합니다.
- 다음을 지 원하는 통합 알림 메커니즘
  - [작업 그룹](action-groups.md)-모든 새 경고 유형 (메트릭, 로그 및 활동 로그)에서 작동 하는 모듈식 알림 메커니즘입니다.
  - SMS, 음성 및 ITSM 커넥터 같은 새로운 알림 메커니즘
- [통합 된 경고 환경](alerts-overview.md) 에서는 서로 다른 신호 (메트릭, 로그 및 활동 로그)에 대 한 모든 경고를 한 곳으로 가져옵니다.

## <a name="before-you-migrate"></a>마이그레이션하기 전에

마이그레이션 프로세스는 기존 경고 규칙을 동등한 새 경고 규칙으로 변환 하 고 작업 그룹을 만듭니다. 준비 중에 다음 사항에 주의 해야 합니다.

- 알림 페이로드 형식과 새 경고 규칙을 만들고 관리 하는 Api는 모두 더 많은 기능을 지원 하기 때문에 기존 경고 규칙의 형식과 다릅니다. [마이그레이션을 준비 하는 방법을 알아봅니다](alerts-prepare-migration.md).

- 일부 클래식 경고 규칙은 도구를 사용 하 여 마이그레이션할 수 없습니다. [마이그레이션할 수 없는 규칙과 수행할 작업에 대해 알아봅니다](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > 마이그레이션 프로세스는 클래식 경고 규칙의 평가에 영향을 주지 않습니다. 이러한 작업은 마이그레이션할 때까지 계속 실행 되 고 경고를 보냅니다.

## <a name="how-to-use-the-migration-tool"></a>마이그레이션 도구를 사용 하는 방법

Azure Portal에서 클래식 경고 규칙의 마이그레이션을 트리거하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 선택 합니다.

1. **경고**를 선택 하 고 **경고 규칙 관리** 또는 **클래식 경고 보기**를 선택 합니다.

1. **새 규칙으로 마이그레이션** 을 선택 하 여 마이그레이션 방문 페이지로 이동 합니다. 이 페이지에는 모든 구독의 목록과 마이그레이션 상태가 표시 됩니다.

    ![마이그레이션-방문](media/alerts-migration/migration-landing.png "마이그레이션 규칙")

    이 도구를 사용 하 여 마이그레이션할 수 있는 모든 구독은 **마이그레이션 준비**로 표시 됩니다.

    > [!NOTE]
    > 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 모든 구독에 대해 단계적으로 롤아웃 됩니다. 롤아웃의 초기 단계에서는 마이그레이션 준비 되지 않음으로 표시 된 일부 구독이 표시 될 수 있습니다.

1. 하나 이상의 구독을 선택한 다음, **마이그레이션 미리 보기**를 선택 합니다.

    결과 페이지에는 한 번에 하나의 구독에 대해 마이그레이션되는 클래식 경고 규칙에 대 한 세부 정보가 표시 됩니다. **이 구독에 대 한 마이그레이션 세부 정보를 다운로드** 를 선택 하 여 세부 정보를 CSV 형식으로 가져올 수도 있습니다.

    ![마이그레이션-미리 보기](media/alerts-migration/migration-preview.png "마이그레이션 미리 보기")

1. 하나 이상의 전자 메일 주소를 지정 하 여 마이그레이션 상태를 알립니다. 마이그레이션이 완료 되 면 전자 메일을 받거나 사용자에 게 필요한 조치가 있는지 확인할 수 있습니다.

1. **마이그레이션 시작**을 선택 합니다. 확인 대화 상자에 표시 된 정보를 읽고 마이그레이션 프로세스를 시작할 준비가 되었는지 확인 합니다.

    > [!IMPORTANT]
    > 구독에 대 한 마이그레이션을 시작한 후에는 해당 구독에 대 한 클래식 경고 규칙을 편집 하거나 만들 수 없습니다. 이러한 제한을 통해 새 규칙으로 마이그레이션하는 동안 기존 경고 규칙에 대 한 변경 내용이 손실 되지 않습니다. 기존 경고 규칙을 변경할 수는 없지만 마이그레이션할 때까지 계속 해 서 실행 하 고 경고를 제공 합니다. 구독에 대 한 마이그레이션이 완료 된 후에는 기존 경고 규칙을 더 이상 사용할 수 없습니다.

    ![마이그레이션-확인](media/alerts-migration/migration-confirm.png "마이그레이션 시작 확인")

1. 마이그레이션이 완료 되거나 사용자의 조치가 필요한 경우 앞에서 제공한 주소로 전자 메일을 받게 됩니다. 포털의 마이그레이션 방문 페이지에서 상태를 주기적으로 확인할 수도 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>내 구독이 마이그레이션 준비 되지 않은 것으로 표시 되는 이유는 무엇 인가요?

마이그레이션 도구는 고객에 게 단계적으로 배포 됩니다. 초기 단계에서는 구독 대부분이 **마이그레이션 준비 안**됨으로 표시 될 수 있습니다. 

구독을 마이그레이션할 준비가 되 면 구독 소유자에 게 해당 도구를 사용할 수 있음을 알리는 전자 메일 메시지가 표시 됩니다. 이 메시지를 확인 하세요.

### <a name="who-can-trigger-the-migration"></a>누가 마이그레이션을 트리거할 수 있나요?

구독 수준에서 모니터링 참여자 역할을 할당 받은 사용자는 마이그레이션을 트리거할 수 있습니다. [마이그레이션 프로세스에 대 한 역할 기반 Access Control에 대해 자세히 알아보세요](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>마이그레이션에 소요 되는 시간

마이그레이션은 한 시간 이내에 대부분의 구독에 대해 완료 됩니다. 마이그레이션 진행률 페이지에서 마이그레이션 진행률을 추적할 수 있습니다. 마이그레이션하는 동안 기존 경고 시스템이 나 새 경고 중 하나에서 여전히 경고를 실행 하 고 있음을 보장 합니다.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>마이그레이션하는 동안 문제가 발생 하는 경우 어떻게 해야 하나요?

마이그레이션 중에 발생할 수 있는 문제에 대 한 도움말은 [문제 해결 가이드](alerts-understand-migration.md#common-problems-and-remedies) 를 참조 하세요. 마이그레이션을 완료 하는 데 필요한 조치가 필요한 경우 도구를 설정할 때 제공 된 전자 메일 주소에 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 준비](alerts-prepare-migration.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)
