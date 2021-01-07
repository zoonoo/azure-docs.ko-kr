---
title: Azure Security Center에 관리 그룹 온보딩
description: 제공된 Azure Policy 정의를 사용하여 관리 그룹의 모든 구독에서 Azure Security Center를 사용하도록 설정하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 4ecd436b548c29c520a7538970d4d703cc8488d2
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027570"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>관리 그룹의 모든 구독에서 Security Center 사용

Azure Policy를 사용하여 동일한 MG(관리 그룹) 내의 모든 Azure 구독에서 Azure Security Center를 사용하도록 설정할 수 있습니다. 이는 포털에서 개별적으로 액세스하는 것보다 편리하며, 구독이 다른 소유자에게 속한 경우에도 작동합니다. 

관리 그룹 및 모든 해당 구독을 온보딩하려면 다음을 수행합니다.

1. **보안 관리자** 권한이 있는 사용자가 Azure Policy를 열고, **구독에서 Azure Security Center 사용** 정의를 검색합니다.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="'구독에서 Azure Security Center 사용' Azure Policy 정의":::

1. **할당** 을 선택하고, 범위를 MG 수준으로 설정했는지 확인합니다.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="'구독에서 Azure Security Center 사용' 정의 할당":::

    > [!TIP]
    > 범위 외에는 필수 매개 변수가 없습니다.

1. **수정 작업 만들기** 를 선택하여 Security Center를 사용하도록 설정되지 않은 모든 기존 구독이 온보딩되도록 합니다.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="'구독에서 Azure Security Center 사용' Azure Policy 정의에 대한 수정 작업 만들기":::

1. 정의가 할당되면 다음이 수행됩니다.

    1. Security Center에 아직 등록되지 않은 MG의 모든 구독을 검색합니다.
    1. 해당 구독을 "비준수"로 표시합니다.
    1. 등록된 모든 구독을 "준수"로 표시합니다(Azure Defender의 설정 또는 해제 여부에 관계없이).

    그런 다음, 수정 작업을 통해 비준수 구독에서 Security Center를 무료로 사용하도록 설정합니다.

> [!IMPORTANT]
> 정책 정의는 **기존**  구독에서만 Security Center를 사용하도록 설정합니다. 새로 만든 구독을 등록하려면 규정 준수 탭을 열고, 관련 비규격 구독을 선택하고, 수정 작업을 만듭니다. Security Center를 사용하여 모니터링하려는 하나 이상의 새 구독이 있는 경우 이 단계를 반복하세요.

## <a name="optional-modifications"></a>선택적 수정

Azure Policy 정의를 수정하기 위해 선택할 수 있는 다양한 방법이 있습니다. 

- **규정 준수를 다르게 정의** - 이 정책은 아직 Security Center에 등록되지 않은 MG의 모든 구독을 "비준수"로 분류합니다. Azure Defender를 사용하지 않는 모든 구독으로 설정하도록 선택할 수 있습니다.

    제공된 정의는 아래 '가격 책정' 설정 중 *하나* 를 준수로 정의합니다. '표준' 또는 '체험'으로 설정된 구독은 규정을 준수함을 의미합니다.

    > [!TIP]
    > Azure Defender 플랜을 사용하도록 설정되면 '표준' 설정에 있는 것으로 설명됩니다. 사용하지 않도록 설정되면 '체험'입니다. [Azure Defender 플랜](security-center-pricing.md)에 대해 자세히 알아보세요.

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    이를 다음으로 변경하면 '표준'으로 설정된 구독만 준수로 분류됩니다.

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Security Center를 사용하도록 설정할 때 적용할 일부 Azure Defender 플랜 정의** - 이 정책을 사용하면 선택적 Azure Defender 플랜을 사용하지 않는 Security Center를 사용하도록 설정합니다. 하나 이상의 설정을 사용하도록 선택할 수 있습니다.

    제공된 정의의 `deployment` 섹션에는 `pricingTier` 매개 변수가 있습니다. 이는 기본적으로 `free`로 설정되지만 수정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계:

이제 전체 관리 그룹이 온보딩되었으므로 Azure Defender의 고급 보호를 사용하도록 설정합니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](security-center-pricing.md)
