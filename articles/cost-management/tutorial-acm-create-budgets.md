---
title: 자습서 - Azure 예산 만들기 및 관리 | Microsoft Docs
description: 이 자습서는 사용자가 소비하는 Azure 서비스 비용을 계획하고 설명하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 704aefd68f35ca20f72a2a0c46bf11912c139e65
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490717"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>자습서: Azure 예산 만들기 및 관리

Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 예산을 사용하여 특정 기간 중 사용자가 소비 또는 구독하는 Azure 서비스를 설명할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 사용자가 만든 예산 임계값이 초과된 경우 알림만 트리거되고 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다.

월별 예산은 4시간마다 지출에 대해 평가됩니다. 그러나 데이터 및 사용된 리소스에 대한 알림을 8시간 이내에 사용할 수 있습니다.  

예산은 미래의 만료 날짜를 선택한 경우 동일한 예산 금액에 대해 기간(월별, 분기별 또는 연간)이 끝나면 자동으로 다시 설정됩니다. 동일한 예산 금액으로 다시 설정하므로, 미래 기간에 대해 예산 책정된 통화 금액이 다를 경우 별도의 예산을 만들어야 합니다.

이 자습서의 예제는 Azure EA(Enterprise Agreement) 구독에 대한 예산을 만들고 편집하는 과정을 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

## <a name="prerequisites"></a>필수 조건

예산은 다양한 유형의 Azure 계정에 대해 지원됩니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 예산을 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다.

 Azure EA 구독의 경우 예산을 보는 읽기 권한이 있어야 합니다. 예산을 만들고 관리하려면 기여자 사용 권한이 있어야 합니다. EA 구독 및 리소스 그룹에 대한 개별 예산을 만들 수 있습니다. 그러나 EA 청구 계정에 대한 예산을 만들 수 없습니다.

다음 Azure 사용 권한 또는 범위는 사용자 및 그룹에서 예산에 대 한 구독 당 지원 됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예산을 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 및 Cost Management 기여자 – 자신의 예산을 만들거나, 수정하거나, 삭제할 수 있습니다. 다른 사용자가 만든 예산에 대한 예산 금액을 수정할 수 있습니다.
- 읽기 권한자 및 Cost Management 읽기 권한자 - 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대한 사용 권한을 할당하는 방법에 대한 자세한 내용은 [Cost Management 데이터에 대한 액세스 할당](assign-access-acm-data.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

-  https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure Portal에서 예산 만들기

월별, 분기별 또는 연간 기간에 대한 Azure 구독 예산을 만들 수 있습니다. Azure portal의 탐색 내용을 만든 예산에 대 한 구독 또는 관리 그룹에 있는지 여부를 결정 합니다.

을 만들거나는 예산 범위를 확인 하려면 Azure portal 선택에서 원하는 범위를 엽니다 **예산을** 메뉴에서. 예를 들어, 이동할 **구독**, 목록에서 구독을 선택 하 고 선택한 **예산을** 메뉴에서. 사용 합니다 **범위** 필 다른 범위에서는 예산에는 관리 그룹으로 전환 합니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

예산을 만들면 예산 대비 현재 지출을 간단하게 보여 줍니다.

**추가**를 클릭합니다.

![Azure Portal에 표시된 비용 관리 예산](./media/tutorial-acm-create-budgets/budgets01.png)

**예산 만들기** 창에서 예산 이름 및 예산 금액을 입력합니다. 그런 다음 월별, 분기별 또는 연간 기간 중에서 선택합니다. 다음으로, 종료 날짜를 선택합니다. 예산은 1개 이상의 비용 임계값(예산의 %) 및 해당하는 이메일 주소가 필요합니다. 필요에 따라 단일 예산에 최대 5개의 임계값과 5개의 이메일 주소를 포함할 수 있습니다. 예산 임계값에 도달하면 메일 알림은 일반적으로 8시간 이내에 수신됩니다. 알림에 대한 자세한 내용은 [비용 경고 사용](cost-mgt-alerts-monitor-usage-spending.md)을 참조하세요.

종 량 제, MSDN 또는 Visual Studio 구독이 있는 경우에 청구서는 청구 기간 월에 정렬 되지 않습니다. 구독 및 리소스 그룹의 해당 형식에 대 한 달력 월 또는 송장 기간에 정렬 되는 예산 범위를 만들 수 있습니다. 송장 기간에 정렬 하는 예산 범위를 만들려면 재설정 청구 월, 청구 분기 또는 연도 청구 기간을 선택 합니다. 월에 정렬 하는 예산 범위를 만들려면 재설정 기간 매월, 분기별 또는 매년 선택 합니다.

예로, 월별 4,500달러의 예산을 만들어 보겠습니다. 예산의 90%에 도달하면 이메일 경고가 생성됩니다.

![예산 만들기 상자에 표시된 예제 정보](./media/tutorial-acm-create-budgets/monthly-budget01.png)

분기별 예산을 만드는 경우 월별 예산과 동일한 방식으로 진행되지만, 해당 분기에 대한 예산 금액이 분기에 속한 3개월에 고르게 나누어진다는 차이점이 있습니다. 예상할 수 있듯이 연간 예산 금액은 1년 전체 12개월에 고르게 나누어집니다.

예산 대비 현재 지출은 Cost Management가 업데이트된 청구 데이터를 수신할 때마다 업데이트됩니다. 일반적으로, 매일입니다.

![예산 대비 현재 지출을 보여 주는 예제 정보](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

예산을 만들면 비용 분석에 표시됩니다. 지출 추세와 비교하여 예산을 보는 것은 [사용자의 비용과 지출을 분석](quick-acm-cost-analysis.md)하기 위해 시작하는 첫 단계 중 하나입니다.

![비용 분석에 표시된 예산 및 지출 예제](./media/tutorial-acm-create-budgets/cost-analysis.png)

앞의 예제에서는 구독에 대한 예산을 만들었습니다. 그러나 리소스 그룹에 대한 예산도 만들 수 있습니다. 리소스 그룹에 대한 예산을 만들려는 경우 **Cost Management + 청구** &gt; **구독**으로 이동 &gt; 구독 선택 > **리소스 그룹** > 리소스 그룹 선택 > **예산** > 예산을 **추가**합니다.

## <a name="edit-a-budget"></a>예산 편집

권한이 있는 액세스 수준에 따라, 예산을 편집하여 속성을 변경할 수 있습니다. 다음 예제에서는 사용자가 구독에 대한 기여자 권한만 있으므로 일부 속성은 읽기 전용입니다. 현재 **만료 날짜**는 비활성화되어 있으며 설정되면 수정할 수 없습니다.

![다양한 속성을 변경하는 예산 편집 예제](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>트리거 작업 그룹

만들거나 구독 또는 리소스 그룹 범위에는 예산 범위를 편집할 때 작업 그룹을 호출 하도록 구성할 수 있습니다. 작업 그룹 예산 임계값이 충족 되 면 다양 한 다른 작업을 수행할 수 있습니다. 작업 그룹에 대 한 자세한 내용은 참조 하세요. [만들기 및 Azure portal에서 작업 그룹 관리](../azure-monitor/platform/action-groups.md)합니다. 예산 기반 자동화를 사용 하 여 작업 그룹에 대 한 자세한 내용은 참조 하세요. [예산 Azure를 사용 하 여 비용 관리](../billing/billing-cost-management-budget-scenario.md)합니다.

을 만들거나 작업 그룹을 업데이트 하려면 클릭 **작업 그룹 관리** 만들거나는 예산 범위를 편집 하는 동안.

![관리 작업 그룹을 표시 하는 예산 범위를 만드는 예제](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

다음으로, 클릭 **작업 그룹 추가** 작업 그룹을 만듭니다.


![추가 작업 그룹 상자의 이미지](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

그룹을 만든 작업 후, 예산 돌아가려면 상자를 닫습니다.

개별 임계값을 충족 되 면 작업 그룹을 사용 하 여 예산을 구성 합니다. 서로 다른 임계값을 5 개까지 지원 됩니다.

![경고 조건이 대 한 작업 그룹 선택을 보여 주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

다음 예제에서는 50%, 75% 및 100%로 설정 하는 예산 임계값을 보여 줍니다. 각 트리거는 지정 된 작업 그룹 내에서 지정 된 동작으로 구성 됩니다.

![다양 한 작업 그룹 및 동작의 유형을 구성 하는 경고 조건을 보여 주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

비용 관리 데이터에 대한 되풀이 내보내기를 만들려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [내보낸 데이터 만들기 및 관리](tutorial-export-acm-data.md)
