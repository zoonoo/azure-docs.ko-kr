---
title: "Azure Functions에 대한 리소스 배포 자동화 | Microsoft Docs"
description: "함수 앱을 Microsoft Azure에 배포하는 Azure Resource Manager 템플릿을 작성하는 방법을 알아봅니다."
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
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Azure Functions 앱에 대한 리소스 배포 자동화

이 항목에서는 함수 앱을 배포하는 Azure Resource Manager 템플릿을 빌드하는 방법을 배웁니다. 배포를 실행할 때 지정한 Azure Function 및 매개 변수에 필요한 리소스의 기준선을 정의하는 방법을 알아봅니다. 함수에 사용되는 [트리거 및 바인딩](functions-triggers-bindings.md)에 따라, 전체 응용 프로그램 코드를 코드로서의 인프라로 포함하도록 추가 리소스를 배포해야 할 수 있습니다.

템플릿을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.

전체 템플릿 예제를 보려면 [사용량 기반 Azure Function 만들기](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) 및/또는 [App Service 계획 기반 Azure Function 만들기](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)를 참조하세요.

## <a name="what-is-deployed"></a>배포하는 항목

아래 예제에서는 기준 Azure 함수 앱을 만듭니다. 함수 앱에 필요한 리소스는 다음과 같습니다.

* [Azure Storage](../storage/index.md) 계정
* 호스팅 계획(소비 계획 또는 App Service 계획)
* 함수 앱(**functionapp** 종류의 Microsoft.Web/Site)

## <a name="parameters"></a>매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함 된 매개 변수라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다.

[변수](../azure-resource-manager/resource-group-authoring-templates.md#variables)는 개별 배포에 따라 변경되지 않는 값 또는 템플릿에 사용되기 전에 변환해야 하는 매개 변수(예: 유효성 검사 규칙을 통과하기 위해)에 유용합니다.

매개 변수를 정의할 때는 **allowedValues** 필드를 사용하여 배포 중에 사용자가 제공할 수 있는 값을 지정합니다. 배포 중에 값이 제공되지 않으면 **defaultValue** 필드를 사용하여 매개 변수에 값을 할당합니다.

템플릿의 매개 변수에 대해 알아보겠습니다.

### <a name="appname"></a>appName

만들려는 함수의 이름입니다.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

함수 앱을 배포할 위치입니다.

> [!NOTE]
> **defaultValue** 매개 변수는 리소스 그룹의 위치를 상속하는 데 사용되거나 Powershell 또는 CLI 배포 동안 매개 변수 값이 지정되지 않은 경우에 사용됩니다. Portal에서 배포하는 경우 **allowedValues** 중에 선택할 수 있는 드롭다운 상자가 제공됩니다.

> [!TIP]
> Azure Functions를 사용할 수 있는 최신 지역 목록을 보려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 페이지를 참조하세요.

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
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration(옵션)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>변수

Azure Resource Manager 템플릿에는 매개 변수 외에도, 템플릿에서 사용되는 보다 구체적인 설정을 구축하기 위해 매개 변수를 통합할 수 있는 변수 개념이 사용됩니다.

아래 예제에서는 변수를 통해 [Azure Resource Manager 템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)를 적용하여 제공된 appName을 받아들인 후 소문자로 변환하여 Azure Storage 계정에 대한 [명명 요구 사항](../storage/storage-create-storage-account.md#create-a-storage-account)을 충족하도록 하는 방식을 확인할 수 있습니다.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>배포할 리소스

### <a name="storage-account"></a>저장소 계정

Azure Storage 계정은 Azure Functions의 필수 리소스입니다.

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

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>호스팅 계획: 소비 계획 및 App Service 계획

함수를 빌드할 때 함수가 플랫폼(소비)의 요구에 따라 크기가 조정되도록 하려는 경우가 있습니다. 또는 인스턴스 수를 수동 또는 자동으로 구성할 수 있는 전용 하드웨어(App Service 계획)에서 함수가 연중무휴로 실행되는 사용자 관리 크기 조정을 선택할 수 있습니다. 한 가지 계획을 사용하기로 결정할 때는 해당 계획에서 사용 가능한 기능을 고려해야 하며 비용을 고려해서 결정을 내려야 할 수도 있습니다. 다른 호스팅 계획에 대한 자세한 내용은 [Azure Functions 크기 조정](functions-scale.md) 문서를 참조하세요.

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

### <a name="function-app-site"></a>함수 앱(사이트)

크기 조정 옵션을 선택한 후에는 모든 함수를 포함할 합수 앱이라는 컨테이너를 만들어야 합니다.

함수 앱에는 **앱 설정** 및 **소스 제어 옵션**을 비롯하여 활용 가능한 많은 자식 리소스가 있습니다. 다른 [배포 옵션](functions-continuous-deployment.md)에 따라 **sourcecontrols** 자식 리소스를 제거하도록 선택할 수도 있습니다.

> [!IMPORTANT]
> Azure Resource Manager를 사용할 때 인프라를 응용 프로그램에 대한 코드 구성으로 만들 수 있게 Azure에 리소스를 배포하는 방식을 이해하는 것이 중요합니다. 이 예제에서는 **siteConfig**를 사용하여 최상위 구성이 적용될 것임을 알고 있어야 합니다. 이러한 구성은 자식 리소스 **sourcecontrols/web**이 적용되기 전에 필요한 Azure Functions 런타임 및 배포 엔진에 의미를 전달하므로 최상위 수준에 설정되어야 합니다. 이러한 설정은 하위 수준 리소스 **config/appSettings**에 구성될 수 있으나 함수는 다른 리소스(예: [논리 앱](../logic-apps/index.md))의 종속 항목이므로 **config/appsettings**를 적용하기 *전에* 함수 앱 및 함수가 배포되어야 하는 경우가 발생합니다.

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
> 이 템플릿에서는 [프로젝트](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 앱 설정을 사용합니다. 이러한 설정은 함수 배포 엔진(Kudu)이 배포 가능한 코드를 검색하려는 기본 디렉터리를 지정합니다. 이 예제에서 GitHub 리포지토리는 함수가 자식이 되는 `src` 폴더를 포함하므로 이 값을 `src`로 설정했습니다. 함수가 직접적으로 리포지토리의 루트에 있는 리포지토리가 있거나 소스 제어에서 배포하지 않는 경우 이 앱 설정을 제거할 수 있습니다.

## <a name="deploying-your-template"></a>템플릿 배포

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [포털](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure에 배포 단추

```<url-encoded-path-to-azuredeploy-json>```을GitHub에 있는 `azuredeploy.json` 파일의 원시 경로에 대한 [URL 인코딩](https://www.bing.com/search?q=url+encode) 버전으로 바꿉니다.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>다음 단계

이제 코드에서 함수 앱을 배포할 수 있으므로 Azure Functions를 개발하고 구성하는 방법을 알아보세요.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 앱 설정을 구성하는 방법](functions-how-to-use-azure-function-app-settings.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


