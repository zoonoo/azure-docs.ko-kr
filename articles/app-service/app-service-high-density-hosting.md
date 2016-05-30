<properties 
	pageTitle="Azure 앱 서비스의 고밀도 호스팅" 
	description="Azure 앱 서비스의 고밀도 호스팅" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/17/2016" 
	ms.author="byvinyal"/>

#Azure 앱 서비스의 고밀도 호스팅#

##앱 크기 조정 이해##

앱 서비스를 사용할 경우 응용 프로그램은 2가지 개념에 의해 할당된 용량에서 분리됩니다.
 
>**응용 프로그램:** 앱 및 해당 런타임 구성을 나타냅니다. 예를 들어, 런타임이 어떤 버전의 .NET 로드해야 하나요? 앱 설정이란? 등입니다.

>**앱 서비스 계획:** 응용 프로그램의 용량, 사용 가능한 기능 집합 및 거주지에 대한 특징을 정의합니다. 예를 들어 대량(4개 코어) 컴퓨터, 인스턴스 4개, 미국 동부의 프리미엄 기능입니다.

앱이 **앱 서비스 계획**에 항상 연결되어 있지만 **앱 서비스 계획**은 하나 이상의 앱에 대한 용량을 제공할 수 있습니다.

즉, 플랫폼에서는 **앱 서비스 계획**을 공유하여 단일 앱을 격리하거나 여러 앱 공유 리소스를 가지도록 유연성을 제공합니다.

그러나 여러 앱이 **앱 서비스 계획**을 공유하는 경우 각각에서 실행되는 해당 앱의 인스턴스 및 해당 **앱 서비스 계획**의 인스턴스가 있습니다.

##앱 크기 조정당##
**앱 크기 조정당**은 **앱 서비스 계획** 수준에서 사용할 수 있는 기능이며 응용 프로그램 마다 활용됩니다.

**앱 크기 조정당**을 사용하면 호스트하는 데 사용되는 **앱 서비스 계획**과 별개로 앱 크기를 조정할 수 있습니다. 이러한 방식으로 **앱 서비스 계획**은 10개의 인스턴스를 제공하도록 구성될 수 있지만 앱은 그 중의 5개만 크기를 조정하도록 설정될 수 있습니다.

다음의 ARM 템플릿은 10개의 인스턴스로 규모를 확장한 **앱 서비스 계획**을 만들고 **앱 크기 조정당**을 사용하여 5개의 인스턴스로만 크기를 확장하도록 구성됩니다.

이렇게 하려면 앱 서비스 계획은 true( `"perSiteScaling": true`)로 설정된 사이트 규모 조정당 속성을 설정하고 앱은 1 `"properties": { "numberOfWorkers": "1" }`에 사용할 작업자의 수를 설정합니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
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
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


##고밀도 호스팅에 대한 권장된 구성##

**앱 크기 조정당**은 앱 서비스 환경 뿐만 아니라 공용 Azure 지역에서도 사용할 수 있는 기능이지만 권장되는 전략은 앱 서비스 환경을 사용하여 해당 고급 기능 및 더 큰 풀 용량을 활용합니다.

앱에 **고밀도 호스팅**를 구성하는 방법에 대한 지침으로 아래에 나열된 단계를 수행합니다.

1. **앱 서비스 환경**을 구성하고 *고밀도 호스팅* 시나리오에 전용으로 사용되는 **작업자 풀**을 선택합니다.

1. 단일 **앱 서비스 계획**을 만들고 확장하여 **작업자 풀**에서 모든 사용 가능한 용량을 사용합니다.

1. **앱 서비스 계획**에서 사이트 크기 조정 플래그당을 true로 설정합니다.

1. 새 사이트가 생성되고 *1*로 설정된 *numberOfWorkers* 속성이 있는 해당 **앱 서비스 계획**에 할당됩니다. **작업자 풀**에서 가능한 고밀도가 발생합니다.

1. 작업자 수는 사이트마다 독립적으로 구성되어 필요에 따라 추가 리소스를 부여할 수 있습니다. 예를 들어, 높은 사용량 사이트는 *numberOfWorkers*를 *3*으로 설정하여 앱에 대한 처리 용량을 늘리는 반면 낮은 사용량 사이트는 *numberOfWorkers*를 *1*로 설정합니다.

<!---HONumber=AcomDC_0518_2016-->