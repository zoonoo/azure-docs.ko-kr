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
   ms.date="12/01/2015"
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
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
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
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

물론, 복사본 개수를 배열 길이가 아닌 다른 값으로 설정합니다. 예를 들어 많은 값이 포함된 배열을 만든 다음 배포할 배열 요소 수를 지정하는 매개 변수 값을 전달할 수 있습니다. 이 경우 첫 번째 예제와 같이 복사본 개수를 설정합니다.

## 루프의 리소스에 따라 달라짐

**dependsOn** 요소를 사용하여 어떤 리소스를 다른 리소스 다음에 배포하도록 지정할 수 있습니다. 루프의 리소스 컬렉션에 따라 달라지는 리소스를 배포해야 하는 경우 **dependsOn** 요소에 copy 루프의 이름을 제공하는 방식으로 사용할 수 있습니다. 다음 예제에서는 가상 컴퓨터를 배포하기 전에 저장소 계정 3개를 배포하는 방법을 보여줍니다. 전체 가상 컴퓨터 정의는 표시되지 않습니다. 참고로 copy 요소의 **name**은 **storagecopy**로 설정되고 가상 컴퓨터에 대한 **dependsOn** 요소는 **storagecopy**로 설정되었습니다.

    {
	    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "contentVersion": "1.0.0.0",
	    "parameters": {},
	    "resources": [
	        {
		        "apiVersion": "2015-06-15",
		        "type": "Microsoft.Storage/storageAccounts",
		        "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
		        "location": "[resourceGroup().location]",
		        "properties": {
                    "accountType": "Standard_LRS"
            	 },
		        "copy": { 
         	        "name": "storagecopy", 
         	        "count": 3 
      		    }
	        },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
	    ],
	    "outputs": {}
    }

## 중첩된 리소스에 대해 반복합니다.

중첩된 리소스에 대해 복사 반복을 사용할 수 없습니다. 일반적으로 다른 리소스 내에 중첩되어 있다고 정의하는 리소스의 여러 인스턴스를 만드는 경우 해당 리소스를 최상위 리소스로 대신 만들어 **형식** 및 **이름** 속성을 통해 부모 리소스와의 관계를 정의해야 합니다.

예를 들어, 데이터 집합을 데이터 팩터리 내에 중첩된 리소스로 정의한다고 가정합니다.

    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[variables('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[variables('dataSetName')]",
            "dependsOn": [
                "[variables('dataFactoryName')]"
            ],
            ...
        }
    }]
    
데이터 집합의 여러 인스턴스를 만들려면 다음과 같이 템플릿을 변경해야 합니다. 정규화된 형식 및 이름이 데이터 팩터리 이름을 포함하는 것을 확인합니다.

    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[variables('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(variables('dataFactoryName'), '/', variables('dataSetName'), copyIndex())]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[parameters('count')]" 
        } 
        ...
    }]

## 다음 단계
- 템플릿 섹션에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)을 참조하세요.
- 템플릿에서 사용할 수 있는 모든 함수는 [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)를 참조하세요.
- 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_1203_2015-->