<properties
   pageTitle="Azure 리소스 관리자 고급 템플릿 작업"
   description="Azure에 앱을 배포할 때 Azure 리소스 관리자 템플릿에서 중첩된 템플릿 및 복사 작업을 사용하는 방법을 설명합니다."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# 고급 템플릿 작업

이 항목에서는 Azure에 리소스를 배포할 때 다양한 고급 작업을 수행하는 데 사용할 수 있는 복사 작업과 중첩된 템플릿에 대해 설명합니다.

## 복사

리소스를 배포할 때 지정된 횟수 만큼 반복합니다.
   
복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 특히 유용합니다. **copyIndex()** 함수는 반복에 대한 현재 값을 반환합니다. 명명된 3개의 웹 사이트를 배포할 수 있습니다.

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

다음 템플릿으로

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

또한 배열 없이 복사 작업을 사용할 수 있습니다. 예를 들어 배포되는 각 리소스 이름의 끝에 증분 번호를 추가할 수 있습니다. 명명된 3개의 웹 사이트를 배포할 수 있습니다.

- examplecopy-0
- examplecopy-1
- examplecopy-2.

다음 템플릿으로

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

인덱스 값이 0에서 2로 이동하는 이전 예제에서 알 수 있습니다. 인덱스 값을 오프셋하여 값을 **copyIndex(1)**과 같은 **copyIndex()** 함수를 전달할 수 있습니다. 수행할 반복 수는 복사 요소에서 지정되지만 copyIndex의 값이 지정된 값 만큼 오프셋됩니다. 따라서 이전 예제와 같은 서식 파일을 사용하지만 **copyIndex(1)**를 지정하여 다음과 같이 명명된 3개의 웹 사이트를 배포합니다.

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 중첩된 템플릿

두 템플릿을 병합하거나 부모에서 자식 템플릿을 시작해야 할 수 있습니다. 이렇게 하려면 중첩된 템플릿을 가리키는 마스터 템플릿 내의 배포 리소스를 사용합니다. **templateLink** 속성을 중첩된 템플릿의 URI로 설정합니다. 템플릿에 직접 값을 지정하거나 매개 변수 파일에 연결하여 매개 변수 값을 중첩된 서식 파일에 제공할 수 있습니다. 첫번째 예제는 **매개 변수** 속성을 사용하여 직접 매개 변수 값을 지정합니다.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

다음 예제는 **parametersLink** 속성을 사용하여 매개 변수 파일에 연결합니다.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## 다음 단계
- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)
- [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](azure-portal/resource-group-template-deploy.md)
- [Azure 리소스 관리자 개요](./resource-group-overview.md)

<!---HONumber=July15_HO1-->