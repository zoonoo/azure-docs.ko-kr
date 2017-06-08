---
title: "Azure에서 템플릿을 사용하여 논리 앱 만들기 | Microsoft Docs"
description: "Azure Resource Manager 템플릿을 사용하여 워크플로를 정의하기 위한 논리 앱을 배포합니다."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 1305fdc4cf87905eef701ec5471d6329006c2b5f
ms.contentlocale: ko-kr
ms.lasthandoff: 01/31/2017


---
# <a name="create-a-logic-app-using-a-template"></a>템플릿을 사용하여 논리 앱 만들기
템플릿은 논리 앱 내에서 다양한 커넥터를 사용할 수 있는 빠른 방법을 제공합니다. Logic Apps에는 비즈니스 워크플로를 정의하는 데 사용할 수 있는 논리 앱을 만들 수 있도록 Azure Resource Manager 템플릿이 포함됩니다. 배포할 리소스를 정의하고 논리 앱을 배포할 때 매개 변수를 정의하는 방법을 정의할 수 있습니다. 이 템플릿을 자체 비즈니스 시나리오에 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

논리 앱 속성에 대한 자세한 내용은 [논리 앱 워크플로 관리 API](https://msdn.microsoft.com/library/azure/mt643788.aspx)를 참조하십시오. 

정의 자체의 예는 [작성자 논리 앱 정의](logic-apps-author-definitions.md)를 참조하십시오. 

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하십시오.

전체 템플릿에 대해서는 [논리 앱 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)을 참조하십시오.

## <a name="what-you-deploy"></a>배포할 내용
이 서식 파일로 논리 앱을 배포합니다:

배포를 자동으로 실행하려면 다음 단추를 선택합니다.  

[![Azure에 배포](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>매개 변수
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>배포할 리소스
### <a name="logic-app"></a>논리 앱
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


## <a name="commands-to-run-deployment"></a>배포 실행 명령
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup




