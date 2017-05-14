---
title: "OMS에서 IT Service Management Connector에 연결할 Service Manager 웹앱을 만드는 자동화 스크립트 | Microsoft Docs"
description: "자동화 스크립트를 사용하여 OMS의 IT Service Management Connector에 연결하고 ITSM 작업 항목을 중앙에서 모니터링 및 관리하는 Service Manager 웹앱을 만듭니다."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 879e819f-d880-41c8-9775-a30907e42059
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 66d643088da9e071ce8d440c8300e1f8124d00aa
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---

# <a name="create-service-manager-web-app-using-the-automated-script-preview"></a>자동화 스크립트를 사용하여 Service Manager 웹앱 만들기(미리 보기)

다음 스크립트를 사용하여 Service Manager 인스턴스에 대한 웹앱을 만듭니다. Service Manager 연결에 대한 자세한 내용은 [Service Manager 웹앱](log-analytics-itsmc-connections.md#create-and-deploy-service-manager-web-app-service)에 나와 있습니다.

다음 필수 정보를 제공하여 스크립트를 실행합니다.

- Azure 구독 정보
- 리소스 그룹 이름
- 위치
- Service Manager 서버 세부 정보(서버 이름, 도메인, 사용자 이름 및 암호)
- 배포용 URL
- 웹앱에 대한 사이트 이름
- BizTalk 서비스 이름.

이 스크립트는 사용자가 지정한 이름(및 웹앱을 고유하게 만드는 몇 가지 추가 설정)을 사용하여 웹앱을 만듭니다. **웹앱 URL**, **클라이언트 ID** 및 **클라이언트 암호**를 생성합니다.

이러한 값을 저장합니다. IT Service Management Cconnector와의 연결을 만들 때 필요합니다.

## <a name="prerequisites"></a>필수 조건

 Windows Management Framework 5.0 이상
Windows 10에는 기본적으로 5.1 버전이 있습니다. [여기](https://www.microsoft.com/download/details.aspx?id=53347)에서 이 Framework를 다운로드할 수도 있습니다.

다음 스크립트를 사용합니다.

```
###################################

# User Configuration Section Begins
####################################

# Subscription name in Azure account. Check in Azure Portal.
$azureSubscriptionName = ""

# Resource group name for resource deployment. Could be an existing resource group or a new one to be created.
$resourceGroupName = ""

# Location for Resource group deployment
################################### List of available regions #################################################
# centralus,eastasia,southeastasia,eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,
# northeurope,westeurope,japaneast,japanwest,brazilsouth,australiasoutheast,australiaeast,westindia,southindia,
# centralindia,canadacentral,canadaeast,uksouth,ukwest.
###############################################################################################################
$location = ""

# Service Manager Authentication Settings
$serverName = ""
$domain = ""
$username = ""
$password = ""


# Site Name Prefix. Default is "smoc". It can be configured to any desired value.
$siteNamePrefix = ""

# BizTalk Service Name. Please provide an already existing biz talk service name. If it doesn't exist, a new one with that name will be created.
$serviceName = ""

##################################
# User Configuration Section Ends
##################################

################
# Installations
################

# Allowing the execution of the script for current user.  
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

Write-Host "Checking for required modules..."
if(!(Get-PackageProvider -Name NuGet))
{
   Write-Host "Installing NuGet Package Provider..."
   Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Scope CurrentUser -Force -WarningAction SilentlyContinue
}
$module = Get-Module -ListAvailable -Name AzureRM
if(!$module -or ($module.Version.Major -lt 3))
{
    Write-Host "Installing AzureRm Module..."  
    try
    {
        # In case of Win 10 Anniversary update
        Install-Module AzureRM -MinimumVersion 3.3.0 -Scope CurrentUser -Force -WarningAction SilentlyContinue -AllowClobber
    }
    catch
    {
        Install-Module AzureRM -MinimumVersion 3.3.0 -Scope CurrentUser -Force -WarningAction SilentlyContinue
    }

}

Write-Host "Requirement check complete!!"

#############
# Parameters
#############

$errorActionPreference = "Stop"

$templateUri = "https://raw.githubusercontent.com/SystemCenterServiceManager/SMOMSConnector/master/azuredeploy.json"

if(!$siteNamePrefix)
{
    $siteNamePrefix = "smoc"
}

Add-AzureRmAccount

$context = Set-AzureRmContext -SubscriptionName $azureSubscriptionName -WarningAction SilentlyContinue
do
{
    $rand = Get-Random -Maximum 32000

    $siteName = $siteNamePrefix + $rand

    $resource = Find-AzureRmResource -ResourceNameContains $siteName -ResourceType Microsoft.Web/sites

}while($resource)

$azureSite = "https://"+$siteName+".azurewebsites.net"

##############
# MAIN Begins
##############

# Web App Deployment
####################

$tenant = $context.Tenant.TenantId

try
{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch
{
    New-AzureRmResourceGroup -Location $location -Name $resourceGroupName
}

Write-Output "Web App Deployment in progress...."

New-AzureRmResourceGroupDeployment -TemplateUri $templateUri -siteName $siteName -ResourceGroupName $resourceGroupName

Write-Output "Web App Deployed successfully!!"

# AAD Authentication
####################

Add-Type -AssemblyName System.Web

$clientSecret = [System.Web.Security.Membership]::GeneratePassword(30,2).ToString()

try
{

    Write-Host "Creating AzureAD application..."

    $adApp = New-AzureRmADApplication -DisplayName $siteName -HomePage $azureSite -IdentifierUris $azureSite -Password $clientSecret

    Write-Host "AzureAD application created succesfully!!"
}
catch
{
    # Delete the deployed web app if Azure AD application fails
    Remove-AzureRmResource -ResourceGroupName $resourceGroupName -ResourceName $siteName -ResourceType Microsoft.Web/sites -Force

    Write-Host "Faiure occured in Azure AD application....Try again!!"

    exit

}

$clientId = $adApp.ApplicationId

$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $clientId

# Web App Configuration
#######################

Write-Host "Configuring deployed Web-App..."
$webApp = Get-AzureRMWebAppSlot -ResourceGroupName $resourceGroupName -Name $siteName -Slot production -WarningAction SilentlyContinue

$appSettingList = $webApp.SiteConfig.AppSettings

$appSettings = @{}
ForEach ($item in $appSettingList) {
    $appSettings[$item.Name] = $item.Value
}
$appSettings['ida:Tenant'] = $tenant
$appSettings['ida:Audience'] = $azureSite
$appSettings['ida:ServerName'] = $serverName
$appSettings['ida:Domain'] = $domain
$appSettings['ida:Username'] = $userName

$connStrings = @{}
$kvp = @{"Type"="Custom"; "Value"=$password}
$connStrings['ida:Password'] = $kvp

Set-AzureRMWebAppSlot -ResourceGroupName $resourceGroupName -Name $siteName -AppSettings $appSettings -ConnectionStrings $connStrings -Slot production -WarningAction SilentlyContinue

# Biz Talk Service
###################

if(!$serviceName)
{
    $serviceName = "ITSMbiz"
}
$resource = Find-AzureRmResource -ResourceNameContains $serviceName -ResourceType Microsoft.BizTalkServices/BizTalk

if(!$resource)
{
    $properties = @{
                    "sku"= @{
                                                "name" = "Free"
                                                "unitCount" = 1
        }
    }
    try
    {
        New-AzureRmResource -ResourceName $serviceName -Location "West US" -PropertyObject $properties -ResourceGroupName $resourceGroupName -ResourceType Microsoft.BizTalkServices/BizTalk -ApiVersion 2014-04-01-preview -Force
    }
    catch
    {
        "Creation of BizTalk Service failed...Please create it manually from Azure Portal.`n"
    }

}

Write-Host "Note: Please Configure Hybrid connection in the Networking section of the application in Azure Portal to link to the on-premises system.`n"
Write-Host "App Details"
Write-Host "============"
Write-Host "App Name:"  $siteName
Write-Host "Client Id:"  $clientId
Write-Host "Client Secret:"  $clientSecret
Write-Host "URI:"  $azureSite

```
## <a name="next-steps"></a>다음 단계
[하이브리드 연결 구성](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

