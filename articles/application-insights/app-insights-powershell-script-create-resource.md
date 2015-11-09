<properties 
	pageTitle="Application Insights 리소스를 만들기 위한 PowerShell 스크립트" 
	description="Application Insights 리소스의 생성을 자동화합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2015" 
	ms.author="awills"/>

#  Application Insights 리소스를 만들기 위한 PowerShell 스크립트

*Application Insights는 미리 보기 상태입니다.*

[Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/)으로 새 응용 프로그램 또는 응용 프로그램의 새 버전을 모니터링 하려는 경우, Microsoft Azure에서 새 리소스를 설정합니다. 이 리소스는 앱이 분석하고 표시한 원격 분석 데이터에 있습니다.

PowerShell을 사용하여 새 리소스의 생성을 자동화할 수 있습니다.

예를 들어 모바일 장치 앱을 개발하는 경우, 언제든지 고객이 사용 중인 앱에는 게시된 여러 버전이 있을 가능성이 있습니다. 혼합된 서로 다른 버전의 원격 분석 결과를 가져오지 않으려고 합니다. 따라서 각 빌드에 대한 새 리소스를 만드는 빌드 프로세스를 가져옵니다.

## Application Insights 리소스를 만들기 위한 스크립트

*출력*

* App Insights 이름 = mytestapp
* IKey 00000000-0000-0000-0000-000000000000 =

*PowerShell 스크립트*

```PowerShell

cls


##################################################################
# Set Values
##################################################################

#If running manually, comment this out before the first execution to login to the Azure Portal:

#Add-AzureAccount

#Set the name of the Application Insights Resource
$appInsightsName = "TestApp"

#Set the application name used for the value of the Tag "AppInsightsApp" - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

#Set the name of the Resource Group to use.  By default will use the application name as a starter
$resourceGroupName = "MyAppResourceGroup"

##################################################################
# Create the Resource and Output the name and iKey
##################################################################
#Set the script to Resource Manager - http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/
Switch-AzureMode AzureResourceManager

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

#Create the App Insights Resource
$resource = New-AzureResource -ResourceName $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01" -PropertyObject @{"Type"="ASP.NET"} -Force -OutputObjectFormat New

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## iKey로 수행할 작업

각 리소스는 해당 계측 키(iKey)로 식별됩니다. iKey는 리소스 생성 스크립트의 출력입니다. 빌드 스크립트는 앱에 포함된 Application Insights SDK에 iKey를 제공해야 합니다.

SDK에 사용할 수 있는 iKey에는 두 가지가 있습니다:
  
* [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서: 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* 또는 [초기화 코드](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`





 

<!---HONumber=Nov15_HO1-->