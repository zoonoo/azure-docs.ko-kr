---
title: 앱 별 크기 조정을 사용 하는 고밀도 호스팅-Azure App Service | Microsoft Docs
description: Azure App Service의 고밀도 호스팅
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 7130c9547e0778ce40a0ad1c1ea41607a02df23e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088098"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>앱 별 크기 조정을 사용 하는 Azure App Service의 고밀도 호스팅

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service를 사용 하는 경우 실행 되는 [App Service 계획](overview-hosting-plans.md) 의 크기를 조정 하 여 앱을 확장할 수 있습니다. 여러 앱이 같은 App Service 계획에서 실행되는 경우 각 스케일 아웃 인스턴스는 계획의 모든 앱을 실행합니다.

*앱 당 크기 조정을* App Service 계획 수준에서 사용 하도록 설정 하 여 앱을 호스팅하는 App Service 계획과 독립적으로 앱을 확장할 수 있습니다. 이러한 방식으로 App Service 계획은 10개의 인스턴스로 확장될 수 있지만 앱은 5개만 사용하도록 설정될 수 있습니다.

> [!NOTE]
> 앱별 크기 조정은 **표준**, **프리미엄**, **프리미엄 V2** 및 **격리** 가격 책정 계층에서만 사용할 수 있습니다.
>

앱은 인스턴스 간에 균등 하 게 배포 하는 최상의 방법을 사용 하 여 사용 가능한 App Service 계획에 할당 됩니다. 균등 배포는 보장 되지 않지만 플랫폼은 동일한 앱의 두 인스턴스가 동일한 App Service 계획 인스턴스에 호스트 되지 않도록 합니다.

플랫폼은 작업자 할당을 결정 하는 메트릭을 사용 하지 않습니다. 응용 프로그램은 App Service 계획에서 인스턴스를 추가 하거나 제거 하는 경우에만 균형 됩니다.

## <a name="per-app-scaling-using-powershell"></a>PowerShell을 사용하여 앱 크기 조정당

```-PerSiteScaling $true``` 매개 변수를 ```New-AzAppServicePlan``` cmdlet에 전달하여 앱별 크기 조정으로 플랜을 만듭니다.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

`-PerSiteScaling $true` 매개 변수를 ```Set-AzAppServicePlan``` cmdlet에 전달하여 기존 App Service 계획으로 앱별 크기 조정을 사용합니다.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

앱 수준에서 앱이 App Service 계획에 사용할 수 있는 인스턴스 수를 구성합니다.

아래 예제에서 이 앱은 기본 앱 서비스 계획이 규모를 확장하는 인스턴스 수와 상관없이 2개 인스턴스로 제한됩니다.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`는 `$newapp.MaxNumberOfWorkers`와 다릅니다. 앱별 크기 조정은 `$newapp.SiteConfig.NumberOfWorkers`를 사용하여 앱의 크기 조정 특성을 결정합니다.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 앱별 크기 조정

다음 Azure Resource Manager 템플릿은 다음을 만듭니다.

- 10개 인스턴스로 규모를 확장하는 App Service 계획
- 최대 5개 인스턴스로 확장하도록 구성된 앱

App Service 계획은 **PerSiteScaling** 속성을 true(`"perSiteScaling": true`)로 설정합니다. 앱은 **작업자 수**를 5(`"properties": { "numberOfWorkers": "5" }`)로 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>고밀도 호스팅에 대한 권장된 구성

앱별 크기 조정은 전역 Azure 지역 및 [App Service Environment](environment/app-service-app-service-environment-intro.md) 모두에서 사용할 수 있는 기능입니다. 그러나 App Service 환경을 사용 하 여 고급 기능을 활용 하 고 더 큰 App Service 계획 용량을 활용 하는 것이 좋습니다.  

앱에 대해 고밀도 호스팅을 구성하려면 이 단계를 수행합니다.

1. App Service 계획을 고밀도 계획으로 지정 하 고 원하는 용량으로 확장 합니다.
1. App Service 계획에서 `PerSiteScaling` 플래그를 true로 설정합니다.
1. 새 앱이 만들어지고 **1**로 설정된 **numberOfWorkers** 속성이 있는 해당 App Service 계획에 할당됩니다.
   - 이 구성을 사용 하면 가능한 가장 높은 밀도가 생성 됩니다.
1. 작업자 수는 앱마다 독립적으로 구성되어 필요에 따라 추가 리소스를 부여할 수 있습니다. 예:
   - 사용량이 많은 앱은 해당 앱에 대한 더 많은 처리 용량을 갖도록 **numberOfWorkers**를 **3**으로 설정할 수 있습니다.
   - 많이 사용되지 않는 앱은 **numberOfWorkers**를 **1**로 설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure App Service 계획의 포괄 개요](overview-hosting-plans.md)
- [App Service Environment 소개](environment/app-service-app-service-environment-intro.md)
