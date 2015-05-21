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
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# 고급 템플릿 작업

이 항목에서는 Azure에 리소스를 배포할 때 다양한 고급 작업을 수행하는 데 사용할 수 있는 복사 작업과 중첩된 템플릿에 대해 설명합니다.

## 복사

배열을 통해 반복적으로 사용하고 리소스를 배포할 때 각 요소를 사용할 수 있도록 해줍니다.
   
다음 예에서는 examplecopy-Contoso, examplecopy-Fabrikam, examplecopy-Coho라는 세 개의 웹사이트를 배포합니다.

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

## 중첩된 템플릿

경우에 따라 두 개의 템플릿을 병합하거나 부모에서 자식 템플릿을 시작해야 할 수 있습니다. 이렇게 하려면 마스터 템플릿 내의 배포 리소스를 사용하여 자식 템플릿을 배포할 수 있습니다. 아래와 같이 중첩된 템플릿의 URI를 제공합니다.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## 다음 단계
- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)
- [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](./resouce-group-template-deploy.md)
- [Azure 리소스 관리자 개요](./resource-group-overview.md)

<!--HONumber=52-->
