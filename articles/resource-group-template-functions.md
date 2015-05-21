<properties
   pageTitle="Azure 리소스 관리자 템플릿 함수"
   description="Azure에 앱을 배포하기 위해 Azure 리소스 관리자 템플릿에서 사용할 함수에 대해 설명합니다."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Azure 리소스 관리자 템플릿 함수

이 항목에서는 Azure 리소스 관리자 템플릿에서 사용할 수 있는 모든 함수에 대해 설명합니다.

## base64

**base64 \(inputString\)**

입력 문자열의 base64 표현을 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| inputString | 예 | base64 표현으로 반환할 문자열 값입니다.

다음 예에서는 base64 함수를 사용하는 방법을 보여 줍니다.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat \(arg1, arg2, arg3, ...\)**

여러 문자열 값을 결합하고 결과 문자열 값을 반환합니다. 이 함수는 임의의 수의 인수를 사용할 수 있습니다.

다음 예에서는 여러 값을 결합하여 값을 반환하는 방법을 보여 줍니다.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys \(resourceName or resourceIdentifier, [apiVersion]\)**

저장소 계정의 키를 반환합니다. [resourceId 함수](./#resourceid) 또는 **providerNamespace/resourceType/resourceName** 형식을 사용하여 resourceId를 지정할 수 있습니다. 이 함수를 사용하여 primaryKey 및 secondaryKey를 가져올 수 있습니다.
  
| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| resourceName 또는 resourceIdentifier | 예 | 저장소 계정의 고유 식별자입니다.
| apiVersion | 예 | 리소스 런타임 상태의 API 버전입니다.

다음 예에서는 출력 섹션의 저장소 계정에서 키를 반환하는 방법을 보여 줍니다.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## 지정

**parameters \(parameterName\)**

매개 변수 값을 반환합니다. 템플릿의 매개 변수 섹션에서 지정된 매개 변수 이름을 정의해야 합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| parameterName | 예 | 반환할 매개 변수의 이름입니다.

다음 예에서는 매개 변수 함수의 간소화된 사용을 보여 줍니다.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## provider

**provider \(providerNamespace, [resourceType]\)**

리소스 공급자와 지원되는 리소스 유형에 대한 정보를 반환합니다. 유형이 제공되지 않은 경우 모든 지원되는 유형이 반환됩니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| providerNamespace | 예 | 공급자의 네임스페이스입니다.
| resourceType | 아니요 | 지정된 네임스페이스 내의 리소스 유형입니다.

각 지원되는 유형이 다음 형식으로 반환됩니다.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

다음 예에서는 공급자 함수를 사용하는 방법을 보여 줍니다.

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## reference

**reference \(resourceName or resourceIdentifier, [apiVersion]\)**

식을 사용하여 다른 리소스의 런타임 상태에서 해당 값을 파생시킬 수 있습니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| resourceName 또는 resourceIdentifier | 예 | 리소스의 이름 또는 고유 식별자입니다.
| apiVersion | 아니요 | 리소스 런타임 상태의 API 버전입니다. 리소스가 동일한 템플릿 내에서 프로비전되지 않은 경우 매개 변수를 사용해야 합니다.

**reference** 함수는 런타임 상태에서 값을 파생하므로 변수 섹션에서 사용할 수 없습니다. 템플릿의 출력 섹션에서 사용할 수 있습니다.

참조 식을 사용하여 참조되는 리소스가 동일한 템플릿 내에서 프로비전되는 경우 한 리소스가 다른 리소스에 종속되도록 선언합니다.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup\(\)**

현재 리소스 그룹을 나타내는 구조화된 개체를 반환합니다. 개체는 다음과 같은 형식입니다.

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

다음 예에서는 리소스 그룹 위치를 사용하여 웹 사이트에 대한 위치를 할당합니다.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## resourceId

**resourceId \([resourceGroupName], resourceType, resourceName1, [resourceName2]...\)**

리소스의 고유 식별자를 반환합니다. 리소스 이름이 모호하거나 동일한 템플릿 내에서 프로비전되지 않은 경우 이 함수를 사용합니다. 식별자는 다음 형식으로 반환됩니다.

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| 매개 변수 | 필수 | 설명
| :---------------: | :------: | :----------
| resourceGroupName | 아니요 | 선택적 리소스 그룹 이름입니다. 기본값은 현재 리소스 그룹입니다. 다른 리소스 그룹에서 리소스를 검색할 경우 이 값을 지정합니다.
| resourceType | 예 | 리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다.
| resourceName1 | 예 | 리소스의 이름입니다.
| resourceName2 | 아니요 | 리소스가 중첩된 경우 다음 리소스 이름 세그먼트입니다.

다음 예에서는 웹 사이트 및 데이터베이스에 대한 리소스 ID를 검색하는 방법을 보여 줍니다. 웹 사이트는 **myWebsitesGroup**이라는 리소스 그룹에 있고 데이터베이스는 이 템플릿에 대한 현재 리소스 그룹에 있습니다.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
대체 리소스 그룹의 저장소 계정 또는 가상 네트워크를 사용할 경우 이 함수를 사용해야 합니다. 저장소 계정 또는 가상 네트워크를 여러 리소스 그룹에서 사용할 수 있으므로, 단일 리소스 그룹을 삭제할 때 해당 저장소 계정 또는 가상 네트워크를 삭제하지 않습니다. 다음 예에서는 외부 리소스 그룹의 리소스를 쉽게 사용할 수 있는 방법을 보여 줍니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }


## \(구독당\)

**subscription\(\)**

구독에 대한 세부 정보를 다음 형식으로 반환합니다.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

다음 예에서는 출력 섹션에서 호출되는 구독 함수를 보여 줍니다.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## variables

**variables \(variableName\)**

변수의 값을 반환합니다. 템플릿의 변수 섹션에서 지정된 변수 이름을 정의해야 합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| 변수 이름 | 예 | 반환할 변수의 이름입니다.


## 다음 단계
- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)
- [고급 템플릿 작업](./resource-group-advanced-template.md)
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](./resouce-group-template-deploy.md)
- [Azure 리소스 관리자 개요](./resource-group-overview.md)

<!--HONumber=52-->
