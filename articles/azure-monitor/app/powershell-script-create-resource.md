---
title: Application Insights 리소스를 만들기 위한 PowerShell 스크립트 | Microsoft Docs
description: Application Insights 리소스의 생성을 자동화합니다.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 8a7b19dd6e5bc08c0c7e278b514ecaa9dc13a00e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254490"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Application Insights 리소스를 만들기 위한 PowerShell 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/)으로 새 애플리케이션 또는 애플리케이션의 새 버전을 모니터링 하려는 경우, Microsoft Azure에서 새 리소스를 설정합니다. 이 리소스는 앱이 분석하고 표시한 원격 분석 데이터에 있습니다. 

PowerShell을 사용하여 새 리소스의 생성을 자동화할 수 있습니다.

예를 들어 모바일 디바이스 앱을 개발하는 경우, 언제든지 고객이 사용 중인 앱에는 게시된 여러 버전이 있을 가능성이 있습니다. 혼합된 서로 다른 버전의 원격 분석 결과를 가져오지 않으려고 합니다. 따라서 각 빌드에 대한 새 리소스를 만드는 빌드 프로세스를 가져옵니다.

> [!NOTE]
> 모든 리소스 집합을 동시에 만들려면 [Azure 템플릿을 사용하여 리소스를 만드는](powershell.md) 것이 좋습니다.
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Application Insights 리소스를 만들기 위한 스크립트
관련 cmdlet 사양을 참조하세요.

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell 스크립트*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>iKey로 수행할 작업
각 리소스는 해당 계측 키(iKey)로 식별됩니다. iKey는 리소스 생성 스크립트의 출력입니다. 빌드 스크립트는 앱에 포함된 Application Insights SDK에 iKey를 제공해야 합니다.

SDK에 사용할 수 있는 iKey에는 두 가지가 있습니다:

* [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에서: 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* 또는 [초기화 코드](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>참고 항목
* [서식 파일에서 Application Insights 및 웹 테스트 리소스 만들기](powershell.md)
* [PowerShell 사용한 Azure 진단의 모니터링 설정](powershell-azure-diagnostics.md) 
* [PowerShell을 사용하여 경고 설정](powershell-alerts.md)

