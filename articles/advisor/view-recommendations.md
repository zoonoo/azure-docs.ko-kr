---
title: 사용자에게 의미 있는 Azure Advisor 추천 보기
description: 노이즈를 줄이기 위해 Azure Advisor 추천을 확인하고 필터링합니다.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986871"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>사용자에게 의미 있는 Azure Advisor 추천 보기

Azure Advisor는 Azure 배포를 최적화하는 데 도움이 되는 추천을 제공합니다. Advisor 내에서 사용자와 관련이 있는 추천만 표시되도록 추천의 범위를 좁히는 데 도움이 되는 몇 가지 기능에 액세스할 수 있습니다.

## <a name="configure-subscriptions-and-resource-groups"></a>구독 및 리소스 그룹 구성

Advisor에서 사용자와 조직에 중요한 구독 및 리소스 그룹을 선택할 수 있습니다. 선택한 구독 및 리소스 그룹에 대한 추천만 표시됩니다. 기본적으로는 모든 제품이 선택되어 있습니다. 구성 설정은 구독 또는 리소스 그룹에 적용되므로 해당 구독 또는 리소스 그룹에 대한 액세스 권한이 있는 모든 사용자에게 동일한 설정이 적용됩니다. 구성 설정은 Azure Portal에서 또는 프로그래밍 방식으로 변경할 수 있습니다.

Azure Portal에서 변경하려면 다음을 수행합니다.

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.

1. 메뉴에서 **구성** 을 선택합니다.

   ![Advisor 구성 메뉴](./media/view-recommendations/configuration.png)

1. Advisor 추천을 받으려면 구독 또는 리소스 그룹에 대한 **포함** 열의 확인란을 선택합니다. 이 확인란을 사용할 수 없다면 해당 구독 또는 리소스 그룹에 대한 구성을 변경할 수 있는 권한이 없을 수 있습니다. [Azure Advisor의 권한](permissions.md)에 대해 자세히 알아보세요.

1. 변경을 수행한 후 아래쪽에서 **적용** 을 클릭합니다.

## <a name="filtering-your-view-in-the-azure-portal"></a>Azure Portal에서 보기 필터링

구성 설정은 변경할 때까지 활성 상태로 유지됩니다. 추천 보기를 단일 보기로 제한하려면 Advisor 패널의 맨 위에 제공된 드롭다운 목록을 사용하면 됩니다. 개요, 고가용성, 보안, 성능, 비용 및 모든 추천 패널에서 보려는 구독, 리소스 종류 및 추천 상태를 선택할 수 있습니다.

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="필터링 옵션을 보여 주는 Azure Advisor의 스크린샷":::

## <a name="dismissing-and-postponing-recommendations"></a>추천 해제 및 연기

Azure Advisor를 사용하면 단일 리소스에 대한 추천을 해제하거나 연기할 수 있습니다. 추천을 해제하는 경우 수동으로 활성화하지 않으면 다시 표시되지 않습니다. 그러나 추천을 연기하면 기간을 지정하여 해당 기간 후에 추천이 자동으로 다시 활성화되도록 할 수 있습니다. Azure Portal에서 연기하거나 프로그래밍 방식으로 연기할 수 있습니다.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Azure Portal에서 단일 추천 연기 

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.
1. 추천 범주를 선택하여 추천을 봅니다.
1. 추천 목록에서 추천을 선택합니다.
1. 연기하거나 해제하려는 추천에 대해 연기 또는 해제 선택

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="단일 추천에 대한 연기 및 해제 작업과 선택 열이 강조 표시된 Managed Disks 사용 창의 스크린샷":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Azure Portal에서 여러 추천을 연기하거나 해제합니다.

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.
1. 추천을 보려면 추천 범주를 선택합니다.
1. 추천 목록에서 추천을 선택합니다.
1. 연기하려는 모든 리소스 행의 왼쪽에 있는 확인란을 선택하고 추천을 해제합니다.
1. 테이블의 왼쪽 위에서 **연기** 또는 **해제** 를 선택합니다.

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="표 왼쪽 위의 연기 및 해제 작업과 선택 열이 강조 표시된 Managed Disks 사용 창의 스크린샷":::

> [!NOTE]
> 추천을 해제하거나 연기하려면 참가자 또는 소유자 권한이 있어야 합니다. Azure Advisor의 권한에 대해 자세히 알아보세요.

> [!NOTE]
> 선택 상자를 사용할 수 없는 경우에도 추천이 로드될 수 있습니다. 모든 추천이 로드 될 때까지 기다렸다가 연기하거나 해제하세요.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>연기되거나 해제된 추천 다시 활성화

연기되거나 해제된 추천을 활성화할 수 있습니다. 이 작업은 Azure Portal에서 수행하거나 프로그래밍 방식으로 수행할 수 있습니다. Azure Portal에서 다음을 수행합니다.

1. Azure Portal에서 [Azure Advisor](https://aka.ms/azureadvisordashboard) 를 엽니다.

1. 개요 패널의 필터를 **연기됨** 으로 변경합니다. 그러면 Advisor에서 연기되거나 해제된 추천을 표시합니다.

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="연기된 드롭다운 메뉴가 선택된 Azure Advisor 창의 스크린샷":::

1. 범주를 선택하여 **연기** 되거나 **해제** 된 추천을 확인합니다.

1. 추천 목록에서 추천을 선택합니다. 그러면 **연기됨 및 해제됨** 탭이 이미 선택되어 있는 추천이 열려 이 추천이 연기되거나 해제된 리소스를 표시합니다.

1. 행 끝에 있는 **활성화** 를 클릭합니다. 클릭하면 해당 리소스에 대한 추천이 활성화되므로 이 표에서 제거됩니다. 이제 이 추천이 **활성** 탭에 표시됩니다.
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="연기됨 및 해제됨 탭과 활성화 작업이 강조 표시된 일시 삭제 사용 창의 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Advisor에서 사용자와 관련이 있는 추천을 볼 수 있는 방법을 설명합니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요. 

- [Azure Advisor란?](advisor-overview.md)
- [Advisor 시작](advisor-get-started.md)
- [Azure Advisor의 권한](permissions.md)



