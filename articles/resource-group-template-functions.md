<properties
   pageTitle="리소스 관리자 템플릿 함수 | Microsoft Azure"
   description="Azure 리소스 관리자 템플릿에서 값을 검색하고 문자열과 숫자로 작업하며 배포 정보를 검색하는 데 사용하는 함수를 설명합니다."
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
   ms.date="11/12/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 템플릿 함수

이 항목에서는 Azure 리소스 관리자 템플릿에서 사용할 수 있는 모든 함수에 대해 설명합니다.

템플릿 함수 및 해당 매개 변수는 대/소문자를 구분하지 않습니다. 예를 들어 리소스 관리자에서 **variables('var1')**와 **VARIABLES('VAR1')**는 동일합니다. 계산될 때 함수는 대/소문자를 명시적으로 수정하지 않는 한(toUpper 또는 toLower 등) 대/소문자를 보존합니다. 특정 리소스 유형에는 식이 계산되는 방식에 관계없이 대/소문자 요구 사항이 있을 수 있습니다.

## 추가

**add(operand1, operand2)**

제공된 두 정수의 합을 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| operand1 | 예 | 사용할 첫 번째 피연산자입니다.
| operand2 | 예 | 사용할 두 번째 피연산자입니다.


## base64

**base64 (inputString)**

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

**concat (arg1, arg2, arg3, ...)**

여러 문자열 값을 결합하고 결과 문자열 값을 반환합니다. 이 함수는 임의의 수의 인수를 사용할 수 있습니다.

다음 예에서는 여러 값을 결합하여 값을 반환하는 방법을 보여 줍니다.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## copyIndex

**copyIndex(offset)**

반복 루프의 현재 인덱스를 반환합니다. 이 함수를 사용하는 방법의 예는 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

## deployment

**deployment()**

현재 배포 작업에 대한 정보를 반환합니다.

배포에 대한 정보는 다음 속성과 함께 개체로 반환됩니다.

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

다음 예에서는 출력 섹션의 배포 정보를 반환하는 방법을 보여 줍니다.

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## div

**div(operand1, operand2)**

제공된 두 정수의 나누기를 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| operand1 | 예 | 나누어지는 수입니다.
| operand2 | 예 | 나누는 데 사용되는 번호는 0과 다를 수 있습니다.

## int

**int(valueToConvert)**

지정된 값을 정수로 변환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| valueToConvert | 예 | 정수로 변환할 값입니다. 값 형식은 문자열 또는 정수일 수만 있습니다.

다음 예제는 사용자가 제공한 매개 변수 값을 정수로 변환합니다.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }

## length

**length(array)**

배열 내의 요소 수를 반환합니다. 일반적으로 리소스를 만들 때 반복 횟수를 지정하는 데 사용합니다. 이 함수를 사용하는 방법의 예는 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

## listKeys

**listKeys (resourceName or resourceIdentifier, apiVersion)**

저장소 계정의 키를 반환합니다. [resourceId 함수](./#resourceid) 또는 **providerNamespace/resourceType/resourceName** 형식을 사용하여 resourceId를 지정할 수 있습니다. 이 함수를 사용하여 primaryKey 및 secondaryKey를 가져올 수 있습니다.
  
| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| resourceName 또는 resourceIdentifier | 예 | 저장소 계정의 고유 식별자입니다.
| apiVersion | 예 | 리소스 런타임 상태의 API 버전입니다.

다음 예에서는 출력 섹션의 저장소 계정에서 키를 반환하는 방법을 보여 줍니다.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

## mod

**mod(operand1, operand2)**

제공된 두 정수를 사용하여 나누기한 나머지를 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| operand1 | 예 | 나누어지는 수입니다.
| operand2 | 예 | 나누는 데 사용되는 번호는 0과 다를 수 있습니다.


## mul

**mul(operand1, operand2)**

제공된 두 정수의 곱하기를 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| operand1 | 예 | 사용할 첫 번째 피연산자입니다.
| operand2 | 예 | 사용할 두 번째 피연산자입니다.


## padLeft

**padLeft (stringToPad, totalLength, paddingCharacter)**

지정된 총 길이에 도달할 때까지 왼쪽에 문자를 추가하여 오른쪽 맞추어진 문자열을 반환합니다.
  
| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| stringToPad | 예 | 오른쪽에 맞추어진 문자열입니다.
| totalLength | 예 | 반환된 문자열에서 문자의 총수입니다.
| paddingCharacter | 예 | 총 길이에 도달할 때까지 왼쪽 여백에 사용되는 문자입니다.

다음 예제는 문자열이 10자에 도달할 때까지 0 문자를 추가하여 사용자가 제공한 매개 변수 값을 채우는 방법을 보여줍니다. 원래 매개 변수 값이 10자 보다 긴 경우 문자가 더 추가되지 않습니다.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }


## 매개 변수

**parameters (parameterName)**

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

## providers

**providers (providerNamespace, [resourceType])**

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

**reference (resourceName or resourceIdentifier, [apiVersion])**

식을 사용하여 다른 리소스의 런타임 상태에서 해당 값을 파생시킬 수 있습니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| resourceName 또는 resourceIdentifier | 예 | 리소스의 이름 또는 고유 식별자입니다.
| apiVersion | 아니요 | 리소스 런타임 상태의 API 버전입니다. 리소스가 동일한 템플릿 내에서 프로비전되지 않은 경우 매개 변수를 사용해야 합니다.

**reference** 함수는 런타임 상태에서 값을 파생하므로 변수 섹션에서 사용할 수 없습니다. 템플릿의 출력 섹션에서 사용할 수 있습니다.

참조 식을 사용하여 참조되는 리소스가 동일한 템플릿 내에서 프로비전되는 경우 한 리소스가 다른 리소스에 종속되도록 암시적으로 선언합니다. 또한 **dependsOn** 속성도 사용할 필요가 없습니다. 참조 리소스가 배포를 완료할 때까지 식은 평가되지 않습니다.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

**replace(originalString, oldCharacter, newCharacter)**

새 문자열을 다른 문자로 대체한 지정된 문자열에서 한 문자의 인스턴스로 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| originalString | 예 | 다른 문자로 대체하는 한 문자의 모든 인스턴스가 있는 문자열입니다.
| oldCharacter | 예 | 원래 문자열에서 제거할 문자입니다.
| newCharacter | 예 | 제거된 문자 대신 추가하는 문자입니다.

다음 예제는 사용자가 제공한 문자열에서 모든 대시를 제거하는 방법을 보여줍니다.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

## resourceGroup

**resourceGroup()**

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

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

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
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## 분할

**split(inputString, delimiter)** **split(inputString, [delimiters])**

보낸 구분 기호로 구분되는 입력 문자열의 부분 문자열을 포함하는 문자열의 배열을 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| inputString | 예 | 분할에 사용될 문자열입니다.
| 구분 기호 | 예 | 사용할 구분 기호는 단일 문자열 또는 문자열의 배열일 수 있습니다.

다음 예제에서는 쉼표를 사용하여 입력 문자열을 분할합니다.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

## string

**string(valueToConvert)**

지정된 값을 문자열로 변환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| valueToConvert | 예 | 문자열로 변환할 값입니다. 값 형식은 부울, 정수 또는 문자열일 수만 있습니다.

다음 예제는 사용자가 제공한 매개 변수 값을 문자열로 변환합니다.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

## sub

**sub(operand1, operand2)**

제공된 두 정수의 빼기를 반환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| operand1 | 예 | 빼기할 수입니다.
| operand2 | 예 | 뺄 수입니다.


## (구독당)

**subscription()**

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

## toLower

**toLower(stringToChange)**

지정된 문자열을 소문자로 변환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| stringToChange | 예 | 소문자로 변환할 문자열입니다.

다음 예제는 사용자가 제공한 매개 변수 값을 소문자로 변환합니다.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

## toUpper

**toUpper(stringToChange)**

지정된 문자열을 대문자로 변환합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| stringToChange | 예 | 대문자로 변환할 문자열입니다.

다음 예제는 사용자가 제공한 매개 변수 값을 대문자로 변환합니다.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

## trim

**trim(stringToTrim)**

지정된 문자열에서 모든 선행 및 후행 공백 문자를 제거합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| stringToTrim | 예 | 잘라낼 문자열입니다.

다음은 사용자가 입력한 매개 변수 값에서 공백 문자를 자르는 예입니다.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }


## uniqueString

**uniqueString(stringForCreatingUniqueString, ...)**

고유한 문자열을 만들려면 제공된 64비트 해시의 문자열을 수행합니다. 이 함수는 리소스의 고유한 이름을 만들어야 할 때 유용합니다. 결과의 고유성 수준을 나타내는 매개 변수 값을 제공합니다. 구독, 리소스 그룹 또는 배포에 대해 해당 이름이 고유한지 여부를 지정할 수 있습니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | 예 | 고유한 문자열을 만들기 위해 해시 함수에서 사용되는 기본 문자열입니다.
| 필요에 따라 추가하는 매개 변수 | 아니요 | 고유성의 수준을 지정하는 값을 만들기 위해 필요한 만큼 문자열을 추가할 수 있습니다.

반환된 값은 완전한 임의 문자열이 아닌 해시 함수의 결과입니다. 반환된 값은 13자입니다. 전역적으로 고유하도록 보장되지는 않습니다. 보다 친숙한 이름을 만들기 위해 해당 값과 명명 규칙의 접두사를 결합할 수도 있습니다.

다음 예제에서는 uniqueString를 사용하여 일반적으로 사용하는 다른 수준에 대해 고유한 값을 만드는 방법을 보여 줍니다.

구독에 따라 고유하게

    "[uniqueString(subscription().subscriptionId)]"

리소스 그룹에 따라 고유하게

    "[uniqueString(resourceGroup().id)]"

리소스 그룹의 배포에 따라 고유하게

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
다음 예제에서는 리소스 그룹에 따라 저장소 계정에 고유한 이름을 만드는 방법을 보여 줍니다.

    "resources": [{ 
        "name": "[concat('ContosoStorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

## uri

**uri(baseUri, relativeUri)**

baseUri와 relativeUri 문자열을 결합하여 절대 URI를 만듭니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| baseUri | 예 | 기본 uri 문자열입니다.
| relativeUri | 예 | 기본 uri 문자열에 추가할 상대 uri 문자열입니다.

다음은 템플릿 링크에서 절대 URI를 만드는 방법을 보여주는 예입니다. 결과는 ****http://contoso.com/resources/nested/azuredeploy.json**입니다.

    "templateLink": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]"


## variables

**variables (variableName)**

변수의 값을 반환합니다. 템플릿의 변수 섹션에서 지정된 변수 이름을 정의해야 합니다.

| 매개 변수 | 필수 | 설명
| :--------------------------------: | :------: | :----------
| 변수 이름 | 예 | 반환할 변수의 이름입니다.


## 다음 단계
- Azure 리소스 관리자 템플릿의 섹션에 대한 설명은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 여러 템플릿을 병합하려면 [Azure 리소스 관리자에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
- 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.
- 만든 템플릿을 배포하는 방법을 보려면 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=Nov15_HO4-->