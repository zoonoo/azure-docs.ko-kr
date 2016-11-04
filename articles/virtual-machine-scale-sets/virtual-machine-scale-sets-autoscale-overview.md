---
title: 자동 크기 조정 및 가상 컴퓨터 규모 집합 | Microsoft Docs
description: 진단 및 자동 크기 조정 리소스를 사용하여 규모 집합의 가상 컴퓨터를 자동적으로 크기 조정하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu

---
# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>자동 크기 조정 및 가상 컴퓨터 규모 집합
크기 집합에서 수행되는 가상 컴퓨터 자동 크기 조정은 성능 요구 사항에 일치하기 위해 필요에 따라 집합에서 컴퓨터를 만들거나 삭제하는 것입니다. 작업량이 증가하면 작업을 효과적으로 수행할 수 있도록 응용 프로그램에 추가 리소스가 필요할 수 있습니다.

자동 크기 조정은 관리 오버헤드를 줄이기 위해 자동화된 프로세스입니다. 오버헤드를 줄이면 지속적으로 시스템 성능을 모니터링하거나 리소스 관리 방법을 결정할 필요가 없습니다. 크기 조정은 탄력적인 프로세스입니다. 부하가 증가함에 따라 더 많은 리소스를 추가할 수 있으나 수요가 감소할 경우 비용을 최소화하고 성능 수준을 유지하기 위해 리소스를 제거할 수 있습니다.

Azure Resource Manager 템플릿, Azure PowerShell 또는 Azure CLI를 사용하여 규모 집합에 자동 크기 조정을 설정합니다.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>리소스 관리자 템플릿을 사용하여 크기 조정 설정
각 응용 프로그램의 리소스를 개별적으로 배포하고 관리하는 대신, 모든 리소스를 하나의 조정된 작업으로 배포하는 템플릿을 사용합니다. 템플릿에서 응용 프로그램 리소스를 정의하고 다양한 환경에 대한 배포 매개 변수를 지정합니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다. 자세한 내용은 [Azure Resource Manager 템플릿 작성](../resource-group-authoring-templates.md)을 살펴보세요.

템플릿에서 용량 요소를 지정합니다.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

용량은 집합에 있는 가상 컴퓨터의 수를 식별합니다. 다른 값으로 템플릿을 배포하여 용량을 수동으로 변경할 수 있습니다. 단순히 용량을 변경하기 위해 템플릿을 배포하고 있는 경우 업데이트된 용량의 SKU 요소만을 포함할 수 있습니다.

autoscaleSettings 리소스와 진단 확장을 조합하여 크기 집합의 용량을 자동으로 변경합니다.

### <a name="configure-the-azure-diagnostics-extension"></a>Azure 진단 확장 구성
규모 집합의 각 가상 컴퓨터에서 메트릭을 수집할 수 있는 경우에만 자동 크기 조정을 수행할 수 있습니다. Azure 진단 확장은 자동 크기 조정 리소스의 메트릭 수집 요구 사항을 충족하는 모니터링 및 진단 기능을 제공합니다. 리소스 관리자 템플릿의 일부로 확장을 설치할 수 있습니다.

이 예제에서는 진단 확장을 구성하는 템플릿에 사용되는 변수를 보여 줍니다.

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

템플릿이 배포될 때 매개 변수가 제공됩니다. 이 예제에서 데이터가 저장되는 저장소 계정 이름 및 데이터가 수집되는 크기 집합 이름이 제공됩니다. 또한 이 Windows Server 예제에서는 Thread Count 성능 카운터가 수집됩니다. Windows 또는 Linux에 있는 모든 사용 가능한 성능 카운터가 진단 정보 수집에 사용될 수 있고 확장 구성에 포함될 수도 있습니다.

이 예제에서는 템플릿의 확장 정의를 보여 줍니다.

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

진단 확장을 실행할 때 데이터는 지정한 저장소 계정에 위치한 테이블에 수집됩니다. WADPerformanceCounters 테이블에서 수집된 데이터를 찾을 수 있습니다.

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>autoScaleSettings 리소스 구성
autoScaleSettings 리소스는 크기 집합에 있는 가상 컴퓨터의 수를 늘릴지 또는 줄일지를 결정하기 위해 진단 확장의 정보를 사용합니다.

이 예제에서는 템플릿의 리소스 구성을 보여 줍니다.

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

위의 예제에서는 자동 크기 조정 작업을 정의하기 위해 두 가지 규칙이 생성됩니다. 첫 번째 규칙은 규모 확장 작업을 정의하고 두 번째 규칙은 규모 축소 작업을 정의합니다. 다음 값이 규칙에 제공됩니다.

* **metricName** - 이 값은 진단 확장에 대한 wadperfcounter 변수에 정의한 성능 카운터와 동일합니다. 위의 예제에서는 스레드 수 카운터를 사용합니다.  
* **metricResourceUri** - 이 값은 가상 컴퓨터 크기 집합의 리소스 식별자입니다. 이 식별자는 리소스 그룹 이름, 리소스 공급자 이름 및 크기 조정을 위한 규모 집합 이름을 포함합니다.
* **timeGrain** – 이 값은 수집되는 메트릭의 세분성입니다. 위의 예제에서는 1분 간격으로 데이터를 수집합니다. 이 값은 timeWindow와 함께 사용됩니다.
* **statistic** – 이 값은 자동 크기 조정 작업을 수용하기 위해 메트릭을 결합하는 방법을 결정합니다. 가능한 값은 평균, 최소, 최대입니다.
* **timeWindow** – 이 값은 인스턴스 데이터가 수집되는 시간 범위입니다. 5분에서 12시간 사이여야 합니다.
* **timeAggregation** – 이 값은 시간이 지남에 따라 수집된 데이터가 결합되어야 하는 방법을 결정합니다. 기본값은 평균입니다. 가능한 값은 평균, 최소, 최대, 마지막, 합계, 개수입니다.
* **operator** – 이 값은 메트릭 데이터와 임계값을 비교하는 데 사용되는 연산자입니다. 가능한 값은 Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual입니다.
* **threshold** - 이 값은 크기 조정 작업을 트리거하는 값입니다. 규모 확장 임계값과 축소 작업 임계값 사이에 충분한 차이가 있는지 확인해야 합니다. 이러한 값을 동일하게 설정하면 시스템은 차이가 일정하다고 예상하여 크기 조정 동작을 구현하지 못하게 합니다. 예를 들어 앞의 예제에서 두 값을 모두 스레드 600으로 설정할 수 없습니다.
* **direction** – 이 값은 임계값이 달성되었을 때 수행되는 동작을 결정합니다. 가능한 값은 증가 또는 감소입니다.
* **type** – 이 값은 발생되어야 하는 동작의 유형이며 ChangeCount로 설정되어 있어야 합니다.
* **value** – 이 값은 크기 집합에서 추가되거나 제거된 가상 컴퓨터의 수입니다. 이 값은 1 이상이어야 합니다.
* **cooldown** – 이 값은 다음 작업이 발생하기 전에 마지막 크기 조정 작업 이후에 대기 시간입니다. 이 값은 1분에서 1주 사이여야 합니다.

사용하는 성능 카운터에 따라 템플릿 구성에서 일부 요소가 다르게 사용됩니다. 다음 예제에서 성능 카운터는 Thread Count이며 규모 확장 작업에 대한 임계값은 650, 규모 축소 작업에 대한 임계값은 550입니다. %Processor Time과 같은 카운터를 사용할 경우 크기 조정 작업을 판단하는 CPU 사용 백분율을 임계값으로 설정합니다.

크기 조정 작업을 트리거하는 부하가 가상 컴퓨터에서 발생하면 템플릿의 값을 기준으로 집합의 컴퓨터 수가 증가합니다. 예를 들어 용량이 3, 크기 조정 작업 값이 1로 설정된 규모 집합에서

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

평균 스레드 수가 임계값인 650 이상으로 증가시키는 부하가 생기면

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

집합의 용량을 1씩 증가시키는 규모 확장 작업을 트리거합니다.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

규모 집합에 가상 컴퓨터가 추가됩니다.

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

5분의 휴지 기간 후 컴퓨터의 평균 스레드 수가 600개 이상으로 유지되는 경우 집합에 다른 컴퓨터가 추가됩니다. 평균 스레드 수가 550개 미만으로 유지되는 경우 규모 집합의 용량이 1씩 감소하며 집합에서 컴퓨터 하나가 제거됩니다.

## <a name="set-up-scaling-using-azure-powershell"></a>Azure PowerShell을 사용하여 크기 조정 설정
PowerShell을 사용하여 자동 크기 조정을 설정하는 예제를 보려면 [Azure Insights PowerShell 빠른 시작 샘플](../monitoring-and-diagnostics/insights-powershell-samples.md)을 참조하세요.

## <a name="set-up-scaling-using-azure-cli"></a>Azure CLI를 사용하여 크기 조정 설정
Azure CLI를 사용하여 자동 크기 조정을 설정하는 예제를 보려면 [Azure Insights 플랫폼 간 CLI 빠른 시작 샘플](../monitoring-and-diagnostics/insights-cli-samples.md)을 참조하세요.

## <a name="investigate-scaling-actions"></a>크기 조정 작업 조사
* [Azure 포털]() - 포털을 사용하여 현재 제한된 양의 정보를 얻을 수 있습니다.
* [Azure 리소스 탐색기]() - 크기 집합의 현재 상태를 탐색하는 데 가장 적합한 도구입니다. 이 경로를 따라 사용자가 만든 규모 집합의 인스턴스 보기가 표시됩니다. 구독 > {구독} > resourceGroups > {리소스 그룹} > 공급자 > Microsoft.Compute > virtualMachineScaleSets > {규모 집합} > virtualMachines
* Azure PowerShell - 이 명령을 사용하여 몇 가지 정보를 가져옵니다.
  
        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
* 다른 컴퓨터와 마찬가지로 jumpbox 가상 컴퓨터에 연결한 다음 개별 프로세스를 모니터링하도록 규모 집합의 가상 컴퓨터에 원격으로 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 구성된 자동 크기 조정을 사용하여 크기 집합을 만드는 방법에 대한 예제를 보려면 [가상 컴퓨터 크기 집합에서 자동으로 컴퓨터 크기 조정](virtual-machine-scale-sets-windows-autoscale.md) 을 살펴보세요.
* [Azure Insights PowerShell 빠른 시작 샘플](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [크기 자동 조정 작업을 사용하여 Azure Insight에서 전자 메일 및 Webhook 경고 알림 보내기](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)의 알림 기능에 대해 자세히 알아봅니다.
* [Azure Insights에서 감사 로그를 사용하여 메일 및 Webhook 경고 알림 보내기](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
* [고급 자동 크기 조정 시나리오](virtual-machine-scale-sets-advanced-autoscale.md)에 대해 자세히 알아봅니다.

<!--HONumber=Oct16_HO2-->


