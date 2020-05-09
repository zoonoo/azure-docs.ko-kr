---
title: 빠른 시작 - Azure Resource Manager 템플릿을 사용하여 예산 만들기
description: Azure Resource Manager 템플릿을 사용하여 예산을 작성하는 방법을 보여주는 빠른 시작입니다.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: subject-armqs
ms.openlocfilehash: de24895334ec4c864e6daae84a6aab47a47d7b9b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103636"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 사용하여 예산 만들기

Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 예산을 사용하여 특정 기간 중 사용자가 소비 또는 구독하는 Azure 서비스를 설명할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 사용자가 만든 예산 임계값을 초과하면 알림이 트리거됩니다. 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다. 이 빠른 시작에서는 Resource Manager 템플릿을 사용하여 예산을 작성하는 방법을 보여줍니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure Resource Manager 템플릿은 EA(기업계약)용 Azure 구독만 지원합니다. 다른 구독 유형은 템플릿에서 지원되지 않습니다.

예산을 만들고 관리하려면 기여자 사용 권한이 있어야 합니다. EA 구독 및 리소스 그룹에 대한 개별 예산을 만들 수 있습니다. 그러나 EA 청구 계정에 대한 예산을 만들 수 없습니다. Azure EA 구독의 경우 예산을 보는 읽기 권한이 있어야 합니다.

예산을 작성한 후에는 적어도 Azure 계정에 대한 읽기 권한이 있어야만 예산을 볼 수 있습니다.

새 구독이 있는 경우 즉시 예산을 만들거나 다른 Cost Management 기능을 사용할 수 없습니다. 모든 Cost Management 기능을 사용하려면 최대 48시간이 걸릴 수 있습니다.

다음 Azure 사용 권한 또는 범위는 사용자 및 그룹별 예산에 대한 구독에 따라 지원됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예산을 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 및 Cost Management 기여자 – 자신의 예산을 만들거나, 수정하거나, 삭제할 수 있습니다. 다른 사용자가 만든 예산에 대한 예산 금액을 수정할 수 있습니다.
- 읽기 권한자 및 Cost Management 읽기 권한자 - 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대한 사용 권한을 할당하는 방법에 대한 자세한 내용은 [Cost Management 데이터에 대한 액세스 할당](assign-access-acm-data.md)을 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/create-budget)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json":::

템플릿에 다음과 같은 Azure 리소스 하나가 정의되어 있습니다.

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Azure 예산을 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 이 템플릿은 예산을 만듭니다.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json"><img src="./media/quick-create-budget-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. 다음 값을 선택하거나 입력합니다.

   [![Resource Manager 템플릿, 예산 만들기, 배포 포털](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹의 고유한 이름을 입력한 다음, **확인**을 클릭합니다.
    * **위치**: 위치를 선택합니다. 예: **미국 중부**
    * **예산 이름**: 예산의 이름을 입력합니다. 리소스 그룹 내에서 고유해야 합니다. 영숫자, 밑줄 및 하이픈만 허용됩니다.
    * **금액**: 예산을 추적하는 데 드는 총 비용 또는 사용량을 입력합니다.
    * **예산 범주**: 예산 범주를 선택합니다. 예산에서 **비용** 또는 **사용량** 중에 무엇을 추적할지 선택합니다.
    * **시간 조직**: 예산이 적용되는 시간을 입력합니다. 허용되는 값은 매월, 매분기 또는 매년입니다. 시간 조직이 끝나면 예산이 초기화됩니다.
    * **시작 날짜**: 해당 월의 1일을 YYYY-MM-DD 형식의 시작 날짜로 입력합니다. 미래의 시작 날짜는 오늘부터 3개월 이내여야 합니다. 시간 조직 기간을 사용하여 과거의 시작 날짜를 지정할 수 있습니다.
    * **종료 날짜**: 예산의 종료 날짜를 YYYY-MM-DD 형식으로 입력합니다. 입력하지 않으면 기본값인 시작 날짜로부터 10년으로 설정됩니다.
    * **연산자**: 비교 연산자를 선택합니다. 가능한 값은 EqualTo, GreaterThan 또는 GreaterThanOrEqualTo입니다.
    * **임계값**: 알림의 임계값을 입력합니다. 비용이 임계값을 초과하면 알림이 전송됩니다. 항상 백분율이며 0~1000 사이여야 합니다.
    * **연락처 이메일** 임계값을 초과하면 예산 알림을 보낼 이메일 주소 목록을 입력합니다. 필요한 형식은 `["user1@domain.com","user2@domain.com"]`입니다.
    * **연락처 역할** 임계값을 초과하면 예산 알림을 보낼 연락처 역할 목록을 입력합니다. 기본값은 소유자, 기여자 및 읽기 권한자입니다. 필요한 형식은 `["Owner","Contributor","Reader"]`입니다.
    * **연락처 그룹** 임계값을 초과하면 예산 알림을 보낼 작업 그룹 목록을 입력합니다. 문자열 배열을 허용합니다. 필요한 형식은 `["Action Group Name1","Action Group Name2"]`입니다. 작업 그룹을 사용하지 않으려면 `[]`를 입력합니다.
    * **리소스 필터** 리소스의 필터 목록을 입력합니다. 필요한 형식은 `["Resource Filter Name1","Resource Filter Name2"]`입니다. 필터를 적용하지 않으려면 `[]`를 입력합니다. 리소스 필터를 입력하는 경우에는 **미터 필터** 값도 입력해야 합니다.
    * **미터 필터** 미터 필터 목록을 입력합니다. 예산 범주로 **사용량**을 사용하는 예산에는 필수입니다. 필요한 형식은 `["Meter Filter Name1","Meter Filter Name2"]`입니다. **리소스 필터**를 입력하지 않았으면 `[]`를 입력합니다.
    * **위에 명시된 사용 약관에 동의함**: 선택합니다.

3. **구매**를 선택합니다. 예산이 성공적으로 배포되면 다음과 같이 알림을 받게 됩니다.

   ![Resource Manager 템플릿, 예산, 배포 포털 알림](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 템플릿을 알아보려면 [템플릿 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

Azure Portal에서 **Cost Management + 청구** > 범위 선택 > **예산**으로 이동하여 예산이 작성되었는지 확인할 수 있습니다. 또는 다음 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 예산을 볼 수 있습니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 배포를 위한 Azure 예산을 만들었습니다. Azure Cost Management 및 청구와 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서를 계속 진행하세요.

- [Cost Management 및 청구](../cost-management-billing-overview.md) 개요 읽기
- Azure Portal에서 [예산 만들기](tutorial-acm-create-budgets.md)
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기