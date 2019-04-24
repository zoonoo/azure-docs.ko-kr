---
title: 관심 있는 주제에 Azure Advisor 권장 사항 보기
description: 보기 및 노이즈를 줄이기 위해 Azure Advisor 권장 사항 필터링 합니다.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467909"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>관심 있는 주제에 Azure Advisor 권장 사항 보기

Azure Advisor는 Azure 배포를 최적화 하기 위한 권장 사항을 제공 합니다. 관리자, 내에서 관심 있는 주제에 권장 범위를 좁힐 수 있는 몇 가지 기능에 액세스할 수 있습니다.

## <a name="configure-subscriptions-and-resource-groups"></a>구독 및 리소스 그룹 구성

관리자는 구독 및 조직에 중요 한 리소스 그룹을 선택 하는 기능을 제공 합니다. 구독 및 선택 하는 리소스 그룹에 대 한 권장 구성만 표시 됩니다. 기본적으로 모든 선택 됩니다. 구성 설정은 해당 구독 또는 리소스 그룹에 대 한 액세스 권한이 있는 모든 사용자에 게 동일한 설정을 적용 되므로 구독 또는 리소스 그룹에 적용 됩니다. Azure portal에서 또는 프로그래밍 방식으로 구성 파일을 변경할 수 있습니다.

Azure portal에서 변경 합니다.

1. 오픈 [Azure Advisor](https://aka.ms/azureadvisordashboard) Azure portal에서 합니다.

1. 선택 **구성** 합니다.

   ![Advisor 구성 메뉴](./media/view-recommendations/configuration.png)

1. 상자를 선택 합니다 **Include** Advisor 권장 사항을 수신 하려면 리소스 그룹이 나 구독에 대 한 열입니다. 상자를 사용 하지 않으면 수는 구독 또는 리소스 그룹의 구성을 변경 하도록 권한이 없습니다. 에 대해 자세히 알아보세요 [Azure Advisor에서 권한을](permissions.md)합니다.

1. 클릭 **적용** 변경한 후 맨 아래에 있습니다.

## <a name="filtering-your-view-in-the-azure-portal"></a>Azure portal에서 보기를 필터링합니다.

구성 설정이 변경 될 때까지 활성 유지 됩니다. 단일 보기에 대 한 권장 보기를 제한 하려는 경우에 제공 관리자 패널의 맨 위에 있는 드롭다운을 사용할 수 있습니다. 개요, 고가용성, 보안, 성능, 비용 및 모든 권장 사항 패널에서 구독, 리소스 유형 및 보려는 권장 사항 상태별을 선택할 수 있습니다.

   ![Advisor 필터링 메뉴](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>해제 하 고 권장 사항 연기

Azure Advisor를 사용 하면 해제 하거나 단일 리소스에 대 한 권장 사항을 연기할 수 있습니다. 권장 사항을 해제 하는 경우 표시 되지 않습니다 것 다시 수동으로 활성화 하지 않는 한 합니다. 그러나 권장 사항을 연기는 것이 좋습니다는 자동으로 다시 활성화 하는 기간을 지정할 수 있습니다. Azure portal에서 또는 프로그래밍 방식으로 연기을 수행할 수 있습니다.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Azure portal에서 단일 권장 사항 연기 

1. 오픈 [Azure Advisor](https://aka.ms/azureadvisordashboard) Azure portal에서 합니다.
1. 권장 사항을 보려면 권장 사항 범주를 선택 합니다.
1. 권장 사항 목록에서 권장 사항을 선택합니다
1. 연기 하거나 해제할 권장 사항 연기 또는 해제 선택

     ![Advisor 필터링 메뉴](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>연기 또는 Azure portal에서 여러 권장 사항 해제

1. 오픈 [Azure Advisor](https://aka.ms/azureadvisordashboard) Azure portal에서 합니다.
1. 권장 사항을 보려면 권장 사항 범주를 선택 합니다.
1. 권장 사항 목록에서 권장 사항을 선택 합니다.
1. 연기 하거나, 권장 사항을 해제 하려면 모든 리소스에 대 한 행의 왼쪽에 있는 확인란을 선택 합니다.
1. 선택 **연기** 또는 **해제** 에서 테이블의 왼쪽 위에 있습니다.

     ![Advisor 필터링 메뉴](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 권장 사항 연기 또는 해제 하려면 참가자 또는 소유자 권한이 필요 합니다. Azure Advisor에서 사용 권한에 대 한 자세히 알아봅니다.

> [!NOTE]
> 선택 상자 비활성화 된 경우에 권장 사항을 로드 여전히 수 있습니다. 모든 권장 사항을 연기 하거나 해제 하기 전에 로드 될 때까지 기다리세요.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>연기 또는 해제 된 권장 사항을 다시 활성화

지연 되거나 해제 된 권장 사항을 활성화할 수 있습니다. Azure portal에서 또는 프로그래밍 방식으로이 작업을 수행할 수 있습니다. Azure Portal에서 다음을 수행합니다.

1. 오픈 [Azure Advisor](https://aka.ms/azureadvisordashboard) Azure portal에서 합니다.

1. 개요 패널에서 필터를 변경할 **연기 됨**합니다. 관리자에는 다음 연기 또는 해제 된 권장 사항이 표시 됩니다.

    ![Advisor 필터링 메뉴](./media/view-recommendations/activate-postponed.png)

1. 범주 참조를 선택 **연기 됨** 하 고 **해제** 권장 사항입니다.

1. 권장 사항 목록에서 권장 사항을 선택 합니다. 권장 사항을 사용 하 여 열립니다는 **연기 & 해제** 탭이 이미는이 권장 사항을 연기 되었거나 해제 된 리소스를 표시 하려면 선택 합니다.

1. 클릭할 **활성화** 행의 끝입니다. 클릭 되 면 해당 리소스에 대 한 활성 상태이 고이 테이블에서 제거 하므로 권장이 합니다. 권장 사항이 표시 됩니다. 합니다 **활성** 탭 합니다.
 
     ![Advisor 필터링 메뉴](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Advisor에 집중할 수 있는 권장 사항을 보는 방법을 설명 합니다. Advisor에 대해 자세히 알아보려면 다음을 참조하세요. 

- [Azure Advisor란?](advisor-overview.md)
- [Advisor 시작](advisor-get-started.md)
- [Azure Advisor에 대 한 사용 권한](permissions.md)



