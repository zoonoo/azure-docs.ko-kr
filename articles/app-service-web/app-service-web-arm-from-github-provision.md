---
title: GitHub 리포지토리에 연결된 웹앱 배포
description: Azure 리소스 관리자 템플릿을 사용하여 GitHub 리포지토리에서 프로젝트가 포함된 웹앱을 배포합니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: cephalin

---
# GitHub 리포지토리에 연결된 웹앱 배포
이 항목에서는 GitHub 리포지토리의 프로젝트에 연결된 웹앱을 배포하는 Azure 리소스 관리자 템플릿을 만드는 방법을 배웁니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 알게 됩니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../resource-group-authoring-templates.md)를 참조하세요.

전체 서식 파일을 보려면 [GitHub 템플릿에 연결된 웹앱](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json)을 참조하세요.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 배포할 내용
이 템플릿을 사용하여 GitHub의 프로젝트에서 코드를 포함하는 웹앱을 배포합니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)로 바꿉니다.

## 매개 변수
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### repoURL
배포하는 프로젝트를 포함하는 GitHub 리포지토리에 대한 URL입니다. 이 매개 변수는 기본값을 포함하지만 이 값은 어떻게 저장소에 대한 URL을 제공하는지 보여주기 위해 의도적으로 지정되었습니다. 템플릿을 테스트할 때 이 값을 사용할 수 있지만, 템플릿을 사용하여 작업하는 경우 사용자 고유 저장소에 UIRL을 제공할 때도 필요합니다.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### 분기
응용 프로그램을 배포할 때 사용하는 저장소의 분기입니다. 기본값은 master, 하지만 배포 하고자 하는 저장소의 모든 분기의 이름을 제공할 수 있습니다.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }

## 배포할 리소스
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### 웹앱
GitHub의 프로젝트에 연결된 웹앱 만들기

**siteName** 매개 변수를 통해 웹앱의 이름을 지정하고 웹앱의 위치는 **siteLocation** 매개 변수를 통해 지정합니다. **dependsOn** 요소에서 템플릿은 서비스 호스팅 계획에 따라 웹앱을 달리 정의합니다. 이것은 호스팅 계획에 따라 달라지기 때문에 호스팅 계획 만들기가 끝날 때까지 웹앱이 생성되지 않습니다. **dependsOn** 요소는 배포 순서를 지정하는 데만 사용됩니다. 호스팅을 계획하기 전에 웹앱을 만드는 경우 호스팅 계획에 따라 달라지는 웹앱을 표시하지 않으면 Azure 리소스 관리자가 동시에 두 리소스를 만들려 해서 오류가 표시될 수 있습니다.

또한 웹앱에는 아래 **리소스** 섹션에 정의된 자식 리소스가 있습니다. 이 자식 리소스는 웹앱과 함께 배포된 프로젝트에 대한 소스 제어를 정의합니다. 이 템플릿에서 소스 제어는 특정 GitHub 리포지토리에 연결됩니다. GitHub 리포지토리에 코드 **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"**로 정의됩니다. 최소한의 매개 변수를 필요로 하는 동안 반복해서 단일 프로젝트를 배포하는 템플릿을 만드는 경우 리포지토리 URL를 하드 코딩할 수 있습니다. 하드 코드한 리포지토리 URL 대신에 리포지토리 URL에 대한 매개 변수를 추가하고 해당 값을 **RepoUrl** 속성에 사용할 수 있습니다.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## 배포 실행 명령
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json




<!---HONumber=AcomDC_0504_2016-->