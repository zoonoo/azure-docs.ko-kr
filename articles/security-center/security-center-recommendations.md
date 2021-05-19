---
title: Azure Security Center의 보안 권장 사항
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: da0826a349131d9d5716932abfc603be8103b788
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903545"
---
# <a name="review-your-security-recommendations"></a>보안 권장 사항 검토

이 항목에서는 Azure 리소스를 보호하는 데 도움이 되는 Azure Security Center의 권장 사항을 확인하고 이해하는 방법을 설명합니다.

## <a name="monitor-recommendations"></a>권장 사항 모니터링 <a name="monitor-recommendations"></a>

보안 센터는 리소스의 보안 상태를 분석하여 잠재적인 취약성을 식별합니다. 

1. Security Center의 메뉴에서 **권장 사항** 페이지를 열어 환경에 적용할 수 있는 권장 사항을 확인합니다. 권장 사항은 보안 컨트롤로 그룹화됩니다.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="보안 컨트롤별로 그룹화된 권장 사항" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. 리소스 종류, 심각도, 환경 또는 중요한 기타 기준에 대한 권장 사항을 찾으려면 권장 사항 목록 위의 선택적 필터를 사용합니다.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Azure Security Center 권장 사항 목록을 구체화하는 필터":::

1. 컨트롤을 확장하고 특정 권장 사항을 선택하여 권장 사항 세부 정보 페이지를 표시합니다.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="권장 사항 세부 정보 페이지" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    페이지에는 다음 내용이 포함되어 있습니다.

    1. 지원되는 권장 사항의 경우 맨 위 도구 모음에 다음 단추 중 하나 이상이 표시됩니다.
        - **적용** 및 **거부**([적용/거부 권장 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md) 참조)
        - 기본 정책에 대한 Azure Policy 항목으로 직접 이동하는 **정책 정의 보기**
    1. **심각도 표시기**
    1. **새로 고침 간격**(관련된 경우)
    1. **제외된 리소스 수** 이 권장 사항에 대한 예외가 있는 경우 제외된 리소스의 수를 표시합니다.
    1. **설명** - 문제에 대한 간단한 설명
    1. **수정 단계** - 영향을 받는 리소스에 대한 보안 문제를 해결하는 데 필요한 수동 단계에 대한 설명입니다. **수정** 옵션**이 있는 권장 사항의 경우 제안된 수정 사항을 리소스에 적용하기 전에 **수정 논리 보기** 를 선택할 수 있습니다.
    1. **영향을 받는 리소스** - 리소스가 탭으로 그룹화됩니다.
        - **정상 리소스** – 영향을 받지 않거나 이미 문제를 해결한 관련 리소스입니다.
        - **비정상 리소스** – 여전히 식별된 문제의 영향을 받는 리소스입니다.
        - **해당 없음 리소스** – 권장 사항에서 명확한 답변을 제공할 수 없는 리소스입니다. 해당 없음 탭에는 각 리소스에 대한 이유도 포함되어 있습니다. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="이유가 포함된 해당 없음 리소스":::
    1. 권장 사항을 재구성하거나 논리 앱을 트리거하는 작업 단추입니다.

## <a name="preview-recommendations"></a>미리 보기 권장 사항

**미리 보기** 로 플래그가 지정된 권장 사항은 보안 점수 계산에 포함되지 않습니다.

미리 보기 기간이 종료되면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정해야 합니다.

미리 보기 추천 사항의 예는 다음과 같습니다.

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="미리 보기 플래그가 있는 추천 사항":::
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. 관련 정보는 다음과 같습니다.

- [권장 사항 교정](security-center-remediate-recommendations.md) - Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
- [적용/거부 권장 사항을 사용하여 구성 오류를 방지합니다](prevent-misconfigurations.md).
- [Security Center 트리거에 대한 응답 자동화](workflow-automation.md)--권장 사항에 대한 응답 자동화
- [권장 사항에서 리소스 제외](exempt-resource.md)
- [보안 권장 사항 - 참조 가이드](recommendations-reference.md)