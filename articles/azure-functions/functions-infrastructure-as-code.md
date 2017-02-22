---
title: "Azure Functions 앱에 대한 리소스 배포 자동화 | Microsoft Docs"
description: "Azure Functions 앱을 배포하는 Azure Resource Manager 템플릿을 빌드하는 방법을 알아봅니다."
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 서버 없는 아키텍처, 코드로서의 인프라, Azure Resource Manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Azure Functions 앱에 대한 리소스 배포 자동화

Azure Resource Manager 템플릿을 사용하여 Azure Functions 앱을 배포할 수 있습니다. Azure Functions 앱에 필요한 기본 리소스와 배포 중에 지정하는 매개 변수를 정의하는 방법에 대해 알아봅니다. Functions 앱의 [트리거 및 바인딩](functions-triggers-bindings.md)에 따라 응용 프로그램의 성공적인 코드로 인프라(Infrastructure as Code) 구성을 위해 추가 리소스를 배포해야 할 수도 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하십시오.

전체 템플릿 예제는 [소비 계획 기반 Azure Functions 앱 만들기](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json)(영문) 및 [App Service 계획 기반 Azure Functions 앱 만들기](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)(영문)를 참조하세요.

## <a name="required-resources"></a>필요한 리소스

이 문서의 예제를 사용하면 기본 Azure Functions 앱을 만들 수 있습니다. 앱에 필요한 리소스는 다음과 같습니다.

* [Azure Storage](../storage/index.md) 계정
* 호스팅 계획(소비 계획 또는 Azure App Service 계획)
* Functions 앱(`type`: **Microsoft.Web/Site**, `kind`: **functionapp**)

## <a name="parameters"></a>매개 변수

Azure Resource Manager를 사용하여 템플릿을 배포할 때 지정할 값의 매개 변수를 정의할 수 있습니다. 템플릿의 **매개 변수** 섹션에는 모든 매개 변수 값이 있습니다. 배포하는 프로젝트 또는 배포되는 환경에 따라 달라지는 값의 매개 변수를 정의합니다.

[변수](../azure-resource-manager/resource-group-authoring-templates.md#variables)는 개별 배포에 따라 변경되지 않는 값 또는 템플릿에 사용되기 전에 변환해야 하는 매개 변수(예: 유효성 검사 규칙을 통과하기 위해)에 유용합니다.

매개 변수를 정의할 때는 **allowedValues** 필드를 사용하여 배포 중에 사용자가 제공할 수 있는 값을 지정합니다. 배포 중에 값이 제공되지 않을 경우 매개 변수에 값을 할당하려면 **defaultValue** 필드를 사용합니다.

Azure Resource Manager 템플릿에서 사용하는 매개 변수는 다음과 같습니다.

### <a name="appname"></a>appName

만들려는 Azure Functions 앱의 이름입니다.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Functions 앱을 배포하는 위치입니다.

> [!NOTE]
> 리소스 그룹의 위치를 상속하거나, PowerShell 또는 Azure CLI 배포 중에 매개 변수 값을 지정하지 않은 경우에는 **defaultValue** 매개 변수를 사용합니다. Azure Portal에서 앱을 배포하는 경우 **allowedValues** 매개 변수 드롭다운 상자에서 값을 선택합니다.

> [!TIP]
> Azure Functions를 사용할 수 있는 최신 지역 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL(옵션)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch(옵션)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration(옵션)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>변수

Azure Resource Manager 템플릿은 변수를 사용하여 매개 변수를 통합하므로 템플릿에서 보다 구체적인 설정을 사용할 수 있습니다.

다음 예제에서는 Azure Storage 계정 [명명 요구 사항](../storage/storage-create-storage-account.md#create-a-storage-account)을 충족하기 위해 변수를 통해 [Azure Resource Manager 템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)를 적용하여 입력된 **appName** 값을 소문자로 변환합니다.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>배포할 리소스

### <a name="storage-account"></a>저장소 계정

Azure Functions 앱에는 Azure Storage 계정이 필요합니다.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>호스팅 계획: 소비 및 App Service

일부 시나리오에서는 플랫폼에 따라 함수 크기를 주문형으로 조정할 수 있으며, 소비 호스팅 계획을 사용하여 완전히 관리되는 크기 조정이라고도 합니다. 또는 함수에 대한 사용자 관리 크기 조정을 선택할 수도 있습니다. 사용자 관리 크기 조정에서 함수는 App Service 호스팅 계획을 사용하여 전용 하드웨어에서 연중 무휴로 실행됩니다. 인스턴스 수는 수동 또는 자동으로 설정할 수 있습니다. 호스팅 계획을 선택하는 경우 계획에 사용할 수 있는 기능 또는 비용별 설계 기능에 따라 선택할 수 있습니다. 호스팅 계획에 대한 자세한 내용은 [Azure Functions 크기 조정](functions-scale.md)을 참조하세요.

#### <a name="consumption-plan"></a>소비 계획

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>앱 서비스 계획

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="functions-app-a-site"></a>Functions 앱(사이트)

크기 조정 옵션을 선택한 후에는 Functions 앱을 만듭니다. 앱은 모든 함수를 포함하는 컨테이너입니다.

Functions 앱에는 앱 설정 및 소스 제어 옵션을 포함하여 배포에 사용할 수 있는 자식 리소스가 많이 있습니다. 또한 **sourcecontrols** 자식 리소스를 제거하고 대신에 다른 [배포 옵션](functions-continuous-deployment.md)을 사용하도록 선택할 수 있습니다.

> [!IMPORTANT]
> Azure Resource Manager를 사용하여 응용 프로그램의 성공적인 코드로 인프라 구성을 만들려면 Azure에 리소스를 배포하는 방법을 이해하는 것이 중요합니다. 다음 예제에서는 **siteConfig**를 사용하여 최상위 수준 구성을 적용합니다. Azure Functions 런타임 및 배포 엔진에 정보를 전달하기 때문에 최상위 수준에서 이러한 구성을 설정하는 것이 중요합니다. **sourcecontrols/web** 자식 리소스를 적용하기 전에 최상위 수준 정보가 필요합니다. 일부 시나리오에서는 **config/appSettings** 자식 수준 리소스에 이러한 설정을 구성할 수 있지만**config/appSettings** 리소스를 적용하기 *전에* Functions 앱과 함수를 배포해야 합니다. 이러한 경우, 예를 들면 [Logic Apps](../logic-apps/index.md)에서 함수는 다른 리소스의 종속성입니다.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 이 템플릿은 Functions 배포 엔진(Kudu)에서 배치할 수 있는 코드를 검색할 기본 디렉터리를 설정하는 [프로젝트](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 앱 설정 값을 사용합니다. 리포지토리에서 함수는 **src** 폴더의 하위 폴더에 있습니다. 따라서 앞의 예제에서 앱 설정 값은 `src`로 설정합니다. 함수가 리포지토리의 루트에 있거나 소스 제어에서 배포하지 않는 경우 이 앱 설정 값을 제거할 수 있습니다.

## <a name="deploy-your-template"></a>템플릿 배포

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure 단추에 배포

```<url-encoded-path-to-azuredeploy-json>```을 GitHub에 있는 `azuredeploy.json` 파일의 원시 경로에 대한 [URL 인코딩](https://www.bing.com/search?q=url+encode) 버전으로 바꿉니다.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>다음 단계

Azure Functions를 개발하고 구성하는 방법에 대해 자세히 알아봅니다.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 앱 설정을 구성하는 방법](functions-how-to-use-azure-function-app-settings.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


