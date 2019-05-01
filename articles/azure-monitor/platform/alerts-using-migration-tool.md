---
title: Azure Monitor에서 클래식 경고 자발적 마이그레이션 도구를 사용 하 여 마이그레이션
description: 클래식 경고 규칙을 마이그레이션하도록 자발적 마이그레이션 도구를 사용 하는 방법에 알아봅니다.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 55e6b515328df635a423e0ecf736b03e17a90b8f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698417"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>자발적 마이그레이션 도구를 사용 하 여 클래식 경고 규칙을 마이그레이션하려면

로 [발표](monitoring-classic-retirement.md), Azure Monitor에서 클래식 경고 2019 년 7 월에에서 사용 중지 됩니다. 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고 마이그레이션 자체 트리거 하려는 고객에 게 Azure portal에서 사용할 수 있습니다. 이 문서에서는 자동 마이그레이션이 2019 년 7 월에서에서 시작 되기 전의 클래식 경고 규칙을 자발적으로 마이그레이션하려면 마이그레이션 도구를 사용 하는 방법에 설명 합니다.

## <a name="benefits-of-new-alerts"></a>새 경고의 이점

클래식 경고는 새, 통합 Azure Monitor에서 경고으로 대체 되 고 됩니다. 새 경고 플랫폼에는 다음과 같은 이점이 있습니다.

- 다양 한 다차원 메트릭에 게 알릴 수 있습니다 [많은 Azure 서비스가 증가](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)합니다.
- 새 메트릭 경고 지원 [다중 리소스 경고 규칙](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) 많은 규칙 관리 오버 헤드를 크게 줄일입니다.
- 통합된 알림 메커니즘을 지원 합니다.
  - [작업 그룹](action-groups.md), 모든 새 경고 유형 (메트릭, 로그 및 활동 로그)를 사용 하 여 작동 하는 모듈식 알림 메커니즘입니다.
  - 새 알림 메커니즘에는 SMS, 음성 및 ITSM 커넥터 등입니다.
- 합니다 [통합 된 경고 환경](alerts-overview.md) 한곳에 서로 다른 신호 (메트릭, 로그 및 활동 로그)에서 모든 경고를 제공 합니다.

## <a name="before-you-migrate"></a>마이그레이션하기 전에

마이그레이션 프로세스는 해당 하는, 새 경고 규칙을 클래식 경고 규칙을 변환 하 고 작업 그룹을 만듭니다. 준비에서 다음 사항을 고려해 야 합니다.

- 알림 페이로드 형식 및 새 경고 규칙 만들기 및 관리 Api는 더 많은 기능을 지원 하므로 클래식 경고 규칙의 경우와 다릅니다. [마이그레이션을 준비 하는 방법을 알아봅니다](alerts-prepare-migration.md)합니다.

- 이 도구를 사용 하 여 일부 클래식 경고 규칙을 마이그레이션할 수 없습니다. [규칙을 마이그레이션할 수 없습니다 및 수행할 작업에 대해 알아봅니다](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated)합니다.

    > [!NOTE]
    > 마이그레이션 프로세스에는 클래식 경고 규칙의 평가 영향을 주지 않습니다. 실행 하 고 마이그레이션된 및 새 경고 규칙을 적용 될 때까지 경고를 보내도록 계속 됩니다.

## <a name="how-to-use-the-migration-tool"></a>마이그레이션 도구를 사용 하는 방법

Azure portal에서 클래식 경고 규칙의 마이그레이션을 트리거하려면 다음을 수행 합니다.

1. [Azure portal](https://portal.azure.com)를 선택 **모니터**합니다.

1. 선택 **경고**를 선택한 후 **경고 규칙 관리** 하거나 **클래식 경고를 볼**합니다.

1. 선택 **새 규칙을 마이그레이션** 마이그레이션 방문 페이지로 이동 합니다. 이 페이지에는 모든 구독 및 해당 마이그레이션 상태 목록을 보여 줍니다.

    ![마이그레이션 방문](media/alerts-migration/migration-landing.png "규칙 마이그레이션")

    이 도구를 사용 하 여 마이그레이션할 수 있는 모든 구독으로 표시 됩니다 **마이그레이션할 준비가**합니다.

    > [!NOTE]
    > 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 모든 구독에 단계적에서 출시 됩니다. 출시의 초기 단계에서 마이그레이션에 대 한 준비 되지 않음으로 표시 된 일부 구독을 볼 수 있습니다.

1. 하나 이상의 구독을 선택 하 고 선택한 **마이그레이션 미리 보기**합니다.

    결과 페이지는 한 번에 하나의 구독에 대 한 마이그레이션되는 클래식 경고 규칙의 세부 정보를 표시 합니다. 선택할 수도 있습니다 **이 구독에 대 한 마이그레이션 세부 정보 다운로드** CSV 형식의 세부 정보를 가져오려고 합니다.

    ![마이그레이션-preview](media/alerts-migration/migration-preview.png "마이그레이션 미리 보기")

1. 마이그레이션 상태 알림을 받을 전자 메일 주소를 하나 이상 지정 합니다. 마이그레이션이 완료 된 경우 또는 사용자의 조치가 필요한 경우 전자 메일을 받을 수 있습니다.

1. 선택 **마이그레이션을 시작**합니다. 확인 대화 상자에서 표시 되는 정보를 읽고 마이그레이션 프로세스를 시작할 준비가 있는지 확인 합니다.

    > [!IMPORTANT]
    > 구독에 대 한 마이그레이션을 시작한 후에 해당 구독에 대 한 클래식 경고 규칙을 만들거나 편집 수 없습니다. 이 제한 사항은 새 규칙으로 마이그레이션하는 동안에 클래식 경고 규칙을 변경 하지 않고 손실 되었는지 확인 합니다. 클래식 경고 규칙을 변경할 수 없습니다, 있지만 여전히 계속 마이그레이션될 했습니다 때까지 경고를 제공 하 고 실행 되도록 합니다. 마이그레이션 구독에 대해 완료 된 후 클래식 경고 규칙을 더 이상 사용할 수 없습니다.

    ![마이그레이션 확인](media/alerts-migration/migration-confirm.png "확인 마이그레이션 시작")

1. 마이그레이션이 완료 되었을 때 또는 작업에서 필요한 경우 이전에 제공한 주소로 전자 메일을 받게 됩니다. 포털에서 마이그레이션 방문 페이지에서 상태를 정기적으로 확인할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>내 구독이 마이그레이션에 대 한 준비 되지 않음으로 나열는 이유는 무엇입니까?

마이그레이션 도구는 단계에서 고객에 게 출시 됩니다. 초기 단계에서 대부분 또는 모든 구독으로 표시 될 수 **마이그레이션 준비가 되지 않은**합니다. 월 중순으로 단, 모든 구독 여야 마이그레이션할 준비를 합니다.

구독 마이그레이션에 대 한 준비 되 면이 도구를 사용할 수 있는지와 같은 전자 메일 메시지가 구독 소유자가 받습니다. 이 메시지에 대 한 관심을 가지십시오.

### <a name="who-can-trigger-the-migration"></a>마이그레이션을 트리거할 수는?

구독 수준에서 할당 Monitoring Contributor 역할이 있는 사용자 마이그레이션을 트리거할 수 있습니다. [마이그레이션 프로세스에 대 한 역할 기반 Access Control에 대 한 자세한](alerts-understand-migration.md#who-can-trigger-the-migration)합니다.

### <a name="how-long-will-the-migration-take"></a>마이그레이션 얼마?

한 시간 이내에 대부분의 구독에 대 한 마이그레이션 완료 됩니다. 수를 추적할 수 마이그레이션 방문 페이지에서 마이그레이션 진행률을 합니다. 마이그레이션 중을 보장할 수는 경고 계속 실행 하는 클래식 경고 시스템 또는 새 합니다.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>마이그레이션 중 문제에 직면 하는 경우 어떻게 해야 합니까?

참조 된 [문제 해결 가이드](alerts-understand-migration.md#common-problems-and-remedies) 마이그레이션하는 동안 발생할 수 있습니다 문제에 대 한 도움말입니다. 마이그레이션을 완료 하려면 모든 작업에 필요한 경우이 도구를 설정할 때 제공한 메일 주소로 알림을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션을 위한 준비](alerts-prepare-migration.md)
- [마이그레이션 도구 작동 원리를 이해합니다](alerts-understand-migration.md)
