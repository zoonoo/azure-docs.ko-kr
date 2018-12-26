---
title: 자습서 - Azure 예산 만들기 및 관리 | Microsoft Docs
description: 이 자습서는 사용자가 소비하는 Azure 서비스 비용을 계획하고 설명하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: bb8eb4eae192cf0dd3a3555a3b1209705dd83aaf
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583694"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>자습서: Azure 예산 만들기 및 관리

Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 예산을 사용하여 특정 기간 중 사용자가 소비 또는 구독하는 Azure 서비스를 설명할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 사용자가 만든 예산 임계값이 초과된 경우 알림만 트리거되고 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다.

예산은 미래의 만료 날짜를 선택한 경우 동일한 예산 금액에 대해 기간(월별, 분기별 또는 연간)이 끝나면 자동으로 다시 설정됩니다. 동일한 예산 금액으로 다시 설정하므로, 미래 기간에 대해 예산 책정된 통화 금액이 다를 경우 별도의 예산을 만들어야 합니다.

이 자습서의 예제는 Azure EA(Enterprise Agreement) 구독에 대한 예산을 만들고 편집하는 과정을 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

## <a name="prerequisites"></a>필수 조건

예산은 모든 Azure EA 고객에게 제공됩니다. Azure EA 구독 보기 예산에 대한 읽기 권한이 있어야 합니다. 예산을 만들고 관리하려면 기여자 사용 권한이 있어야 합니다. EA 구독 및 리소스 그룹에 대한 개별 예산을 만들 수 있습니다. 그러나 EA 청구 계정에 대한 예산을 만들 수 없습니다.

다음 Azure 사용 권한은 사용자 및 그룹별 예산에 대한 구독에 따라 지원됩니다.

- 소유자 – 구독에 대한 예산을 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 및 Cost Management 기여자 – 자신의 예산을 만들거나, 수정하거나, 삭제할 수 있습니다. 다른 사용자가 만든 예산에 대한 예산 금액을 수정할 수 있습니다.
- 읽기 권한자 및 Cost Management 읽기 권한자 - 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대한 사용 권한을 할당하는 방법에 대한 자세한 내용은 [Cost Management 데이터에 대한 액세스 할당](assign-access-acm-data.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- http://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure Portal에서 예산 만들기

월별, 분기별 또는 연간 기간에 대한 Azure 구독 예산을 만들 수 있습니다. Azure Portal의 탐색 콘텐츠는 사용자가 구독 또는 리소스 그룹에 대한 예산을 만드는지 여부를 결정합니다.

Azure Portal에서 **Cost Management + 청구** &gt; **구독** &gt; 구독 선택 &gt; **예산**으로 이동합니다. 이 예제에서 사용자가 만드는 예산은 사용자가 선택한 구독에 대한 것입니다.

예산을 만들면 예산 대비 현재 지출을 간단하게 보여 줍니다.

**추가**를 클릭합니다.

![Cost Management 예산](./media/tutorial-acm-create-budgets/budgets01.png)

**예산 만들기** 창에서 예산 이름 및 예산 금액을 입력합니다. 그런 다음 월별, 분기별 또는 연간 기간 중에서 선택합니다. 다음으로, 종료 날짜를 선택합니다. 예산은 1개 이상의 비용 임계값(예산의 %) 및 해당하는 이메일 주소가 필요합니다. 필요에 따라 단일 예산에 최대 5개의 임계값과 5개의 이메일 주소를 포함할 수 있습니다. 예산 임계값에 도달하면 메일 알림은 일반적으로 8시간 이내에 수신됩니다.

예로, 월별 4,500달러의 예산을 만들어 보겠습니다. 예산의 90%에 도달하면 이메일 경고가 생성됩니다.

![월별 예산 예](./media/tutorial-acm-create-budgets/monthly-budget01.png)

분기별 예산을 만드는 경우 월별 예산과 동일한 방식으로 진행되지만, 해당 분기에 대한 예산 금액이 분기에 속한 3개월에 고르게 나누어진다는 차이점이 있습니다. 예상할 수 있듯이 연간 예산 금액은 1년 전체 12개월에 고르게 나누어집니다.

예산 대비 현재 지출은 Cost Management가 업데이트된 청구 데이터를 수신할 때마다 업데이트됩니다. 일반적으로, 매일입니다.

![예산 대비 현재 지출](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

예산을 만들면 비용 분석에 표시됩니다. 지출 추세와 비교하여 예산을 보는 것은 [사용자의 비용과 지출을 분석](quick-acm-cost-analysis.md)하기 위해 시작하는 첫 단계 중 하나입니다.

![비용 분석에 표시된 예산](./media/tutorial-acm-create-budgets/cost-analysis.png)

앞의 예제에서는 구독에 대한 예산을 만들었습니다. 그러나 리소스 그룹에 대한 예산도 만들 수 있습니다. 리소스 그룹에 대한 예산을 만들려는 경우 **Cost Management + 청구** &gt; **구독**으로 이동 &gt; 구독 선택 > **리소스 그룹** > 리소스 그룹 선택 > **예산** > 예산을 **추가**합니다.

## <a name="edit-a-budget"></a>예산 편집

권한이 있는 액세스 수준에 따라, 예산을 편집하여 속성을 변경할 수 있습니다. 다음 예제에서는 사용자가 구독에 대한 기여자 권한만 있으므로 일부 속성은 읽기 전용입니다. 현재 **만료 날짜**는 비활성화되어 있으며 설정되면 수정할 수 없습니다.

![예산 편집 - 기여자 권한](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

비용 관리 데이터에 대한 되풀이 내보내기를 만들려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [내보낸 데이터를 만들고 관리](tutorial-export-acm-data.md)
