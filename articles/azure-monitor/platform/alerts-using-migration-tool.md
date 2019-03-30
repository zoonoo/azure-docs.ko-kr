---
title: 자발적 마이그레이션 도구를 사용 하 여 Azure Monitor에서 클래식 경고 마이그레이션
description: 클래식 경고 규칙을 마이그레이션하도록 자발적 마이그레이션 도구를 사용 하는 방법에 알아봅니다.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632265"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>자발적 마이그레이션 도구를 사용 하 여 클래식 경고 규칙을 마이그레이션하려면

로 [발표](monitoring-classic-retirement.md), Azure Monitor에서 클래식 경고 2019 년 7 월에에서 사용 중지 됩니다. 마이그레이션 도구 마이그레이션을 자발적으로 트리거를 Azure portal에서 사용할 수 있으며 클래식 경고 규칙을 사용 하는 고객에 게 배포 됩니다. 이 문서에서는 안내 2019 년 7 월에서에서 자동 마이그레이션이 시작 되기 전의 클래식 경고 규칙을 자발적으로 마이그레이션하는 마이그레이션 도구를 사용 하는 방법에 있습니다.

## <a name="benefits-of-new-alerts"></a>새 경고의 이점

클래식 경고는 Azure Monitor에서 새 통합 경고으로 대체 되 고 됩니다. 새 경고 플랫폼에는 다음과 같은 이점이 있습니다.

- 다양 한 다차원 메트릭 경고 [많은 더 많은 Azure 서비스](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- 새로운 메트릭 경고 지원 [다중 리소스 경고 규칙](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) 많은 규칙 관리 오버 헤드를 크게 줄일입니다.
- 통합된 알림 메커니즘
  - [작업 그룹](action-groups.md) 는 모든 새 경고 유형 (메트릭, 로그 및 활동 로그)를 사용 하 여 작동 하는 모듈식 알림 메커니즘
  - SMS, 음성 및 ITSM 커넥터와 같은 새 알림 메커니즘을 활용할 수 있습니다.
- 합니다 [통합 된 경고 환경](alerts-overview.md) 서로 다른 신호에서 모든 경고를 제공 (메트릭, 활동 로그 및 로그)를 한 곳에

## <a name="before-you-migrate"></a>마이그레이션하기 전에

마이그레이션의 일부로 클래식 경고 규칙은 해당 하는 새 경고 규칙에 변환 하 고 작업 그룹 만들어집니다.

- 알림 페이로드 형식 뿐만 아니라 새 경고 규칙 만들기 및 관리 Api는 더 많은 기능을 지 원하는 클래식 경고 규칙의 다릅니다. 에 대해 알아봅니다 [마이그레이션을 준비 하는 방법을](alerts-prepare-migration.md)합니다.

- 이 도구를 사용 하 여 일부 클래식 경고 규칙을 마이그레이션할 수 없습니다. [설명 하는 규칙 마이그레이션할 고 마이그레이션해야 하는 방법은](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated)합니다.

    > [!NOTE]
    > 마이그레이션 프로세스에는 클래식 경고 규칙의 평가 영향을 주지 않습니다. 계속 실행 하 고 새 경고 규칙의 평가 시작 하 고 마이그레이션될 때까지 경고를 보냅니다.


## <a name="how-to-use-the-migration-tool"></a>마이그레이션 도구를 사용 하는 방법

다음 절차에 Azure portal에서 클래식 경고 규칙의 마이그레이션 트리거하는 방법을 설명 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 클릭합니다.

2. 클릭 **경고** 클릭 **경고 규칙 관리** 하거나 **클래식 경고를 보려면**합니다.

3. 클릭 **새 규칙을 마이그레이션** 마이그레이션 방문 페이지로 이동 합니다. 이 페이지에는 모든 구독 목록과에 마이그레이션 상태를 보여 줍니다.

    ![마이그레이션 방문](media/alerts-migration/migration-landing.png "규칙 마이그레이션")

4. 이 도구를 사용 하 여 마이그레이션할 수 있는 모든 구독으로 표시 됩니다 **마이그레이션할 준비가**합니다.

    > [!NOTE]
    > 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 모든 구독에 단계적에서 출시 됩니다. 출시의 초기 단계에서 일부 구독이 마이그레이션에 대 한 준비 되지 않음으로 표시 될 수 있습니다.

5. 하나 이상의 구독을 선택 하 고 클릭 **마이그레이션 미리 보기**

6. 이 페이지에서 한 번에 하나의 구독에 대 한 마이그레이션되는 클래식 경고 규칙의 세부 정보를 볼 수 있습니다. 할 수도 있습니다 **이 구독에 대 한 마이그레이션 세부 정보 다운로드** .csv 형식에서입니다.

    ![마이그레이션-preview](media/alerts-migration/migration-preview.png "마이그레이션 미리 보기")

7. 하나 이상의 제공 **전자 메일 주소** 마이그레이션 상태에 대 한 알림을 수 있습니다. 마이그레이션이 완료 된 또는 작업에서 필요한 경우 전자 메일이 전송 됩니다.

8. 클릭할 **마이그레이션을 시작**합니다. 확인 대화 상자에 표시 되는 정보를 읽고 마이그레이션 프로세스를 시작할 준비가 있는지 확인 합니다.

    >[!IMPORTANT]
    > 구독에 대 한 마이그레이션 프로세스를 시작 하면 구독에 대 한 클래식 경고 규칙 편집/만들기 수 없습니다. 그러나 클래식 경고 규칙에는 실행 및 통해 마이그레이션될 때까지 경고를 제공을 계속 합니다. 이 클래식 경고 규칙 및 마이그레이션 중에 만든 새 규칙 간의 충실도 보장 하는 것입니다. 마이그레이션 구독에 대해 완료 되 면 더 이상 클래식 경고 규칙을 사용 하지 있습니다.

    ![마이그레이션 확인](media/alerts-migration/migration-confirm.png "확인 마이그레이션 시작")

9. 로 8 단계에서 제공 하는 전자 메일 주소가 전자 메일을 받게, 마이그레이션을 완료 또는에서 작업 해야 했습니다. 포털에서 마이그레이션 방문 페이지에서 상태를 정기적으로 확인할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**왜 내 구독이 나열 됩니다 안 됨으로 마이그레이션에 대 한 준비?**

마이그레이션 도구는 모든 고객에 게 단계별로 출시 됩니다. 초기 단계, 대부분 또는 모든 구독으로 표시 될 수 있습니다 **마이그레이션 준비가 되지 않은**합니다. 그러나 월 중순으로 모든 구독 마이그레이션 준비가 되어 있어야 합니다.

구독 마이그레이션에 대 한 준비 되 면 구독 소유자 도구의 가용성을 알리는 이메일을 받게 됩니다. 이 알림에 대 한 관심을 가지십시오.

### <a name="who-can-trigger-the-migration"></a>**마이그레이션을 트리거할 수는?**

구독 수준에서 할당 Monitoring Contributor 역할이 있는 사용자 마이그레이션을 트리거할 수 됩니다. 에 대해 자세히 알아보세요 [마이그레이션 프로세스에 대 한 역할 기반 Access Control](alerts-understand-migration.md#who-can-trigger-the-migration)합니다.

### <a name="how-long-is-the-migration-going-to-take"></a>**기간 마이그레이션 것 되려면?**

대부분의 구독에 대 한 마이그레이션 일반적으로 한 시간 이내 완료합니다. 수를 추적할 수 마이그레이션 방문 페이지에서 마이그레이션 진행률을 합니다.  이 시간 동안 하세요 보장할 수 경고 여전히 실행 중인지 클래식 경고 시스템 또는 새 것입니다.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**마이그레이션 중 문제에 직면 하는 경우 어떻게 해야 합니까?**

따르세요 합니다 [문제 해결 가이드 마이그레이션하는 동안 발생할 수 있는 문제에 대 한 수정 단계를 보려면](alerts-understand-migration.md#common-issues-and-remediations)합니다. 마이그레이션을 완료 하려면 모든 작업에 필요한 경우 마이그레이션 중에 제공 된 전자 메일 주소에서 알려드립니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 준비](alerts-prepare-migration.md)
- [마이그레이션 도구 작동 원리를 이해합니다](alerts-understand-migration.md)
