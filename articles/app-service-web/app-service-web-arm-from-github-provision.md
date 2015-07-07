<properties 
	pageTitle="GitHub 리포지토리에 연결된 웹 앱 배포" 
	description="Azure 리소스 관리자 템플릿을 사용하여 GitHub 리포지토리에서 프로젝트가 포함된웹 앱을 배포합니다." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# GitHub 리포지토리에 연결된 웹 앱 배포

이 항목에서는 GitHub 리포지토리의 프로젝트에 연결된 웹 앱을 배포하는 Azure 리소스 관리자 템플릿을 만드는 방법을 배웁니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 알게 됩니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../resource-group-authoring-templates.md)를 참조하십시오.

전체 서식 파일을 보려면 [GitHub 템플릿에 연결된 웹 앱](https://github.com/tfitzmac/AppServiceTemplates/blob/master/WebAppLinkedToGithub.json)을 참조하십시오.

## 배포할 내용

이 템플릿을 사용하여 GitHub의 프로젝트에서 코드를 포함하는 웹 앱을 배포합니다.

## 매개 변수

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]
    
## 배포할 리소스

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### 웹 앱

GitHub의 프로젝트에 연결된 웹 앱 만들기

**siteName** 매개 변수를 통해 웹 앱의 이름을 지정하고 웹 앱의 위치는 **siteLocation** 매개 변수를 통해 지정합니다. **dependsOn** 요소에서 템플릿은 서비스 호스팅 계획에 따라 웹 앱을 달리 정의합니다. 이것은 호스팅 계획에 따라 달라지기 때문에 호스팅 계획 만들기가 끝날 때까지 웹 앱이 생성되지 않습니다. **dependsOn** 요소는 배포 순서를 지정하는 데만 사용됩니다. 호스팅을 계획하기 전에 웹 앱을 만드는 경우 호스팅 계획에 따라 달라지는 웹 앱을 표시하지 않으면 Azure 리소스 관리자가 동시에 두 리소스를 만들려 하고 오류가 표시 될 수 있습니다.

또한 웹 앱에는 아래 **리소스** 섹션에 정의된 자식 리소스가 있습니다. 이 자식 리소스는 웹 앱과 함께 배포된 프로젝트에 대한 소스 제어를 정의합니다. 이 템플릿에서 소스 제어는 특정 GitHub 리포지토리에 연결됩니다. GitHub 리포지토리에 코드 **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"**로 정의됩니다. 최소한의 매개 변수를 필요로 하는 동안 반복해서 단일 프로젝트를 배포하는 템플릿을 만드는 경우 리포지토리 URL를 하드 코딩할 수 있습니다. 하드 코드한 리포지토리 URL 대신에 리포지토리 URL에 대한 매개 변수를 추가하고 해당 값을 **RepoUrl** 속성에 사용할 수 있습니다. [웹 작업 템플릿을 사용하는 웹 앱](../app-service-web-deploy-web-app-with-webjobs.md)에서 리포지토리 URL 매개 변수의 예를 볼 수 있습니다.

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## 배포 실행 명령

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json


 

<!---HONumber=62-->