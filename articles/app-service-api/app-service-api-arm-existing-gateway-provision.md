<properties 
	pageTitle="기존 게이트웨이로 API 앱 배포" 
	description="기존의 게이트웨이 및 앱 서비스 계획을 사용하는 API 앱을 배포하기 위해 Azure 리소스 관리자 템플릿을 사용합니다." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="tomfitz"/>

# 기존 게이트웨이로 API 앱을 프로비전

이 항목에서는 Azure API 앱 및 기존 게이트웨이를 배포하는 Azure 리소스 관리자 템플릿을 만드는 방법을 배웁니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 알게 됩니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../resource-group-authoring-templates.md)를 참조하세요.

앱 배포에 대한 자세한 내용은 [Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](../app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하십시오.

전체 템플릿은 [기존 게이트웨이 템플릿과 API 앱](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-existing/azuredeploy.json)을 참조하십시오.

## 배포할 내용

이 템플릿에서 계획 및 기존 게이트웨이를 호스팅하는 기존 앱 서비스와 연결된 API 앱을 배포할 것입니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-existing%2Fazuredeploy.json)로 바꿉니다.

## 매개 변수

[AZURE.INCLUDE [앱-서비스-api-배포-매개 변수](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

기존 앱 서비스 호스팅 계획의 식별자입니다.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

기존 호스팅 계획의 설정입니다.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## 변수

이 템플릿이 리소스를 배포할 때 사용되는 변수를 정의합니다.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
값은 아래와 같이 **variables('packageId')**으로 사용됩니다. API 앱에 대한 NuGet 패키지 Id를 포함합니다.

## 배포할 리소스

### API 앱을 호스트할 웹 앱

API 앱을 호스팅하는 웹 앱을 만듭니다.

**종류**는 해당 웹앱이 API 앱을 호스팅하는 Azure 포털임을 알리는 **apiApp**으로 설정됩니다. 포털은 웹앱 블레이드 찾아보기에서 웹앱을 숨깁니다. 앱은 기본 빈 API 앱 패키지를 설치하기 위해 확장을 포함합니다. 링크는 API 앱과 호스팅 웹 앱 간에 정의됩니다. 앱 설정 섹션에는 API 앱 호스팅에 필요한 값이 포함됩니다. **serverFarmId** 속성은 **hostingPlanId** 매개 변수에서 제공한 값으로 설정됩니다.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      }
    }

### API 앱

API 앱을 만듭니다.

호스팅 웹 앱 및 게이트웨이의 이름을 API 앱에서 속성으로 정의됩니다.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }


## 배포 실행 명령

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json


 

<!---HONumber=Oct15_HO4-->