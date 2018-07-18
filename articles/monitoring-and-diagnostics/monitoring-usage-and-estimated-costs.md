---
title: Azure Monitor의 모니터링 사용량 및 예상 비용
description: Azure Monitor의 모니터링 사용량 및 예상 비용 프로세스 개요 페이지
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.component: ''
ms.openlocfilehash: edfcc244105403ae33251777c560d4cc21dfe5cb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264285"
---
# <a name="monitoring-usage-and-estimated-costs"></a>모니터링 사용량 및 예상 비용

Azure Portal의 모니터 허브에서 **사용량 및 예상 비용** 페이지는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 등과 같은 핵심 모니터링 기능의 사용량을 설명합니다. 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 상품을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. 드릴인은 31일 동안의 사용량 추세를 보여 줍니다. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/monitoring-usage-and-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다.

![노드당 포함 가로 막대형 차트 스크린 샷](./media/monitoring-usage-and-estimated-costs/002.png)

다른 비슷한 사용량 및 비용 요약은 다음과 같습니다. 이 예제에서는 새로운 2018년 4월 구독으로, 사용량 기준 가격 책정 모델을 보여 줍니다. 노드 기반 청구에서는 부족했던 부분을 확인합니다. Log Analytics 및 Application Insights의 데이터 수집 및 보존이 새 공통 미터에서 보고됩니다.

![사용량 및 예상 비용 포털 스크린 샷 - 2018년 4월 가격 책정](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>새 가격 책정 모델

2018년 4월, [새 모니터링 가격 책정 모델이 릴리스되었습니다](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  이 모델은 클라우드 기반이며 사용을 기준으로 한 가격 책정이라는 특징이 있습니다. 노드 기반 약정 없이 사용한 양만큼만 결제합니다. [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)에 대해 새 가격 책정 모델의 세부 정보를 제공합니다. 

2018년 4월 2일 이후 Log Analytics 또는 Application Insights에 가입한 고객의 경우 새 가격 책정 모델만 선택할 수 있습니다. 이 서비스를 이미 사용하던 고객의 경우 새 가격 책정 모델로의 전환이 선택 사항입니다.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>새 가격 책정 모델의 영향 평가
새 가격 책정 모델이 미치는 영향은 모니터링 사용량 패턴에 따라 고객마다 달라질 것입니다. 2018년 4월 2일 이전에 Log Analytics 또는 Application Insights를 사용한 고객의 경우 Azure Monitor에서 **사용량 및 예상 비용** 페이지에 새 가격 책정 모델로 전환할 경우 비용 변동 예상치가 나타납니다. 구독을 새 모델로 전환하는 방법을 제공합니다. 대부분의 고객에게 새 가격 책정 모델이 유리합니다. 데이터 사용 패턴이 매우 높은 수준이거나 요금이 비싼 지역의 고객이라면 여기에 해당하지 않을 수 있습니다.

**사용량 및 예상 비용** 페이지에서 선택한 구독의 예상 비용을 보려면 페이지 맨 위에 있는 파란 배너를 선택합니다. 새 가격 책정 모델은 한 번에 한 구독만 선택할 수 있으므로 한 번에 한 구독에 대해서만 확인해 보는 것이 좋습니다.

![새 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/004.png)

새 페이지에서는 이전 페이지의 유사한 버전을 녹색 배너와 함께 보여 줍니다.

![현재 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/005.png)

이 페이지는 새 가격 책정 모델에 해당하는 다양한 미터 집합도 보여 줍니다. 다음은 예제 목록입니다.

- Insight and Analytics\Overage per Node
- Insight and Analytics\Included per Node
- Application Insights\Basic Overage Data
- Application Insights\Included Data

새 가격 책정 모델에는 노드 기반 포함 데이터 할당이 없습니다. 따라서 이러한 데이터 수집 미터가 **Shared Services\Data Ingestion**이라는 새로운 공통 데이터 수집 미터로 결합됩니다. 

비용이 많이 드는 지역에서 Log Analytics 또는 Application Insights에 수집된 데이터는 또 다른 변화가 있습니다. 비용이 많이 드는 지역에 대한 데이터는 새 지역별 미터로 표시됩니다. 예로 **데이터 수집(미국 중서부)** 이 있습니다.

> [!NOTE]
> 구독당 예상 비용은 OMS(Operations Management Suite) 구독의 계정 수준 노드별 자격의 영향을 받지 않습니다. 이 경우 새 가격 책정 모델에 대한 더 상세한 논의는 고객 담당자에게 문의하세요.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>새 가격 책정 모델 및 Operations Management Suite 구독 자격

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 및 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans)에 대한 노드별 데이터 주입 자격이 있습니다. 지정된 구독에서 Log Analytics 작업 영역 또는 Application Insights 리소스에 대해 이러한 자격을 얻으려면: 

- 구독의 가격 책정 모델이 2018년 4월 이전 모델의 가격 책정 모델을 유지해야 합니다.
- Log Analytics 작업 영역에서 "노드당(OMS)" 가격 책정 계층을 사용해야 합니다.
- Application Insights 리소스에서 "Enterprise" 가격 책정 계획을 사용해야 합니다.

조직에서 구매한 제품군의 노드 수에 따라, 일부 구독을 새 가격 책정 모델로 전환하는 것이 이로울 수 있지만 신중히 결정해야 합니다. 일반적으로 위에서 설명한 대로 단순히 2018년 4월 이전 모델에서 유지하는 것이 좋습니다.

> [!WARNING]
> 조직이 Microsoft Operations Management Suite E1 및 E2를 구매한 경우 일반적으로 2018년 4월 이전의 가격 책정 모델의 구독을 유지하는 것이 가장 좋습니다. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환할 경우의 변경 내용

새 가격 책정 모델은 단일 계층(또는 계획)에 대한 Log Analytics 및 Application Insights 가격 책정 옵션을 간소화합니다. 새 가격 책정 모델로 구독을 이동하면:

- 각 Log Analytics의 가격 책정 계층을 새 GB당 계층으로 변경(Azure Resource Manager에서 "pergb2018"이라고 함)
- Enterprise 계획에서 Application Insights 리소스도 Basic 계획으로 변경됩니다.

비용 예측에서 이러한 변경의 효과를 보여 줍니다.

> [!WARNING]
> 구독에서 Azure Resource Manager 또는 PowerShell을 사용하여 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) 또는 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell)를 배포하는 경우 새 가격 책정 모델로 이동했습니다. Log Analytics 또는 Application Insights의 "Basic"에 대해 "pergb2018" 이외의 가격 책정 계층/계획을 지정하는 경우 잘못된 가격 책정 계층/계획을 지정한 이유로 배포에 실패하지 않고 성공하지만 **유효한 가격 책정 계층/계획만을 사용합니다**(유효하지 않은 가격 책정 계층 메시지가 생성되는 Log Analytics Free 계층에는 적용되지 않음).
>

## <a name="moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환

구독의 새 가격 책정 모델을 사용하기로 했다면 **사용량 및 예상 비용** 페이지의 **가격 책정 모델 선택** 옵션을 선택합니다.

![새 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/006.png)

**가격 책정 모델 선택** 페이지가 열립니다. 이전 페이지에서 본 각 구독 목록을 보여 줍니다.

![가격 책정 모델 선택 스크린 샷](./media/monitoring-usage-and-estimated-costs/007.png)

구독을 새 가격 책정 모델로 전환하려면 확인란을 선택하고 **저장**을 선택하기만 하면 됩니다. 같은 방법으로 기존 가격 책정 모델로 되돌릴 수 있습니다. 가격 책정 모델을 변경하려면 구독 소유자나 참가자 권한이 필요합니다.

## <a name="automate-moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환 자동화

아래 스크립트는 Azure PowerShell 모듈이 필요합니다. 최신 버전이 설치되었는지 확인하려면 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.1.0)를 참조하세요.

최신 버전의 Azure PowerShell이 설치되어 있으면 먼저 ``Connect-AzureRmAccount`` 명령을 실행합니다.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

isGrandFatherableSubscription의 결과가 True이면 이 구독의 가격 책정 모델을 전환할 수 있다는 의미입니다. optedInDate의 값이 없으면 이 구독이 이전 가격 책정 모델로 설정되어 있다는 의미입니다.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

이 구독을 새로운 가격 책정 모델로 마이그레이션하려면 다음 명령을 실행합니다.

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

변경 작업이 성공했는지 확인하려면 다음 명령을 다시 실행합니다.

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

마이그레이션이 성공하면 다음과 비슷한 결과가 표시됩니다.

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

이제 optInDate에는 이 구독에 새 가격 책정 모델이 적용된 시간의 타임스탬프가 포함되어 있습니다.

이전 가격 책정 모델로 되돌리려면 다음 명령을 실행합니다.

```PowerShell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

``-Action listmigrationdate``가 포함된 이전 스크립트를 다시 실행하면 구독이 기존 가격 책정 모델로 전환되었음을 알리는 빈 optedInDate 값이 표시됩니다.

구독이 여러 개 있고 동일한 테넌트에 호스팅되는 구독을 마이그레이션하려는 경우 다음 스크립트를 사용하여 사용자 고유의 변형을 만들 수 있습니다.

```PowerShell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzureRMSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

테넌트의 모든 구독이 새 가격 책정 모델에 적합한지 확인하려면 다음 명령을 실행합니다.

```PowerShell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

세 개의 배열을 생성하는 스크립트를 만들어서 스크립트를 추가로 구체화할 수 있습니다. 한 배열은 ```isGrandFatherableSubscription```이 True로 설정되어 있고 현재 optedInDate의 값이 없는 모든 구독으로 구성됩니다. 두 번째 구독 배열은 현재 새 가격 책정 모델에 있습니다. 세 번째 배열은 테넌트의 구독 중에서 새 가격 책정 모델에 적합하지 않은 구독 id로만 채워집니다.

```PowerShell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> 구독 수에 따라 위의 스크립트를 실행하는 데 약간의 시간이 걸릴 수 있습니다. .add() 메서드를 사용하기 때문에 항목이 각 배열에 추가될 때 PowerShell 창에 증분 값이 반영됩니다.

구독을 세 개의 배열로 나누었으니, 결과를 신중하게 검토해야 합니다. 배열 콘텐츠의 백업 복사본을 만들어 놓으면 나중에 필요할 때 변경 내용을 간편하게 되돌릴 수 있습니다. 현재 이전 가격 책정 모델이 적용되는 모든 적격 구독을 새 가격 책정 모델로 변환하기로 결정한 경우 다음과 같은 방법으로 이 작업을 수행할 수 있습니다.

```PowerShell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```