<properties
   pageTitle="Azure 리소스 관리자 템플릿 작성"
   description="응용 프로그램을 Azure에 배포하는 선언적 JSON 구문을 사용하여 Azure 리소스 관리자 템플릿을 만듭니다."
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
   ms.date="02/17/2016"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 템플릿 작성

Azure 응용 프로그램은 일반적으로 원하는 목표를 충족시키기 위해 리소스(예: 데이터베이스 서버, 데이터베이스 또는 웹 사이트)의 조합이 필요합니다. 각 리소스를 개별적으로 배포하고 관리하는 대신, 응용 프로그램의 모든 리소스를 하나의 조정된 작업으로 배포하고 프로비전하는 Azure 리소스 관리자 템플릿을 만들 수 있습니다. 템플릿에서 응용 프로그램에 필요한 리소스를 정의하고 다양한 환경에 따라 값을 입력하는 배포 매개 변수를 지정합니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다. 이 항목에서는 템플릿의 섹션에 대해 설명합니다.

Visual Studio는 템플릿 생성 작업을 도와주는 도구를 제공합니다. 템플릿으로 Visual Studio를 사용하는 방법에 대한 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 생성 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

템플릿의 크기는 1MB로, 각 매개 변수 파일의 크기는 64KG로 제한해야 합니다. 1MB의 제한은 반복적인 리소스 정의로 확장된 후 템플릿의 마지막 상태와 변수 및 매개변수 값에 적용됩니다.

## 템플릿 계획

템플릿을 시작하기 전에 먼저 배포하려는 템플릿 및 템플릿 사용 방법을 이해해야 합니다. 특히 다음 사항을 고려해야 합니다.

1. 배포해야 하는 리소스 형식
2. 해당 리소스의 위치
3. 리소스를 배포할 때 사용할 리소스 공급자 API 버전
4. 해당 리소스 중에 다른 리소스 이후에 배포해야 하는 리소스가 있는지 여부
5. 배포하는 동안 전달하려는 값 및 템플릿에서 직접 정의하려는 값
6. 배포로부터 값을 반환해야 하는지 여부

배포에 사용할 수 있는 리소스 형식, 해당 형식에 지원되는 지역, 각 형식에 대해 사용 가능한 API 버전을 알아보려면 [리소스 관리자 공급자, 지역, API 버전 및 스키마](resource-manager-supported-services.md)를 참조하세요. 이 항목에서는 템플릿 내에서 제공해야 하는 값을 결정하는 데 도움이 되는 예제 및 링크를 제공합니다.

다른 리소스를 배포한 이후에 배포해야 하는 리소스는 다른 리소스에 종속된 것으로 표시할 수 있습니다. 이 작업을 수행하는 방법은 아래 [리소스](#resources) 섹션에 나와 있습니다.

실행 중에 매개 변수 값을 제공하면 템플릿 배포의 결과를 변경할 수 있습니다. 이 작업을 수행하는 방법은 아래 [매개 변수](#parameters) 섹션에 나와 있습니다.

[출력](#outputs) 섹션에서 배포 값을 반환할 수 있습니다.

## 템플릿 형식

가장 간단한 구조의 템플릿에는 다음 요소가 포함됩니다.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| 요소 이름 | 필수 | 설명
| :------------: | :------: | :----------
| $schema | 예 | 템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다. 위에 나와 있는 URL을 사용해야 합니다.
| contentVersion | 예 | 템플릿의 버전입니다(예: 1.0.0.0). 이 요소에 값을 제공할 수 있습니다. 템플릿을 사용하여 리소스를 배포할 때 이 값을 사용하면 정확한 템플릿이 사용되도록 할 수 있습니다.
| 매개 변수 | 아니요 | 배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값입니다.
| variables | 아니요 | 템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값입니다.
| 리소스 | 예 | 리소스 그룹에 배포 또는 업데이트되는 리소스 종류입니다.
| outputs | 아니요 | 배포 후 반환되는 값입니다.

템플릿의 섹션에 대해서는 이 항목의 뒷부분에서 좀더 자세히 설명합니다. 지금은 템플릿을 구성하는 구문 중 몇 개를 살펴보겠습니다.

## 식 및 함수

템플릿의 기본 구문은 JSON이지만 식 및 함수를 통해 템플릿에서 사용할 수 있는 JSON을 확장하면 엄격한 리터럴 값이 아닌 값을 만들 수 있습니다. 식은 대괄호([ 및 ]) 안에 들어 있고 템플릿을 배포할 때 평가됩니다. 식은 JSON 문자열 값에서 어느 위치에나 나타날 수 있으며 항상 다른 JSON 값을 반환합니다. 대괄호([)로 시작하는 리터럴 문자열을 사용해야 하는 경우 대괄호를 두 개([[) 사용해야 합니다.

일반적으로 배포를 구성하기 위한 작업을 수행하는 함수를 식과 함께 사용합니다. JavaScript에서와 마찬가지로 함수 호출은 **functionName(arg1,arg2,arg3)**으로 형식이 지정됩니다. 점과 [인덱스] 연산자를 사용하여 속성을 참조할 수 있습니다.

다음 예제에서는 값을 생성할 때 여러 함수를 사용하는 방법을 보여 줍니다.
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)를 참조하세요.


## 매개 변수

템플릿의 매개 변수 섹션에서는 리소스를 배포할 때 입력할 수 있는 값을 지정합니다. 이러한 매개 변수 값을 사용하여 개발, 테스트 및 프로덕션 등의 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 템플릿에서 매개 변수를 제공할 필요는 없지만 매개 변수가 없으면 템플릿이 항상 이름, 위치 및 속성이 같은 동일한 리소스를 배포합니다.

배포된 리소스에 대한 값을 설정하는 템플릿 전체에서 이들 매개 변수 값을 사용할 수 있습니다. parameters 섹션에서 선언한 매개 변수만 템플릿의 다른 섹션에서 사용할 수 있습니다.

parameters 섹션 내에서는 다른 매개 변수 값을 생성하는 매개 변수 값을 사용할 수 없습니다. 변수 섹션에서 새 값을 생성합니다.

다음과 같은 구조를 사용하여 매개 변수를 정의합니다.

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| 요소 이름 | 필수 | 설명
| :------------: | :------: | :----------
| parameterName | 예 | 매개 변수의 이름입니다. 유효한 JavaScript 식별자여야 합니다.
| type | 예 | 매개 변수 값의 유형입니다. 아래의 허용되는 유형 목록을 참조하세요.
| defaultValue | 아니요 | 매개 변수 값을 제공하지 않는 경우 매개 변수의 기본값입니다.
| allowedValues | 아니요 | 올바른 값을 제공하도록 매개 변수에 대해 허용되는 값의 배열입니다.
| minValue | 아니요 | Int 형식 매개 변수의 최소값이며, 이 값이 포함됩니다.
| maxValue | 아니요 | Int 형식 매개 변수의 최대값이며, 이 값이 포함됩니다.
| minLength | 아니요 | 문자열, secureString 및 배열 형식 매개 변수의 최소 길이이며, 이 값이 포함됩니다.
| maxLength | 아니요 | 문자열, secureString 및 배열 형식 매개 변수의 최대 길이이며, 이 값이 포함됩니다.
| description | 아니요 | 포털 사용자 지정 템플릿 인터페이스를 통해 템플릿의 사용자에게 표시되는 매개 변수에 대한 설명입니다.

허용되는 유형 및 값은 다음과 같습니다.

- string 또는 secureString - 유효한 모든 JSON 문자열
- int - 유효한 모든 JSON 정수
- bool - 유효한 모든 JSON 부울
- object 또는 secureObject - 유효한 모든 JSON 개체
- array - 유효한 모든 JSON 배열

매개 변수를 선택적으로 지정하려면 defaultValue를 빈 문자열로 설정합니다.

템플릿을 배포하는 명령의 매개 변수 중 하나와 일치하는 매개 변수 이름을 지정하면(예: 템플릿에 [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) cmdlet의 **ResourceGroupName** 매개 변수와 동일한 **ResourceGroupName**이라는 매개 변수 포함) **FromTemplate** 후위가 있는 매개 변수(예: **ResourceGroupNameFromTemplate**)에 대한 값을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

>[AZURE.NOTE] 모든 암호와 키, 기타 비밀은 **secureString** 유형을 사용해야 합니다. 리소스 배포 후에는 secureString 형식의 템플릿 매개 변수를 읽을 수 없습니다.

다음 예제에서는 매개 변수를 정의하는 방법을 보여줍니다.

    "parameters": {
       "siteName": {
          "type": "string",
          "minLength": 2,
          "maxLength": 60
       },
       "siteLocation": {
          "type": "string",
          "minLength": 2
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       },
       "instancesCount": {
          "type": "int",
          "maxValue": 10
       },
       "numberOfWorkers": {
          "type": "int",
          "minValue": 1
       }
    }

배포 중에 매개 변수 값을 입력하는 방법은 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](../resource-group-template-deploy/#parameter-file)를 참조하세요.

## 변수

변수 섹션에서 템플릿을 통해 사용할 수 있는 값을 생성합니다. 일반적으로 이들 변수는 매개 변수에서 제공되는 값에 따라 달라집니다. 변수를 정의할 필요는 없지만 종종 변수를 통해 복잡한 식을 줄이면 템플릿이 단순화됩니다.

다음과 같은 구조를 사용하여 변수를 정의합니다.

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

다음 예제에서는 두 매개 변수 값에서 생성된 변수를 정의하는 방법을 보여줍니다.

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

다음 예제에서는 복합 JSON 유형인 변수와 다른 변수에서 생성된 변수를 보여줍니다.

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## 리소스

resources 섹션에서 배포 또는 업데이트되는 리소스를 정의합니다. 여기에서 템플릿이 더 복잡해질 수 있는데 그 이유는 올바른 값을 제공하려면 배포하는 유형을 이해해야 하기 때문입니다. 리소스 공급자에 대해 알아보려면 [리소스 관리자 공급자, 지역, API 버전 및 스키마](resource-manager-supported-services.md)를 참조하세요.

다음과 같은 구조를 사용하여 리소스를 정의합니다.

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| 요소 이름 | 필수 | 설명
| :----------------------: | :------: | :----------
| apiVersion | 예 | 리소스를 만들 때 사용하는 REST API의 버전입니다. 특정 리소스 형식에 사용 가능한 버전 번호를 결정하려면 [지원되는 API 버전](../resource-manager-supported-services/#supported-api-versions)을 참조하세요.
| type | 예 | 리소스 유형입니다. 이 값은 리소스 공급자의 네임스페이스와 리소스 공급자가 지원하는 리소스 유형을 조합한 값입니다.
| name | 예 | 리소스의 이름입니다. 이 이름은 RFC3986에 정의된 URI 구성 요소 제한을 따라야 합니다.
| location | 아니요 | 제공된 리소스의 지역적 위치를 지원합니다. 사용 가능한 위치를 확인하려면 [지원되는 지역](../resource-manager-supported-services/#supported-regions)을 참조하세요.
| tags | 아니요 | 리소스와 연결된 태그입니다.
| 설명 | 아니요 | 템플릿에서 리소스를 문서화하는 내용에 대한 참고
| dependsOn | 아니요 | 정의 중인 리소스가 종속되는 리소스입니다. 종속된 순서에 따라 리소스 간의 종속성이 평가되고 리소스가 배포됩니다. 리소스가 서로 종속되어 있지 않으면 병렬로 배포됩니다. 이 값은 리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록일 수 있습니다.
| properties | 아니요 | 리소스별 구성 설정입니다. 속성의 값은 리소스를 만들기 위해 REST API 작업(PUT 메서드)에 대한 요청 본문에 제공한 값과 동일합니다. 리소스 스키마 설명서 또는 REST API에 대한 링크를 참조하려면 [리소스 관리자 공급자, 지역, API 버전 및 스키마](resource-manager-supported-services.md)를 참조하세요.
| 리소스 | 아니요 | 정의 중인 리소스에 종속되는 하위 리소스입니다. 부모 리소스의 스키마에서 허용되는 리소스 종류만 제공할 수 있습니다. 자식 리소스 종류의 정규화된 이름에는 부모 리소스 종류가 포함됩니다(예: **Microsoft.Web/sites/extensions**). 부모 리소스에 대한 종속성은 암시되지 않습니다. 해당 종속성을 명시적으로 정의해야 합니다. 


리소스 이름이 고유하지 않은 경우 아래에 설명된 **resourceId** 도우미 함수를 사용하여 리소스에 대한 고유 식별자를 가져올 수 있습니다.

리소스 섹션에는 배포할 리소스의 배열이 포함되어 있습니다. 각 리소스 내에서 해당 리소스의 자식 리소스 배열도 정의할 수 있습니다. 따라서 리소스 섹션에는 다음과 같은 구조가 있을 수 있습니다.

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



다음 예제는 하위 **확장** 리소스가 있는 **Microsoft.Web/serverfarms** 리소스 및 **Microsoft.Web/sites** 리소스를 보여줍니다. 서버 팜이 존재해야 사이트를 배포할 수 있기 때부터 해당 사이트는 서버 팜에 대한 종속으로 표시됩니다. **확장** 리소스는 사이트의 하위 항목입니다.

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
                  "dependsOn": [
                      "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
                  ],
                  "properties": {
                    "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                      "Application Path": "[parameters('siteName')]"
                    }
                  }
              }
          ]
        }
    ]


## 출력

Outputs 섹션에서, 배포에서 반환되는 값을 지정합니다. 예를 들어, 배포된 리소스에 액세스하기 위한 URI를 반환할 수 있습니다.

다음 예제에서는 출력 정의의 구조를 보여줍니다.

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| 요소 이름 | 필수 | 설명
| :------------: | :------: | :----------
| outputName | 예 | 출력 값의 이름입니다. 유효한 JavaScript 식별자여야 합니다.
| type | 예 | 출력 값의 유형입니다. 출력 값은 템플릿 입력 매개 변수와 동일한 유형을 지원합니다.
| value | 예 | 출력 값으로 평가 및 반환될 템플릿 언어 식입니다.


다음 예제에서는 Outputs 섹션에서 반환되는 값을 보여줍니다.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## 고급 시나리오
이 항목에서는 템플릿에 대한 기본 지식을 제공합니다. 하지만 시나리오에 따라서는 고급 작업이 필요할 수 있습니다.

두 템플릿을 병합하거나 부모 템플릿 내에서 자식 템플릿을 사용해야 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

다른 리소스 그룹 내에 있는 리소스를 사용해야 할 수도 있습니다. 여러 리소스 그룹에서 공유하는 저장소 계정 또는 가상 네트워크에서 작업하는 경우 흔한 일입니다. 자세한 내용은 참조는 [resourceId 함수](../resource-group-template-functions#resourceid)를 참조하세요.

## 템플릿 완료
다음 템플릿은 웹 앱을 배포하고 .zip 파일에서 코드를 사용하여 프로비전합니다.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
               ],
               "properties": {
                 "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                 "dbType": "None",
                 "connectionString": "",
                 "setParameters": {
                   "Application Path": "[parameters('siteName')]"
                 }
               }
             }
           ]
         }
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## 다음 단계
- 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
- 만든 템플릿을 배포하는 방법을 보려면 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.
- 응용 프로그램 배포에 대한 자세한 예는 [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하세요.
- 사용할 수 있는 스키마는 [Azure 리소스 관리자 스키마](https://github.com/Azure/azure-resource-manager-schemas)를 참조하세요.

<!---HONumber=AcomDC_0309_2016-->