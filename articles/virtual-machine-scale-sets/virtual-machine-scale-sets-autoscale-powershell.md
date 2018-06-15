---
title: Azure PowerShell을 사용하여 가상 머신 확장 집합 자동 크기 조정 | Microsoft Docs
description: Azure PowerShell을 사용하여 가상 머신 확장 집합에 대한 자동 크기 조정 규칙을 만드는 방법
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201334"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell을 사용하여 가상 머신 확장 집합의 크기를 자동으로 조정
확장 집합을 만들 때 실행할 VM 인스턴스 수를 정의합니다. 응용 프로그램 수요가 변경될 때는 VM 인스턴스 수를 자동으로 늘리거나 줄일 수 있습니다. 자동 크기 조정 기능을 사용하면 고객 수요에 따라 조정하거나 앱 수명 주기 동안 응용 프로그램 성능 변화에 대응할 수 있습니다.

이 문서에서는 Azure PowerShell을 사용하여 확장 집합의 VM 인스턴스 성능을 모니터링하는 자동 크기 조정 규칙을 만드는 방법을 보여 줍니다. 이러한 자동 크기 조정 규칙은 성능 메트릭에 따라 VM 인스턴스 수를 늘리거나 줄일 수 있습니다. 이러한 단계는 [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) 또는 [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)에서도 완료할 수 있습니다.


## <a name="prerequisites"></a>필수 조건
자동 크기 조정 규칙을 만들려면 기존 가상 머신 확장 집합이 필요합니다. 확장 집합은 [Azure Portal](virtual-machine-scale-sets-create-portal.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) 또는 [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md)을 사용하여 만들 수 있습니다.

자동 크기 조정 규칙을 보다 쉽게 만들려면 확장 집합에 대해 몇 개의 변수를 정의합니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹과 *East US* 지역에 있는 *myScaleSet*라는 확장 집합에 대한 변수를 정의합니다. 구독 ID는 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription)을 사용하여 가져옵니다. 사용자 계정에 여러 구독이 연결되어 있으면 첫 번째 구독만 반환됩니다. 이름과 구독 ID를 다음과 같이 조정합니다.

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>자동 규모 확장 규칙 만들기
응용 프로그램 수요가 증가하면 확장 집합의 VM 인스턴스 부하가 증가합니다. 증가된 로드가 단순한 요구가 아닌 일관된 요구인 경우 확장 집합의 VM 인스턴스 수를 늘리도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이러한 VM 인스턴스를 만들고 응용 프로그램을 배포하면 확장 집합이 부하 분산 장치를 통해 트래픽을 분산하기 시작합니다. 사용자는 모니터링할 메트릭(예: CPU 또는 디스크), 지정된 임계값을 응용 프로그램 로드가 충족해야 하는 기간, 확장 집합에 추가할 VM 인스턴스 수를 제어할 수 있습니다.

평균 CPU 로드가 5분간 70%를 초과할 경우 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule)을 사용하여 확장 집합의 VM 인스턴스 수를 늘리는 규칙을 만들어 보겠습니다. 규칙이 트리거되면 VM 인스턴스 수가 20% 증가합니다. VM 인스턴스 수가 적은 확장 집합에서는 `-ScaleActionScaleType`은 그대로 두고 `-ScaleActionValue`만 지정하여 인스턴스를 *1*개 또는 *2*개 더 늘릴 수 있습니다. VM 인스턴스 수가 많은 확장 집합에서는 VM 인스턴스의 10% 또는 20% 증가가 더 적합할 수 있습니다.

이 규칙에 사용되는 매개 변수는 다음과 같습니다.

| 매개 변수               | 설명                                                                                                         | 값          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | 확장 집합 작업을 모니터링하고 적용하기 위한 성능 메트릭입니다.                                                   | 백분율 CPU |
| *-TimeGrain*            | 분석을 위해 메트릭이 수집되는 빈도입니다.                                                                   | 1분       |
| *-MetricStatistic*      | 분석을 위해 수집된 메트릭을 집계하는 방법을 정의합니다.                                                | 평균        |
| *-TimeWindow*           | 메트릭과 임계값을 비교하기 전에 모니터링하는 기간입니다.                                   | 10분      |
| *-Operator*             | 메트릭 데이터를 임계값과 비교하는 데 사용되는 연산자입니다.                                                     | 보다 큼   |
| *-Threshold*            | 자동 크기 조정 규칙이 작업을 트리거하도록 하는 값입니다.                                                      | 70%            |
| *-ScaleActionDirection* | 규칙이 적용될 때 확장 집합이 확장 또는 축소되어야 하는지를 정의합니다.                                             | 증가       |
| *–ScaleActionScaleType* | VM 인스턴스 수를 백분율 양만큼 변경해야 함을 나타냅니다.                                 | 백분율 변경 |
| *-ScaleActionValue*     | 규칙이 트리거되면 VM 인스턴스의 백분율을 변경해야 합니다.                                            | 20             |
| *-ScaleActionCooldown*  | 자동 크기 조정 작업이 적용될 시간을 주기 위해 규칙을 다시 적용하기 전에 대기할 시간입니다. | 5분      |

다음 예제에서는 이 확장 규칙이 적용되는 *myRuleScaleOut*이라는 개체를 생성합니다. *-MetricResourceId*는 구독 ID, 리소스 그룹 이름 및 확장 집합 이름에 대해 이전에 정의된 변수를 사용합니다.

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>자동 규모 감축 규칙 만들기
저녁이나 주말에는 응용 프로그램 수요가 줄어들 수 있습니다. 이 감소된 로드가 일정 기간 동안 일관성 있게 유지될 경우 확장 집합의 VM 인스턴스 수를 줄이도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이 규모 감축 작업은 현재 수요를 충족하는 데 필요한 수의 인스턴스만 실행하므로 확장 집합의 실행 비용을 줄입니다.

평균 CPU 로드가 10분간 30% 미만일 경우 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule)을 사용하여 확장 집합의 VM 인스턴스 수를 줄이는 규칙을 만듭니다. 규칙이 트리거되면 VM 인스턴스 수가 20% 줄어듭니다. 다음 예제에서는 이 확장 규칙이 적용되는 *myRuleScaleDown*이라는 개체를 만듭니다. *-MetricResourceId*는 구독 ID, 리소스 그룹 이름 및 확장 집합 이름에 대해 이전에 정의된 변수를 사용합니다.

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>자동 크기 조정 프로필 정의
자동 크기 조정 규칙을 확장 집합과 연결하려면 프로필을 만듭니다. 자동 크기 조정 프로필은 기본, 최소, 최대 확장 집합 용량을 정의하고 자동 크기 조정 규칙을 연결합니다. [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile)을 사용하여 자동 크기 조정 프로필을 만듭니다. 다음 예제에서는 기본 및 최소 용량으로 VM 인스턴스 *2*개, 최대 용량으로 *10*개를 설정합니다. 그런 다음, 이전 단계에서 만든 규모 확장 및 규모 감축 규칙이 연결됩니다.

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>확장 집합에 자동 크기 조정 규칙 적용
마지막 단계는 확장 집합에 자동 크기 조정 프로필을 적용하는 것입니다. 그러면 확장 집합이 응용 프로그램 수요에 따라 자동으로 확장되거나 감축될 수 있습니다. [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting)을 사용하여 자동 크기 조정 프로필을 다음과 같이 적용합니다.

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>확장 집합의 인스턴스 수 모니터링
VM 인스턴스의 수와 상태를 확인하려면 [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM)을 사용하여 확장 집합의 인스턴스 목록을 확인합니다. 상태는 확장 집합이 자동으로 확장되면서 VM 인스턴스가 프로비전되고 있는지, 아니면 확장 집합이 자동으로 감축되면서 프로비전 해제되고 있는지를 나타냅니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에 있는 *myScaleSet*이라는 확장 집합에 대한 VM 인스턴스 상태를 확인합니다.

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>일정에 따라 자동 크기 조정
이전 예제에서는 CPU 사용량 같은 기본 호스트 메트릭을 사용하여 확장 집합을 자동으로 감축하거나 확장했습니다. 일정에 따라 자동 크기 조정 규칙을 만들 수도 있습니다. 이러한 일정 기반 규칙을 사용하면 응용 프로그램 수요(예: 코어 작업 시간)의 예상 증가 전에 VM 인스턴스 수를 자동으로 확장한 다음, 수요 감소가 예상되는 시간(예: 주말)에 인스턴스 수를 자동으로 감축할 수 있습니다.

호스트 메트릭 대신 일정에 따라 자동 크기 조정 규칙을 만들려면 Azure Portal을 사용합니다. 현재 일정 기반 규칙은 Azure PowerShell을 사용하여 만들 수 없습니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 자동 크기 조정 규칙을 사용하여 수평으로 크기를 조정하고 확장 집합의 VM 인스턴스 *수*를 늘리거나 줄이는 방법을 배웠습니다. 수직으로 크기를 조정하여 VM 인스턴스 *크기*를 늘리거나 줄일 수도 있습니다. 자세한 내용은 [가상 머신 확장 집합을 사용하여 수직으로 자동 크기 조정](virtual-machine-scale-sets-vertical-scale-reprovision.md)을 참조하세요.

VM 인스턴스 관리 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 가상 머신 확장 집합 관리](virtual-machine-scale-sets-windows-manage.md)를 참조하세요.

자동 크기 조정 규칙이 트리거될 때 경고를 생성하는 방법에 대한 자세한 내용은 [Azure Monitor에서 자동 크기 조정 작업을 사용하여 메일 및 webhook 경고 알림 보내기](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)를 참조하세요. [Azure Monitor에서 감사 로그를 사용하여 메일 및 webhook 경고 알림을 보낼](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) 수도 있습니다.
