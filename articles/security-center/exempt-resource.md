---
title: Azure Security Center 보안 권장 사항 및 보안 점수에서 리소스 제외
description: 보안 권장 사항 및 보안 점수에서 리소스를 제외 하는 방법을 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 87c16207f312479dcfe083ad9494d75b3538e18c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532553"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>권장 사항 및 보안 점수에서 리소스 제외

모든 보안 팀의 핵심 우선 순위는 분석가가 조직에 중요 한 작업 및 인시던트에 집중할 수 있도록 하려는 것입니다. Security Center에는 가장 높은 우선 순위를 지정 하는 정보를 사용자 지정 하 고 보안 점수가 조직의 보안 결정에 대 한 올바른 반영 인지 확인 하는 많은 기능이 있습니다. 제외 리소스는 이러한 기능 중 하나입니다.

Azure Security Center에서 보안 권장 사항을 조사할 때 가장 먼저 검토 하는 정보 중 하나는 영향을 받는 리소스 목록입니다.

경우에 따라 포함 하지 않아야 하는 리소스가 나열 됩니다. Security Center에서 추적하지 않는 프로세스를 통해 수정되었을 수 있습니다. 또는 조직에서 특정 리소스에 대한 위험을 감수하기로 결정했을 수도 있습니다. 

이러한 경우 예외 규칙을 만들고 리소스를 나중에 비정상 리소스로 나열 하지 않고 보안 점수에 영향을 주지 않도록 할 수 있습니다. 

리소스는 적용 되지 않는 것으로 나열 되 고 이유는 선택한 근거를 사용 하 여 "제외 됨"으로 표시 됩니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기|
|가격 책정:|Azure Defender 고객에 게 추가 비용 없이 제공 되는 프리미엄 Azure 정책 기능입니다. 다른 사용자에 게는 나중에 요금이 부과 될 수 있습니다.|
|필요한 역할 및 권한:|예외를 만들기 위한 **구독 소유자** 또는 **정책 기여자**<br>규칙을 만들려면 Azure Policy에서 정책을 편집할 수 있는 권한이 필요 합니다.<br>[Azure Policy에서 AZURE RBAC 사용 권한](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)에 대해 자세히 알아보세요.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||


## <a name="create-an-exemption-rule"></a>예외 규칙 만들기

1. 비정상 리소스 목록에서 제외 하려는 리소스에 대 한 줄임표 메뉴 ("...")를 선택 합니다.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="상황에 맞는 메뉴에서 예외 옵션 만들기":::

    예외 만들기 창이 열립니다.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="상황에 맞는 메뉴에서 예외 옵션 만들기":::

1. 조건을 입력 하 고이 리소스를 제외 해야 하는 이유를 선택 합니다.
    - **완화** 됨-이 문제는 제안 되는 것과 다른 도구나 프로세스에서 처리 되기 때문에 리소스와 관련이 없습니다.
    - **면제** -이 리소스에 대 한 위험 수락
1. **저장**을 선택합니다.
1. 잠시 후 (최대 24 시간이 걸릴 수 있음):
    - 리소스는 보안 점수에 영향을 주지 않습니다.
    - 리소스는 권장 사항 세부 정보 페이지의 **해당 없음** 탭에 나열 됩니다.
    - 권장 사항 세부 정보 페이지의 맨 위에 있는 정보 스트립은 제외 된 리소스의 수를 나열 합니다.
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="상황에 맞는 메뉴에서 예외 옵션 만들기":::

1. 제외 된 리소스를 검토 하려면 **해당 없음** 탭을 엽니다.

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="상황에 맞는 메뉴에서 예외 옵션 만들기":::

    각 예외에 대 한 이유는 테이블 (1)에 포함 됩니다.

    예외를 수정 하거나 삭제 하려면 (2)와 같이 줄임표 메뉴 ("...")를 선택 합니다.


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>구독에 대 한 모든 예외 규칙 검토

예외 규칙은 Azure policy를 사용 하 여 정책 할당에서 리소스에 대 한 예외를 만듭니다.

Azure Policy를 사용 하 여 **예외** 페이지에서 모든 예외를 추적할 수 있습니다.

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="상황에 맞는 메뉴에서 예외 옵션 만들기":::



## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수에 영향을 주지 않도록 권장 구성에서 리소스를 제외 하는 방법을 배웠습니다. 보안 점수에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Security Center의 보안 점수](secure-score-security-controls.md)