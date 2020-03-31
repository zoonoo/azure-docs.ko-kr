---
title: 중요한 Azure 관리자 권장 사항 보기
description: Azure Advisor 권장 사항을 보고 필터링하여 노이즈를 줄입니다.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422366"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>중요한 Azure 관리자 권장 사항 보기

Azure Advisor는 Azure 배포를 최적화하는 데 도움이 되는 권장 사항을 제공합니다. Advisor 내에서 권장 사항을 중요한 기능으로만 좁히는 데 도움이 되는 몇 가지 기능에 액세스할 수 있습니다.

## <a name="configure-subscriptions-and-resource-groups"></a>구독 및 리소스 그룹 구성

Advisor는 사용자 및 조직에 중요한 구독 및 리소스 그룹을 선택할 수 있는 기능을 제공합니다. 선택한 구독 및 리소스 그룹에 대한 권장 사항만 표시됩니다. 기본적으로 모두 선택됩니다. 구성 설정은 구독 또는 리소스 그룹에 적용되므로 해당 구독 또는 리소스 그룹에 액세스할 수 있는 모든 사람에게 동일한 설정이 적용됩니다. 구성 설정은 Azure 포털에서 또는 프로그래밍 방식으로 변경할 수 있습니다.

Azure 포털을 변경하려면 다음을 수행하십시오.

1. Azure 포털에서 [Azure 관리자를](https://aka.ms/azureadvisordashboard) 엽니다.

1. 메뉴에서 **구성을** 선택합니다.

   ![어드바이저 구성 메뉴](./media/view-recommendations/configuration.png)

1. 어드바이저 권장 사항을 받으려면 구독 또는 리소스 그룹에 **대한 포함** 열의 확인란을 선택합니다. 상자를 사용하지 않도록 설정하면 해당 구독 또는 리소스 그룹에 대한 구성을 변경할 수 있는 권한이 없을 수 있습니다. [Azure Advisor 의 사용 권한에](permissions.md)대해 자세히 알아봅니다.

1. 변경한 후 하단에서 **적용을** 클릭합니다.

## <a name="filtering-your-view-in-the-azure-portal"></a>Azure 포털에서 보기 필터링

구성 설정은 변경될 때까지 활성 상태로 유지됩니다. 단일 보기에 대한 권장 사항 보기를 제한하려면 Advisor 패널 상단에 제공된 드롭다운을 사용할 수 있습니다. 개요, 고가용성, 보안, 성능, 비용 및 모든 권장 사항 패널에서 보려는 구독, 리소스 유형 및 권장 사항 상태를 선택할 수 있습니다.

   ![어드바이저 필터링 메뉴](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>권장 사항 해제 및 연기

Azure Advisor를 사용하면 단일 리소스에 대한 권장 사항을 해제하거나 연기할 수 있습니다. 권장 사항을 해제하면 수동으로 활성화하지 않으면 다시 표시되지 않습니다. 그러나 권장 사항을 연기하면 권장 사항이 자동으로 다시 활성화된 기간을 지정할 수 있습니다. 연기는 Azure 포털에서 또는 프로그래밍 방식으로 수행할 수 있습니다.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Azure 포털에서 단일 권장 사항 연기 

1. Azure 포털에서 [Azure 관리자를](https://aka.ms/azureadvisordashboard) 엽니다.
1. 추천 카테고리를 선택하여 추천을 확인합니다.
1. 권장 사항 목록에서 권장 사항 선택
1. 연기하거나 해제할 권장 사항에 대해 연기 또는 해제를 선택합니다.

     ![어드바이저 필터링 메뉴](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Azure 포털에서 여러 권장 사항을 연기하거나 해제합니다.

1. Azure 포털에서 [Azure 관리자를](https://aka.ms/azureadvisordashboard) 엽니다.
1. 추천 카테고리를 선택하여 추천을 확인합니다.
1. 권장 사항 목록에서 권장 사항을 선택합니다.
1. 권장 사항을 연기하거나 해제하려는 모든 리소스에 대해 행 왼쪽에 있는 확인란을 선택합니다.
1. 테이블 왼쪽 상단에서 **연기** 또는 **해제를** 선택합니다.

     ![어드바이저 필터링 메뉴](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 추천을 취소하거나 연기하려면 기고자 또는 소유자의 권한이 필요합니다. Azure Advisor의 사용 권한에 대해 자세히 알아보세요.

> [!NOTE]
> 선택 상자를 사용하지 않도록 설정한 경우 권장 사항이 계속 로드중일 수 있습니다. 연기하거나 해제하기 전에 모든 권장 사항이 로드될 때까지 기다려 주십시오.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>연기되거나 해제된 권장 사항 다시 활성화

연기되거나 해제된 권장 사항을 활성화할 수 있습니다. 이 작업은 Azure 포털에서 또는 프로그래밍 방식으로 수행할 수 있습니다. Azure Portal에서 다음을 수행합니다.

1. Azure 포털에서 [Azure 관리자를](https://aka.ms/azureadvisordashboard) 엽니다.

1. 개요 패널의 필터를 **연기로**변경합니다. 그런 다음 어드바이저가 연기되거나 기각된 권장 사항을 표시합니다.

    ![어드바이저 필터링 메뉴](./media/view-recommendations/activate-postponed.png)

1. **연기및** 해제된 권장 사항을 보려면 범주를 **선택합니다.**

1. 권장 사항 목록에서 권장 사항을 선택합니다. 이렇게 하면 이 권장 사항이 연기되거나 해제된 리소스를 표시하기 위해 이미 선택된 **& 해제된** 탭이 있는 권장 사항이 열립니다.

1. 행 끝에서 **활성화를** 클릭합니다. 일단 클릭하면 권장 사항이 해당 리소스에 대해 활성화되므로 이 테이블에서 제거됩니다. 이제 권장 사항이 **활성** 탭에 표시됩니다.
 
     ![어드바이저 필터링 메뉴](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Advisor에서 중요한 권장 사항을 볼 수 있는 방법을 설명합니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요. 

- [Azure Advisor란?](advisor-overview.md)
- [어드바이저 시작](advisor-get-started.md)
- [Azure 어드바이저의 권한](permissions.md)



