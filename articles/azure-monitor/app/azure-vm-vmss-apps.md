---
title: Azure VM에서 성능 모니터링 - Azure 응용 프로그램 인사이트
description: Azure VM 및 Azure 가상 시스템 규모 집합에 대한 응용 프로그램 성능 모니터링입니다. 차트 로드 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661331"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure 가상 시스템 및 Azure 가상 시스템 규모 집합에 Azure 모니터 응용 프로그램 인사이트 에이전트 배포

[Azure 가상 시스템](https://azure.microsoft.com/services/virtual-machines/) 및 Azure 가상 [시스템 크기 집합에서](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) 실행되는 .NET 기반 웹 응용 프로그램에서 모니터링을 사용하도록 설정하는 것이 그 어느 때보다 쉬워졌습니다. 코드를 수정하지 않고도 Application Insights를 사용하면 얻을 수 있는 모든 이점을 얻을 수 있습니다.

이 문서에서는 응용 프로그램 인사이트 에이전트를 사용하여 응용 프로그램 인사이트 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포를 위한 프로세스자동화에 대한 예비 지침을 제공합니다.

> [!IMPORTANT]
> .NET에 대한 Azure 응용 프로그램 인사이트 에이전트는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장하지 않습니다. 일부 기능은 지원되지 않을 수 있으며 일부 기능은 제한된 기능을 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure 가상 컴퓨터 및 Azure 가상 시스템 집합 호스트된 응용 프로그램에 대 한 응용 프로그램 모니터링을 사용 하는 두 가지 방법이 있습니다.

* 애플리케이션 인사이트 에이전트를 통한 **코드리스**
    * 이 방법을 사용하면 가장 쉽게 사용할 수 있으며 고급 구성이 필요하지 않습니다. 이를 "런타임" 모니터링이라고도 합니다.

    * Azure 가상 컴퓨터 및 Azure 가상 시스템 크기 집합의 경우 최소한 이 수준의 모니터링을 사용하도록 설정하는 것이 좋습니다. 그런 다음 특정 시나리오에 따라 수동 계측이 필요한지 여부를 평가할 수 있습니다.

    * 응용 프로그램 인사이트 에이전트는 .NET SDK와 동일한 종속성 신호를 즉시 수집합니다. 자세한 내용은 [종속성 자동 수집을](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) 참조하십시오.
        > [!NOTE]
        > 현재 .Net IIS 호스팅 응용 프로그램만 지원됩니다. SDK를 사용하여 Azure 가상 시스템 및 가상 시스템 규모 집합에서 호스팅되는 코어, Java 및 Node.js 응용 프로그램을 ASP.NET 계측합니다.

* SDK를 통한 **코드 기반**

    * 이 방법은 훨씬 더 사용자 정의할 수 있지만 [응용 프로그램 인사이트 SDK NuGet 패키지에 대한 종속성을 추가해야 합니다.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 이 방법은 최신 버전의 패키지에 대한 업데이트를 직접 관리해야 한다는 의미이기도 합니다.

    * 에이전트 기반 모니터링을 사용하여 기본적으로 캡처되지 않은 이벤트/종속성을 추적하기 위해 사용자 지정 API 호출을 수행해야 하는 경우 이 메서드를 사용해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭 문서에 대한 API를](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 확인하십시오.

> [!NOTE]
> 에이전트 기반 모니터링 및 수동 SDK 기반 계측이 모두 감지되면 수동 계측 설정만 적용됩니다. 이는 중복 된 데이터가 전송되는 것을 방지하기위한 것입니다. 이에 대한 자세한 내용은 [아래의 문제 해결 섹션을](#troubleshooting) 확인하십시오.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell을 사용하여 Azure 가상 컴퓨터에서 .NET 응용 프로그램에 대한 응용 프로그램 인사이트 에이전트 관리

> [!NOTE]
> 응용 프로그램 인사이트 에이전트를 설치하기 전에 연결 문자열이 필요합니다. [새 응용 프로그램 인사이트 리소스를 만들거나](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) 기존 응용 프로그램 인사이트 리소스에서 연결 문자열을 복사합니다.

> [!NOTE]
> 파워쉘이 신가요? [시작하기 가이드를](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)확인하십시오.

Azure 가상 시스템의 확장으로 응용 프로그램 인사이트 에이전트 설치 또는 업데이트
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Powershell 루프를 사용하여 대규모의 여러 가상 머신에서 확장으로 응용 프로그램 인사이트 에이전트를 설치하거나 업데이트할 수 있습니다.

Azure 가상 컴퓨터에서 응용 프로그램 인사이트 에이전트 확장 제거
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure 가상 시스템에 대한 쿼리 응용 프로그램 인사이트 에이전트 확장 상태
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Azure 가상 시스템에 대해 설치된 확장 목록 가져옵니다.
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
또한 포털의 Azure 가상 [시스템 블레이드에서](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) 설치된 확장을 볼 수도 있습니다.

> [!NOTE]
> 응용 프로그램 인사이트 에이전트 확장을 배포하는 데 사용한 연결 문자열과 연결된 응용 프로그램 인사이트 리소스 내의 라이브 메트릭 스트림을 클릭하여 설치를 확인합니다. 여러 가상 컴퓨터에서 데이터를 보내는 경우 서버 이름 아래의 대상 Azure 가상 컴퓨터를 선택합니다. 데이터가 흐르기 시작하는 데 최대 1분이 걸릴 수 있습니다.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>powershell을 사용하여 Azure 가상 시스템 규모 집합에서 .NET 응용 프로그램에 대한 응용 프로그램 인사이트 에이전트 관리

Azure 가상 시스템 확장 집합에 대한 확장으로 응용 프로그램 인사이트 에이전트 설치 또는 업데이트
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 가상 시스템 규모 집합에서 응용 프로그램 모니터링 확장 제거
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 가상 시스템 규모 집합에 대한 쿼리 응용 프로그램 모니터링 확장 상태
```powershell
# Not supported by extensions framework
```

Azure 가상 시스템 규모 집합에 대해 설치된 확장 목록 가져옵니다.
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>문제 해결

Azure 가상 시스템 및 가상 시스템 규모 집합에서 실행되는 .NET 응용 프로그램에 대한 응용 프로그램 인사이트 모니터링 에이전트 확장에 대한 문제 해결 팁을 찾아보십시오.

> [!NOTE]
> .NET Core, Java 및 Node.js 응용 프로그램은 수동 SDK 기반 계측을 통해 Azure 가상 시스템 및 Azure 가상 시스템 규모 집합에서만 지원되므로 아래 단계는 이러한 시나리오에 적용되지 않습니다.

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>다음 단계
* [Azure 가상 시스템 확장 집합에 응용 프로그램을 배포하는](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)방법에 대해 알아봅니다.
* 끝점이 다운된 경우 [가용성 웹 테스트를](monitor-web-app-availability.md) 경고하도록 설정합니다.
