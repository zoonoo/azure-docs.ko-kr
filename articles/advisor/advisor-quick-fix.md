---
title: Advisor의 빠른 수정 권장 사항
description: Advisor에서 빠른 수정을 사용하여 대량 수정 수행
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968586"
---
# <a name="quick-fix-remediation-for-advisor"></a>Advisor의 빠른 수정
**빠른 수정** 을 사용하면 여러 리소스에 대한 권장 사항을 보다 빠르고 쉽게 수정할 수 있습니다. 리소스를 대량으로 수정하는 기능을 제공하며, 대규모 리소스 수정을 통해 구독을 더 빠르게 최적화하는 데 도움이 됩니다.
이 기능은 특정 권장 사항에 대해서만 Azure Portal을 통해 사용할 수 있습니다.


## <a name="steps-to-use-quick-fix"></a>‘빠른 수정’을 사용하는 단계

1. **빠른 수정** 레이블이 있는 권장 사항 목록에서 권장 사항을 클릭합니다.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="권장 사항의 빠른 수정 레이블이 표시된 Azure Advisor 스크린샷":::
   
   ‘이미지의 가격은 예시용입니다.’

2. 권장 사항 세부 정보 페이지에는 이 권장 사항이 적용되는 리소스 목록이 표시됩니다. 권장 사항과 관련해서 수정할 리소스를 모두 선택합니다.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="목록 항목 및 빠른 수정 단추가 강조 표시된 영향을 받는 리소스 창 스크린샷":::
   
   ‘이미지의 가격은 예시용입니다.’

3. 리소스를 선택한 후에는 **빠른 수정** 단추를 클릭하여 대량 수정합니다.

   > [!NOTE]
   > 나열된 리소스 중 일부는 수정할 수 있는 적절한 권한이 없어서 사용하지 못할 수도 있습니다.
   
   > [!NOTE]
   > Advisor에서 언급된 혜택 외에 다른 영향이 있을 경우 정확하고 합리적인 수정 결정에 도움이 되도록 환경에서 알림이 제공됩니다.
   
4. 수정 완료 시 알림을 받게 됩니다. 리소스 목록 뷰에 수정되지 않은 리소스와 선택한 모드의 리소스가 있으면 오류가 표시됩니다.  


## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 뛰어난 운영 권장 사항](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
