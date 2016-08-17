<properties 
	pageTitle="Azure 앱 서비스에서 Azure 리소스 관리자 템플릿을 사용하여 논리 앱 만들기 | Microsoft Azure" 
	description="Azure 리소스 관리자 템플릿을 사용하여 워크플로를 정의하기 위한 빈 논리 앱을 배포합니다." 
	services="logic-apps" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="deonhe"/>

# 템플릿을 사용하여 논리 앱 만들기

Azure 리소스 관리자 템플릿을 사용하여 워크플로 정의에 사용할 수 있는 빈 논리 앱을 만듭니다. 배포할 리소스를 배포를 실행할 때 매개 변수를 지정하는 방법을 정의할 수 있습니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

논리 앱 속성에 대한 자세한 내용은 [논리 앱 워크플로 관리 API](https://msdn.microsoft.com/library/azure/mt643788.aspx)를 참조하십시오.

정의 자체의 예는 [작성자 논리 앱 정의](app-service-logic-author-definitions.md)를 참조하십시오.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../resource-group-authoring-templates.md)를 참조하십시오.

전체 템플릿에 대해서는 [논리 앱 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)을 참조하십시오.

## 배포할 내용

이 서식 파일로 논리 앱을 배포합니다:

배포를 자동으로 실행하려면 다음 단추를 선택합니다.

[![Azure에 배포](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)로 바꿉니다.

## 매개 변수

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## 배포할 리소스

### 논리 앱

논리 앱을 만듭니다.

템플릿은 논리 앱 이름에 대한 매개 변수 값을 사용합니다. 논리 앱의 위치를 리소스 그룹과 같은 위치로 설정합니다.

이 특정 정의는 한 시간에 한 번 실행되며, **testUri** 매개 변수에서 지정된 위치를 ping합니다.

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## 배포 실행 명령

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=AcomDC_0803_2016-->