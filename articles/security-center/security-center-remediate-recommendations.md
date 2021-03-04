---
title: Azure Security Center의 권장 사항 수정 | Microsoft Docs
description: 이 문서에서는 Azure Security Center의 권장 사항에 응답 하 여 리소스를 보호 하 고 보안 정책을 충족 하는 방법을 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 6222491d35317e549a3d21f29395b9c25813ea9f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100358"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center의 권장 사항 교정

권장 사항은 리소스의 보안을 강화 하는 방법에 대 한 제안을 제공 합니다. 권장 구성에서 제공 하는 수정 단계를 수행 하 여 권장 사항을 구현할 수 있습니다.

## <a name="remediation-steps"></a>수정 단계 <a name="remediation-steps"></a>

모든 권장 사항을 검토 한 후 먼저 수정할 항목을 결정 합니다. 보안 점수를 높이기 위해 가장 높은 잠재력으로 보안 제어의 우선 순위를 지정 하는 것이 좋습니다.

1. 목록에서 권장 사항을 선택 합니다.

1. **수정 단계** 섹션의 지침을 따릅니다. 각 권장 사항에는 고유한 지침 집합이 있습니다. 다음 스크린샷에서는 HTTPS를 통한 트래픽만 허용 하도록 응용 프로그램을 구성 하는 수정 단계를 보여 줍니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="권장 사항에 대 한 수동 수정 단계" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. 완료 되 면 문제가 해결 되었는지 알려 주는 알림이 표시 됩니다.

## <a name="quick-fix-remediation"></a>빠른 수정

업데이트를 간소화 하 고 환경 보안을 개선 하기 위해 (그리고 보안 점수를 늘리기) 많은 권장 사항에 빠른 수정 옵션이 포함 되어 있습니다.

빠른 픽스를 사용 하면 여러 리소스에 대 한 권장 사항을 빠르게 수정할 수 있습니다.

> [!TIP]
> 빠른 수정 솔루션은 특정 권장 사항에 대해서만 사용할 수 있습니다. 사용 가능한 빠른 수정이 있는 권장 사항을 찾으려면 권장 사항 목록에 대해 **응답 작업** 필터를 사용 합니다.
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="권장 사항 목록 위의 필터를 사용 하 여 빠른 수정 옵션이 있는 권장 사항을 찾습니다.":::

빠른 수정 솔루션을 구현 하려면 다음을 수행 합니다.

1. **빠른 수정** 이 있는 권장 사항 목록에서 레이블, 권장 사항을 선택 합니다.

    [![빠른 수정을 선택 합니다.](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. **비정상 리소스** 탭에서 권장 구성을 구현 하려는 리소스를 선택 하 고 **재구성** 을 선택 합니다.

    > [!NOTE]
    > 나열 된 리소스 중 일부를 수정할 수 있는 적절 한 권한이 없기 때문에 일부 리소스를 사용 하지 못할 수 있습니다.

1. 확인 상자에서 수정 세부 정보 및 의미를 읽습니다.

    ![빠른 수정](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > 수정을 클릭 한 후 열리는 **리소스 재구성** 창에서 회색 상자에 의미가 **나열 됩니다.** 빠른 수정 수정을 진행할 때 발생 하는 변경 내용을 나열 합니다.

1. 필요한 경우 관련 매개 변수를 삽입 하 고 수정를 승인 합니다.

    > [!NOTE]
    > 수정이 완료 되 면 **정상 리소스** 탭에서 리소스를 확인 하는 데 몇 분 정도 걸릴 수 있습니다. 수정 작업을 보려면 [활동 로그](#activity-log)를 확인 합니다.

1. 작업이 완료 되 면 재구성에 성공 하면 알려 주는 알림이 표시 됩니다.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>활동 로그의 빠른 수정 재구성 로깅 <a name="activity-log"></a>

수정 작업은 템플릿 배포 또는 REST 패치 API 호출을 사용 하 여 리소스에 대 한 구성을 적용 합니다. 이러한 작업은 [Azure 활동 로그](../azure-resource-manager/management/view-activity-logs.md)에 기록 됩니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center의 권장 사항을 수정 하는 방법을 보여 줍니다. Security Center에 대한 자세한 내용은 다음 페이지를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -Azure 구독 및 리소스 그룹에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
* [보안 정책, 이니셔티브 및 권장 사항은 무엇 인가요?](security-policy-concept.md)