---
title: 자습서 - Azure 예산 만들기 및 관리 | Microsoft Docs
description: 이 자습서는 사용자가 소비하는 Azure 서비스 비용을 계획하고 설명하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8d8f796fa7db9cab5bcac88a293dd1b98707a571
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025765"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>자습서: Azure 예산 만들기 및 관리

Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 예산을 사용하여 특정 기간 중 사용자가 소비 또는 구독하는 Azure 서비스를 설명할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 사용자가 만든 예산 임계값이 초과된 경우 알림만 트리거되고 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다.

비용 및 사용 현황 데이터는 일반적으로 12-16 시간 이내에 사용할 수 있으며 예산 비용은 4 시간 마다 계산 됩니다. 전자 메일 알림은 일반적으로 12-16 시간 이내에 수신 됩니다.

예산은 미래의 만료 날짜를 선택한 경우 동일한 예산 금액에 대해 기간(월별, 분기별 또는 연간)이 끝나면 자동으로 다시 설정됩니다. 동일한 예산 금액으로 다시 설정하므로, 미래 기간에 대해 예산 책정된 통화 금액이 다를 경우 별도의 예산을 만들어야 합니다.

이 자습서의 예제는 Azure EA(Enterprise Agreement) 구독에 대한 예산을 만들고 편집하는 과정을 안내합니다.

예산을 [만드는 방법](https://www.youtube.com/watch?v=ExIVG_Gr45A) 을 시청 하 Azure Cost Management 비디오를 사용 하 여 지출을 모니터링 하 고 Azure에서 예산을 만들어 지출을 모니터링 하는 방법을 확인 하세요.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

## <a name="prerequisites"></a>사전 요구 사항

예산은 다양한 유형의 Azure 계정에 대해 지원됩니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 예산을 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다.

 Azure EA 구독의 경우 예산을 보는 읽기 권한이 있어야 합니다. 예산을 만들고 관리하려면 기여자 사용 권한이 있어야 합니다. EA 구독 및 리소스 그룹에 대한 개별 예산을 만들 수 있습니다. 그러나 EA 청구 계정에 대한 예산을 만들 수 없습니다.

다음 Azure 권한 또는 범위는 사용자 및 그룹별 예산에 대 한 구독 당 지원 됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예산을 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 및 Cost Management 기여자 – 자신의 예산을 만들거나, 수정하거나, 삭제할 수 있습니다. 다른 사용자가 만든 예산에 대한 예산 금액을 수정할 수 있습니다.
- 읽기 권한자 및 Cost Management 읽기 권한자 - 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대한 사용 권한을 할당하는 방법에 대한 자세한 내용은 [Cost Management 데이터에 대한 액세스 할당](assign-access-acm-data.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure Portal에서 예산 만들기

월별, 분기별 또는 연간 기간에 대한 Azure 구독 예산을 만들 수 있습니다. Azure Portal의 탐색 콘텐츠는 구독 또는 관리 그룹에 대 한 예산 생성 여부를 결정 합니다.

예산을 만들거나 보려면 Azure Portal에서 원하는 범위를 열고 메뉴에서 **예산** 을 선택 합니다. 예를 들어 **구독**으로 이동 하 여 목록에서 구독을 선택한 다음 메뉴에서 **예산** 을 선택 합니다. 약 **범위** 를 사용 하 여 예산에서 관리 그룹과 같은 다른 범위로 전환 합니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

예산을 만들면 예산 대비 현재 지출을 간단하게 보여 줍니다.

**추가**를 클릭합니다.

![이미 생성 된 예산 목록을 보여 주는 예제](./media/tutorial-acm-create-budgets/budgets01.png)

**예산 만들기** 창에서 표시 된 범위가 올바른지 확인 합니다. 추가 하려는 필터를 선택 합니다. 필터를 사용 하면 구독에 있는 리소스 그룹 또는 가상 머신과 같은 서비스와 같은 특정 비용으로 예산을 만들 수 있습니다. 비용 분석에 사용할 수 있는 모든 필터를 예산에 적용할 수도 있습니다.

범위 및 필터를 확인 한 후에는 예산 이름을 입력 합니다. 그런 다음 월별, 분기별 또는 연간 예산 다시 설정 기간을 선택 합니다. 이 다시 설정 기간에 따라 예산에 따라 분석 되는 기간이 결정 됩니다. 예산에 의해 평가 되는 비용은 각각의 새 기간이 시작 될 때 0부터 시작 합니다. 분기별 예산을 만드는 경우 월별 예산과 동일한 방식으로 진행되지만, 해당 분기에 대한 예산 금액이 분기에 속한 3개월에 고르게 나누어진다는 차이점이 있습니다. 연간 예산 금액은 해당 월의 12 개월 모두 균등 하 게 분할 됩니다.

종량제, MSDN 또는 Visual Studio 구독이 있는 경우 송장 청구 기간이 달력상 월과 다를 수 있습니다. 이러한 구독 유형 및 리소스 그룹의 경우 청구서 기간이 나 달력 월에 맞춘 예산을 만들 수 있습니다. 청구서 기간에 맞게 조정 된 예산을 만들려면 **청구 월**, **청구 분기**또는 **청구 연도의**다시 설정 기간을 선택 합니다. 월에 정렬 된 예산을 만들려면 **월별**, **분기별**또는 **매년**다시 설정 기간을 선택 합니다.

그런 다음 예산이 유효 하지 않게 되는 만료 날짜를 확인 하 고 비용 평가를 중지 합니다.

지금 까지의 예산에서 선택한 필드에 따라 예산에 사용할 임계값을 선택 하는 데 도움이 되는 그래프가 표시 됩니다. 제안 된 예산은 향후 기간에 발생할 수 있는 가장 높은 예상 비용을 기준으로 합니다. 예산 금액을 변경할 수 있습니다.

![월별 비용 데이터로 예산 생성을 보여 주는 예 ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

예산 금액을 구성한 후 **다음** 을 클릭 하 여 예산 경고를 구성 합니다. 예산은 1개 이상의 비용 임계값(예산의 %) 및 해당하는 이메일 주소가 필요합니다. 필요에 따라 단일 예산에 최대 5개의 임계값과 5개의 이메일 주소를 포함할 수 있습니다. 예산 임계값이 충족 되 면 전자 메일 알림은 일반적으로 20 시간 이내에 수신 됩니다. 알림에 대한 자세한 내용은 [비용 경고 사용](cost-mgt-alerts-monitor-usage-spending.md)을 참조하세요. 아래 예제에서는 예산의 90%에 도달 하면 전자 메일 경고가 생성 됩니다.

![경고 조건을 보여 주는 예제](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

예산을 만들면 비용 분석에 표시됩니다. 지출 추세와 비교하여 예산을 보는 것은 [사용자의 비용과 지출을 분석](quick-acm-cost-analysis.md)하기 위해 시작하는 첫 단계 중 하나입니다.

![비용 분석에 표시된 예산 및 지출 예제](./media/tutorial-acm-create-budgets/cost-analysis.png)

앞의 예제에서는 구독에 대한 예산을 만들었습니다. 그러나 리소스 그룹에 대한 예산도 만들 수 있습니다. 리소스 그룹에 대한 예산을 만들려는 경우 **Cost Management + 청구** &gt; **구독**으로 이동 &gt; 구독 선택 > **리소스 그룹** > 리소스 그룹 선택 > **예산** > 예산을 **추가**합니다.

## <a name="trigger-an-action-group"></a>작업 그룹 트리거

구독 또는 리소스 그룹 범위에 대 한 예산을 만들거나 편집 하는 경우 작업 그룹을 호출 하도록 구성할 수 있습니다. 작업 그룹은 예산 임계값이 충족 될 때 다양 한 작업을 수행할 수 있습니다. 작업 그룹에 대 한 자세한 내용은 [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조 하세요. 작업 그룹에서 예산 기반 자동화를 사용 하는 방법에 대 한 자세한 내용은 [Azure 예산으로 비용 관리](../billing/billing-cost-management-budget-scenario.md)를 참조 하세요.

작업 그룹을 만들거나 업데이트 하려면 예산을 만들거나 편집 하는 동안 **작업 그룹 관리** 를 클릭 합니다.

![관리 작업 그룹을 표시 하는 예산을 만드는 예](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


그런 다음 **작업 그룹 추가** 를 클릭 하 고 작업 그룹을 만듭니다.


![작업 그룹 추가 상자의 이미지](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

작업 그룹을 만든 후에는 상자를 닫아 예산으로 돌아갑니다.

개별 임계값이 충족 될 때 작업 그룹을 사용 하도록 예산을 구성 합니다. 최대 5 개의 서로 다른 임계값이 지원 됩니다.

![경고 조건에 대 한 작업 그룹 선택을 보여 주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

다음 예제에서는 50%, 75% 및 100%로 설정 된 예산 임계값을 보여 줍니다. 각는 지정 된 작업 그룹 내에서 지정 된 작업을 트리거하기 위해 구성 됩니다.

![다양 한 작업 그룹 및 동작 유형을 사용 하 여 구성 된 경고 조건을 보여 주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

비용 관리 데이터에 대한 되풀이 내보내기를 만들려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [내보낸 데이터를 만들고 관리](tutorial-export-acm-data.md)
