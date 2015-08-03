<properties
   pageTitle="Azure 리소스 관리자에서 연결된 템플릿 사용"
   description="Azure 리소스 관리자 템플릿에서 연결된 템플릿을 사용하여 모듈식 템플릿 솔루션을 만드는 방법을 설명합니다. 매개 변수 값을 전달하고 매개 변수 파일 및 동적으로 생성된 URL을 지정하는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자에서 연결된 템플릿 사용

하나의 Azure 리소스 관리자 템플릿 내에서 다른 템플릿에 연결하여 배포를 특정 용도의 템플릿 집합으로 분해할 수 있습니다. 응용 프로그램을 여러 코드 클래스로 분해하는 경우처럼 이러한 분해는 테스트, 다시 사용 및 가독성 측면에서 이점을 제공합니다.

주 템플릿의 매개 변수를 연결된 템플릿에 전달할 수 있으며 이러한 매개 변수는 호출하는 템플릿이 노출하는 매개 변수 또는 변수에 직접 매핑될 수 있습니다. 또한 연결된 템플릿은 원본 템플릿에 출력 변수를 다시 전달할 수 있으므로 템플릿 간에 양방향 데이터 교환이 가능해집니다.

## 템플릿에 연결

연결된 템플릿을 가리키는 주 템플릿 내에 배포 리소스를 추가하여 두 템플릿 간에 링크를 만듭니다. **templateLink** 속성을 연결된 템플릿의 URI로 설정합니다. 템플릿에 직접 값을 지정하거나 매개 변수 파일에 연결하여 매개 변수 값을 연결된 템플릿에 제공할 수 있습니다. 다음 예제에서는 **parameters** 속성을 사용하여 매개 변수 값을 직접 지정합니다.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## 매개 변수 파일에 연결

다음 예제는 **parametersLink** 속성을 사용하여 매개 변수 파일에 연결합니다.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## 변수를 사용하여 템플릿 연결

앞의 예제에서는 템플릿 링크에 대한 하드 코딩된 URL 값을 보여 주었습니다. 이 방법은 간단한 템플릿에는 적용될 수 있지만 대규모 모듈식 템플릿 집합으로 작업하는 경우에는 가능하지 않습니다. 대신, 주 템플릿에 대한 기본 URL을 보관하는 정적 변수를 만든 다음 해당 기본 URL에서 연결된 템플릿에 대한 URL을 동적으로 만들 수 있습니다. 이 방식의 경우 주 템플릿에서만 정적 변수를 변경하면 되므로 템플릿을 쉽게 이동하거나 분기할 수 있다는 장점이 있습니다. 주 템플릿은 분해된 템플릿 전체에서 올바른 URI를 전달합니다.

다음 예제에서는 기본 URL을 사용하여 연결된 템플릿에 대해 두 개의 URL을 만드는 방법을 보여 줍니다(**sharedTemplateUrl** 및 **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

## 연결된 템플릿의 값을 다시 전달

연결된 템플릿에서 주 템플릿으로 값을 전달해야 하는 경우 연결된 템플릿의 **출력** 섹션에서 값을 만들 수 있습니다. 예제를 보려면 [Azure 리소스 관리자 템플릿의 상태 공유](best-practices-resource-manager-state.md)를 참조하세요.

## 다음 단계
- [템플릿 작성](./resource-group-authoring-templates.md)
- [템플릿 배포](azure-portal/resource-group-template-deploy.md)

<!---HONumber=July15_HO4-->