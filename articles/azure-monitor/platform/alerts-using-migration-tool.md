---
title: Azure 모니터 경고 규칙 마이그레이션
description: 자발적 마이그레이션 도구를 사용하여 기존 경고 규칙을 마이그레이션하는 방법을 알아봅니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114261"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>자발적 마이그레이션 도구를 사용하여 클래식 경고 규칙 마이그레이션

[이전에 발표된](monitoring-classic-retirement.md)대로 Azure Monitor의 클래식 경고는 2019년 9월에 사용 중지됩니다(원래 2019년 7월). Azure 포털에서 클래식 경고 규칙을 사용하고 마이그레이션을 트리거하려는 고객에게 마이그레이션 도구를 사용할 수 있습니다. 이 문서에서는 자동 마이그레이션이 2019년 9월에 시작되기 전에 마이그레이션 도구를 사용하여 클래식 경고 규칙을 자발적으로 마이그레이션하는 방법을 설명합니다.

> [!NOTE]
> 마이그레이션 도구의 롤아웃 이 지연으로 인해 클래식 경고 마이그레이션의 사용 중지 날짜가 원래 발표된 [날짜인 2019년 6월 30일부터 2019년 8월 31일로 연장되었습니다.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

## <a name="benefits-of-new-alerts"></a>새로운 경고의 이점

클래식 경고는 Azure Monitor에서 통합된 새 경고로 대체되고 있습니다. 새 경고 플랫폼에는 다음과 같은 이점이 있습니다.

- 더 많은 Azure 서비스에 대한 다양한 다차원 메트릭에 대해 경고할 수 [있습니다.](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- 새 메트릭 경고는 [다중 리소스 경고 규칙을](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) 지원하여 많은 규칙 관리의 오버헤드를 크게 줄입니다.
- 다음을 지원하는 통합 알림 메커니즘은 다음과 같은 것입니다.
  - [작업 그룹](action-groups.md)- 모든 새 경고 유형(메트릭, 로그 및 활동 로그)과 함께 작동하는 모듈식 알림 메커니즘입니다.
  - SMS, 음성 및 ITSM 커넥터와 같은 새로운 알림 메커니즘.
- [통합 된 경고 환경은](alerts-overview.md) 서로 다른 신호 (메트릭, 로그 및 활동 로그)에 있는 모든 경고를 한 곳에 제공합니다.

## <a name="before-you-migrate"></a>마이그레이션하기 전에

마이그레이션 프로세스는 클래식 경고 규칙을 동등한 새 경고 규칙으로 변환하고 작업 그룹을 만듭니다. 준비 시 다음 사항을 유의하십시오.

- 알림 페이로드 형식과 새 경고 규칙을 만들고 관리하는 API는 더 많은 기능을 지원하기 때문에 클래식 경고 규칙과 다릅니다. [마이그레이션을 준비하는 방법에 대해 알아봅니다.](alerts-prepare-migration.md)

- 일부 클래식 경고 규칙은 도구를 사용하여 마이그레이션할 수 없습니다. [마이그레이션할 수 없는 규칙과 마이그레이션할 작업에 대해 알아봅니다.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > 마이그레이션 프로세스는 클래식 경고 규칙의 평가에 영향을 미치지 않습니다. 마이그레이션되고 새 경고 규칙이 적용될 때까지 계속 실행되고 경고를 보냅니다.

## <a name="how-to-use-the-migration-tool"></a>마이그레이션 도구 사용 방법

Azure 포털에서 클래식 경고 규칙의 마이그레이션을 트리거하려면 다음 단계를 따르십시오.

1. [Azure 포털에서](https://portal.azure.com) **모니터를**선택합니다.

1. **경고를**선택한 다음 **경고 규칙 관리를** 선택하거나 클래식 경고 보기를 **선택합니다.**

1. **새 규칙으로 마이그레이션을** 선택하여 마이그레이션 방문 페이지로 이동합니다. 이 페이지에는 모든 구독 및 해당 마이그레이션 상태 목록이 표시됩니다.

    ![마이그레이션 방문](media/alerts-migration/migration-landing.png "규칙 마이그레이션")

    도구를 사용하여 마이그레이션할 수 있는 모든 구독은 **마이그레이션 준비됨으로**표시됩니다.

    > [!NOTE]
    > 마이그레이션 도구는 클래식 경고 규칙을 사용하는 모든 구독에 단계적으로 롤아웃됩니다. 롤아웃의 초기 단계에서 는 마이그레이션준비가 되지 않은 것으로 표시된 일부 구독이 표시될 수 있습니다.

1. 하나 이상의 구독을 선택한 다음 **미리 보기 마이그레이션을**선택합니다.

    결과 페이지에는 한 번에 하나의 구독에 대해 마이그레이션될 클래식 경고 규칙의 세부 정보가 표시됩니다. **이 구독에 대한 마이그레이션 세부 정보 다운로드를** 선택하여 CSV 형식으로 세부 정보를 얻을 수도 있습니다.

    ![마이그레이션 미리 보기](media/alerts-migration/migration-preview.png "미리 보기 마이그레이션")

1. 마이그레이션 상태에 대한 알림을 받을 하나 이상의 전자 메일 주소를 지정합니다. 마이그레이션이 완료되거나 작업이 필요한 경우 전자 메일을 받게 됩니다.

1. **마이그레이션 시작을**선택합니다. 확인 대화 상자에 표시된 정보를 읽고 마이그레이션 프로세스를 시작할 준비가 되어 있는지 확인합니다.

    > [!IMPORTANT]
    > 구독에 대한 마이그레이션을 시작한 후에는 해당 구독에 대한 클래식 경고 규칙을 편집하거나 만들 수 없습니다. 이 제한은 새 규칙으로 마이그레이션하는 동안 클래식 경고 규칙이 손실되지 않도록 합니다. 클래식 경고 규칙을 변경할 수는 없지만 마이그레이션될 때까지 계속 실행되고 경고를 제공합니다. 구독에 대한 마이그레이션이 완료되면 더 이상 클래식 경고 규칙을 사용할 수 없습니다.

    ![마이그레이션 확인](media/alerts-migration/migration-confirm.png "마이그레이션 시작 확인")

1. 마이그레이션이 완료되거나 조치가 필요한 경우 이전에 제공한 주소로 이메일을 받게 됩니다. 포털의 마이그레이션 방문 페이지에서 상태를 주기적으로 확인할 수도 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>구독이 마이그레이션할 준비가 되지 않은 것으로 나열된 이유는 무엇입니까?

마이그레이션 도구는 단계적으로 고객에게 배포됩니다. 초기 단계에서는 대부분의 또는 모든 구독이 **마이그레이션준비가 되지 않은**것으로 표시될 수 있습니다. 

구독을 마이그레이션할 준비가 되면 구독 소유자는 도구를 사용할 수 있다는 전자 메일 메시지를 받게 됩니다. 이 메시지에 대 한 눈을 밖으로 유지.

### <a name="who-can-trigger-the-migration"></a>마이그레이션을 트리거할 수 있는 사람은 누구입니까?

구독 수준에서 모니터링 기여자 역할이 할당된 사용자는 마이그레이션을 트리거할 수 있습니다. [마이그레이션 프로세스에 대한 역할 기반 액세스 제어에 대해 자세히 알아봅니다.](alerts-understand-migration.md#who-can-trigger-the-migration)

### <a name="how-long-will-the-migration-take"></a>마이그레이션은 얼마나 걸입니까?

대부분의 구독에 대해 마이그레이션이 완료된 시간은 1시간 이내에 완료됩니다. 마이그레이션 방문 페이지에서 마이그레이션 진행 상황을 추적할 수 있습니다. 마이그레이션 하는 동안 경고는 여전히 클래식 경고 시스템 또는 새 경고 시스템에서 실행 되 고 있습니다.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>마이그레이션 중에 문제가 발생하면 어떻게 해야 합니까?

마이그레이션 중에 직면할 수 있는 문제에 대한 도움말은 [문제 해결 가이드를](alerts-understand-migration.md#common-problems-and-remedies) 참조하세요. 마이그레이션을 완료하기 위해 필요한 작업이 있으면 도구를 설정할 때 제공한 이메일 주소로 알림을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 준비](alerts-prepare-migration.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)
