<properties
   pageTitle="리소스의 여러 인스턴스를 배포 | Microsoft Azure"
   description="Azure 리소스 관리자 템플릿에서 복사 작업 및 배열을 사용하여 여러 번 반복하는 방법을 설명합니다."
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
   ms.date="08/27/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기

이 항목에서는 Azure 리소스 관리자 템플릿을 반복하여 리소스의 여러 인스턴스를 만드는 방법을 보여 줍니다.

## copy, copyIndex 및 length

여러 번 만들려는 리소스 내에서 반복 횟수를 지정하는 **copy** 개체를 정의할 수 있습니다. copy는 다음 형식을 갖습니다.

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

아래의 concat 함수에서 볼 수 있듯이 **copyIndex()** 함수로 현재 반복 값에 액세스할 수 있습니다.

    [concat('examplecopy-', copyIndex())]

값의 배열에서 여러 리소스를 만들 때 **lenth** 함수를 사용하여 개수를 지정할 수 있습니다. length 함수에 대한 매개 변수로 배열을 제공합니다.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## 이름에 인덱스 값 사용

복사 작업을 사용하여 증분 인덱스를 기준으로 고유하게 명명되는 리소스의 여러 인스턴스를 만들 수 있습니다. 예를 들어 배포되는 각 리소스 이름의 끝에 고유 번호를 추가할 수 있습니다. 다음 이름의 웹 사이트 3개를 배포하려면

- examplecopy-0
- examplecopy-1
- examplecopy-2.

다음 템플릿을 사용합니다.

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

## 인덱스 값 오프셋

인덱스 값이 0에서 2로 이동하는 이전 예제에서 알 수 있습니다. 인덱스 값을 오프셋하여 값을 **copyIndex(1)**과 같은 **copyIndex()** 함수를 전달할 수 있습니다. 수행할 반복 수는 복사 요소에서 지정되지만 copyIndex의 값이 지정된 값 만큼 오프셋됩니다. 따라서 이전 예제와 같은 서식 파일을 사용하지만 **copyIndex(1)**를 지정하여 다음과 같이 명명된 3개의 웹 사이트를 배포합니다.

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 배열 사용
   
복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 특히 유용합니다. 다음 이름의 웹 사이트 3개를 배포하려면

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

다음 템플릿을 사용합니다.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
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
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {} 
      } 
    ]

물론, 복사본 개수를 배열 길이가 아닌 다른 값으로 설정합니다. 예를 들어 많은 값이 포함된 배열을 만든 다음 배포할 배열 요소 수를 지정하는 매개 변수 값을 전달할 수 있습니다. 이 경우 첫 번째 예제와 같이 복사본 개수를 설정합니다.

## 다음 단계
- 템플릿 섹션에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)을 참조하세요.
- 템플릿에서 사용할 수 있는 모든 함수는 [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)를 참조하세요.
- 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](azure-portal/resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=Oct15_HO3-->