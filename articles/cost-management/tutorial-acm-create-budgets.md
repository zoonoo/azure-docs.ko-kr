---
title: 자습서 - Azure 예산 만들기 및 관리 | Microsoft Docs
description: 이 자습서는 사용자가 소비하는 Azure 서비스 비용을 계획하고 설명하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: edb46bc361c515439a93d9c3d0b9987bebe4b1b1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229870"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>자습서: Azure 예산 만들기 및 관리

Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 예산을 사용하여 특정 기간 중 사용자가 소비 또는 구독하는 Azure 서비스를 설명할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 사용자가 만든 예산 임계값이 초과된 경우 알림만 트리거되고 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다.

Cost and usage data is typically available within 12-16 hours and budgets are evaluated against these costs every four hours. Email notifications are normally received within 12-16 hours.

예산은 미래의 만료 날짜를 선택한 경우 동일한 예산 금액에 대해 기간(월별, 분기별 또는 연간)이 끝나면 자동으로 다시 설정됩니다. 동일한 예산 금액으로 다시 설정하므로, 미래 기간에 대해 예산 책정된 통화 금액이 다를 경우 별도의 예산을 만들어야 합니다.

이 자습서의 예제는 Azure EA(Enterprise Agreement) 구독에 대한 예산을 만들고 편집하는 과정을 안내합니다.

Watch the [How to create a budget to monitor your spending with Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video to see how you can create budgets in Azure to monitor spending.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

## <a name="prerequisites"></a>전제 조건

예산은 다양한 유형의 Azure 계정에 대해 지원됩니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 예산을 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다.

 Azure EA 구독의 경우 예산을 보는 읽기 권한이 있어야 합니다. 예산을 만들고 관리하려면 기여자 사용 권한이 있어야 합니다. EA 구독 및 리소스 그룹에 대한 개별 예산을 만들 수 있습니다. 그러나 EA 청구 계정에 대한 예산을 만들 수 없습니다.

The following Azure permissions, or scopes, are supported per subscription for budgets by user and group. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예산을 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 및 Cost Management 기여자 – 자신의 예산을 만들거나, 수정하거나, 삭제할 수 있습니다. 다른 사용자가 만든 예산에 대한 예산 금액을 수정할 수 있습니다.
- 읽기 권한자 및 Cost Management 읽기 권한자 - 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대한 사용 권한을 할당하는 방법에 대한 자세한 내용은 [Cost Management 데이터에 대한 액세스 할당](assign-access-acm-data.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure Portal에서 예산 만들기

월별, 분기별 또는 연간 기간에 대한 Azure 구독 예산을 만들 수 있습니다. Your navigational content in the Azure portal determines whether you create a budget for a subscription or for a management group.

To create or view a budget, open the desired scope in the Azure portal and select **Budgets** in the menu. For example, navigate to **Subscriptions**, select a subscription from the list, and then select **Budgets** in the menu. Use the **Scope** pill to switch to a different scope, like a management group, in Budgets. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

예산을 만들면 예산 대비 현재 지출을 간단하게 보여 줍니다.

**추가**으로 로그온합니다.

![Example showing a list of budgets already created](./media/tutorial-acm-create-budgets/budgets01.png)

In the **Create budget** window, make sure that the scope shown is correct. Choose any filters that you want to add. Filters allow you to create budgets on specific costs, such as resource groups in a subscription or a service like virtual machines. Any filter you can use in cost analysis can also be applied to a budget.

After you've identified your scope and filters, type a budget name. Then, choose a monthly, quarterly or annual budget reset period. This reset period determines the time window that's analyzed by the budget. The cost evaluated by the budget starts at zero at the beginning of each new period. 분기별 예산을 만드는 경우 월별 예산과 동일한 방식으로 진행되지만, 해당 분기에 대한 예산 금액이 분기에 속한 3개월에 고르게 나누어진다는 차이점이 있습니다. An annual budget amount is evenly divided among all 12 months of the calendar year.

종량제, MSDN 또는 Visual Studio 구독이 있는 경우 송장 청구 기간이 달력상 월과 다를 수 있습니다. For those subscription types and resource groups, you can create a budget that's aligned to your invoice period or to calendar months. To create a budget aligned to your invoice period, select a reset period of **Billing month**, **Billing quarter**, or **Billing year**. To create a budget aligned to the calendar month, select a reset period of **Monthly**, **Quarterly**, or **Annually**.

Next, identify the expiration date when the budget becomes invalid and stops evaluating your costs.

Based on the fields chosen in the budget so far, a graph is shown to help you select a threshold to use for your budget. The suggested budget is based on the highest forecasted cost that you might incur in future periods. You can change the budget amount.

![Example showing budget creation with monthly cost data ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

After you configure the budget amount, click **Next** to configure budget alerts. 예산은 1개 이상의 비용 임계값(예산의 %) 및 해당하는 이메일 주소가 필요합니다. 필요에 따라 단일 예산에 최대 5개의 임계값과 5개의 이메일 주소를 포함할 수 있습니다. When a budget threshold is met, email notifications are normally received in less than 20 hours. 알림에 대한 자세한 내용은 [비용 경고 사용](cost-mgt-alerts-monitor-usage-spending.md)을 참조하세요. In the example below, an email alert gets generated when 90% of the budget is reached. If you create a budget with the Budgets API, you can also assign roles to people to receive alerts. Assigning roles to people isn't supported in the Azure portal. For more about the Azure budgets API, see [Budgets API](/rest/api/consumption/budgets).

![Example showing alert conditions](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

예산을 만들면 비용 분석에 표시됩니다. 지출 추세와 비교하여 예산을 보는 것은 [사용자의 비용과 지출을 분석](quick-acm-cost-analysis.md)하기 위해 시작하는 첫 단계 중 하나입니다.

![비용 분석에 표시된 예산 및 지출 예제](./media/tutorial-acm-create-budgets/cost-analysis.png)

앞의 예제에서는 구독에 대한 예산을 만들었습니다. 그러나 리소스 그룹에 대한 예산도 만들 수 있습니다. 리소스 그룹에 대한 예산을 만들려는 경우 **Cost Management + 청구** &gt; **구독**으로 이동 &gt; 구독 선택 > **리소스 그룹** > 리소스 그룹 선택 > **예산** > 예산을 **추가**합니다.

## <a name="trigger-an-action-group"></a>Trigger an action group

When you create or edit a budget for a subscription or resource group scope, you can configure it to call an action group. The action group can perform a variety of different actions when your budget threshold is met. Action Groups are currently only supported for subscription and resource group scopes. For more information about Action Groups, see [Create and manage action groups in the Azure portal](../azure-monitor/platform/action-groups.md). For more information about using budget-based automation with action groups, see [Manage costs with Azure budgets](../billing/billing-cost-management-budget-scenario.md).



To create or update action groups, click **Manage action groups** while you're creating or editing a budget.

![Example of creating a budget to show Manage action groups](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Next, click **Add action group** and create the action group.


![Image of the Add action group box](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

After the action group is created, close the box to return to your budget.

Configure your budget to use your action group when an individual threshold is met. Up to five different thresholds are supported.

![Example showing action group selection for an alert condition](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

The following example shows budget thresholds set to 50%, 75% and 100%. Each is configured to trigger the specified actions within the designated action group.

![Example showing alert conditions configured with various action groups and type of actions](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Budget integration with action groups only works for action groups that have the common alert schema disabled. For more information about disabling the schema, see [How do I enable the common alert schema?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

비용 관리 데이터에 대한 되풀이 내보내기를 만들려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [내보낸 데이터를 만들고 관리](tutorial-export-acm-data.md)
