---
title: "Azure Resource Manager 템플릿 작성 | Microsoft 문서"
description: "응용 프로그램을 Azure에 배포하는 선언적 JSON 구문을 사용하여 Azure 리소스 관리자 템플릿을 만듭니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: a3a1fc856dc4fb39e3d3b765e943662799c75398
ms.openlocfilehash: 62b51e2c6235011019d0ad837fe58388cf85e8d0


---
# <a name="authoring-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 작성
이 항목에서는 Azure Resource Manager 템플릿의 구조에 대해 설명합니다. 여기서는 템플릿의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다. 

이미 배포된 리소스의 템플릿을 보려면 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요. 템플릿 작성에 대한 지침은 [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)을 참조하세요. 템플릿 작성에 대한 권장 사항은 [Azure Resource Manager 템플릿 생성 모범 사례](resource-manager-template-best-practices.md)를 참조하세요.

JSON 편집기가 유용하면 템플릿 만드는 태스크를 간소화할 수 있습니다. 템플릿으로 Visual Studio를 사용하는 방법에 대한 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 생성 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요. VS 코드를 사용하는 방법에 대한 자세한 내용은 [Visual Studio 코드로 Azure Resource Manager 템플릿으로 작업](resource-manager-vs-code.md)을 참조하세요.

템플릿의 크기는 1MB로, 각 매개 변수 파일의 크기는 64KB로 제한됩니다. 1MB의 제한은 반복적인 리소스 정의로 확장된 후 템플릿의 마지막 상태와 변수 및 매개변수 값에 적용됩니다. 

## <a name="template-format"></a>템플릿 형식
가장 간단한 구조의 템플릿에 포함되는 요소는 다음과 같습니다.

```json
{
   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "",
   "parameters": {  },
   "variables": {  },
   "resources": [  ],
   "outputs": {  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| $schema |예 |템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다. 위 예제에서 보여 주는 URL을 사용합니다. |
| contentVersion |예 |템플릿의 버전입니다(예: 1.0.0.0). 이 요소에 값을 제공할 수 있습니다. 템플릿을 사용하여 리소스를 배포할 때 이 값을 사용하면 정확한 템플릿이 사용되도록 할 수 있습니다. |
| 매개 변수 |아니요 |배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값입니다. |
| variables |아니요 |템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값입니다. |
| 리소스 |예 |리소스 그룹에 배포 또는 업데이트되는 리소스 종류입니다. |
| outputs |아니요 |배포 후 반환되는 값입니다. |

템플릿의 섹션에 대해서는 이 항목의 뒷부분에서 더 자세히 설명합니다.

## <a name="expressions-and-functions"></a>식 및 함수
템플릿의 기본 구문은 JSON이지만, 식 및 함수를 사용하면 템플릿에서 사용할 수 있는 JSON을 확장할 수 있습니다. 식에는 엄격한 리터럴 값이 아닌 값을 만들 수 있습니다. 식은 대괄호([ 및 ]) 안에 들어 있고 템플릿을 배포할 때 평가됩니다. 식은 JSON 문자열 값에서 어느 위치에나 나타날 수 있으며 항상 다른 JSON 값을 반환합니다. 대괄호([)로 시작하는 리터럴 문자열을 사용해야 하는 경우 대괄호를 두 개([[) 사용해야 합니다.

일반적으로 배포를 구성하기 위한 작업을 수행하는 함수를 식과 함께 사용합니다. JavaScript에서와 마찬가지로 함수 호출은 **functionName(arg1,arg2,arg3)**으로 형식이 지정됩니다. 점과 [인덱스] 연산자를 사용하여 속성을 참조할 수 있습니다.

다음 예제에서는 값을 생성할 때 여러 함수를 사용하는 방법을 보여 줍니다.

```json
"variables": {
   "location": "[resourceGroup().location]",
   "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
   "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](resource-group-template-functions.md)를 참조하세요. 

## <a name="parameters"></a>매개 변수
템플릿의 매개 변수 섹션에서는 리소스를 배포할 때 입력할 수 있는 값을 지정합니다. 이러한 매개 변수 값을 사용하여 개발, 테스트 및 프로덕션 등의 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 템플릿에서 매개 변수를 제공할 필요는 없지만 매개 변수가 없으면 템플릿이 항상 이름, 위치 및 속성이 같은 동일한 리소스를 배포합니다.

배포된 리소스에 대한 값을 설정하는 템플릿 전체에서 이들 매개 변수 값을 사용할 수 있습니다. parameters 섹션에서 선언한 매개 변수만 템플릿의 다른 섹션에서 사용할 수 있습니다.

다음과 같은 구조를 사용하여 매개 변수를 정의합니다.

```json
"parameters": {
   "<parameter-name>" : {
     "type" : "<type-of-parameter-value>",
     "defaultValue": "<default-value-of-parameter>",
     "allowedValues": [ "<array-of-allowed-values>" ],
     "minValue": <minimum-value-for-int>,
     "maxValue": <maximum-value-for-int>,
     "minLength": <minimum-length-for-string-or-array>,
     "maxLength": <maximum-length-for-string-or-array-parameters>,
     "metadata": {
         "description": "<description-of-the parameter>" 
     }
   }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| parameterName |예 |매개 변수의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| type |예 |매개 변수 값의 유형입니다. 아래의 허용되는 유형 목록을 참조하세요. |
| defaultValue |아니요 |매개 변수 값을 제공하지 않는 경우 매개 변수의 기본값입니다. |
| allowedValues |아니요 |올바른 값을 제공하도록 매개 변수에 대해 허용되는 값의 배열입니다. |
| minValue |아니요 |Int 형식 매개 변수의 최소값이며, 이 값이 포함됩니다. |
| maxValue |아니요 |Int 형식 매개 변수의 최대값이며, 이 값이 포함됩니다. |
| minLength |아니요 |string, secureString 및 array 형식 매개 변수의 최소 길이이며, 이 값이 포함됩니다. |
| maxLength |아니요 |string, secureString 및 array 형식 매개 변수의 최대 길이이며, 이 값이 포함됩니다. |
| 설명 |아니요 |포털 사용자 지정 템플릿 인터페이스를 통해 템플릿의 사용자에게 표시되는 매개 변수에 대한 설명입니다. |

허용되는 유형 및 값은 다음과 같습니다.

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

매개 변수를 선택적으로 지정하려면 defaultValue를 제공합니다(빈 문자열 가능). 

템플릿을 배포하는 명령의 매개 변수 중 하나와 일치하는 매개 변수 이름을 지정하는 경우 해당 매개 변수의 값으로 **FromTemplate** 접미사를 제공하라는 메시지가 표시됩니다. 예를 들어 [New-AzureRmResourceGroupDeployment][deployment2cmdlet] cmdlet의 **ResourceGroupName** 매개 변수와 동일한 **ResourceGroupName**으로 명명된 매개 변수가 템플릿에 포함되는 경우 값으로 **ResourceGroupNameFromTemplate**을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

> [!NOTE]
> 모든 암호와 키, 기타 비밀은 **secureString** 유형을 사용해야 합니다. JSON 개체에 중요한 데이터를 전달하는 경우 **secureObject** 유형을 사용합니다. 리소스 배포 후에는 secureString 또는 secureObject 형식의 템플릿 매개 변수를 읽을 수 없습니다. 
> 
> 예를 들어 배포 기록의 다음 항목은 secureString 및 secureObject가 아닌 문자열과 개체에 대한 값을 보여줍니다.
>
> ![배포 값 표시](./media/resource-group-authoring-templates/show-parameters.png)  
>

다음 예제에서는 매개 변수를 정의하는 방법을 보여줍니다.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  },
  "skuName": {
    "type": "string",
    "defaultValue": "F1",
    "allowedValues": [
      "F1",
      "D1",
      "B1",
      "B2",
      "B3",
      "S1",
      "S2",
      "S3",
      "P1",
      "P2",
      "P3",
      "P4"
    ]
  },
  "skuCapacity": {
    "type": "int",
    "defaultValue": 1,
    "minValue": 1
  }
}
```

배포 중에 매개 변수 값을 입력하는 방법은 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요. 

## <a name="variables"></a>variables
변수 섹션에서 템플릿을 통해 사용할 수 있는 값을 생성합니다. 일반적으로 변수는 매개 변수에서 제공하는 값에 따라 달라집니다. 변수를 정의할 필요는 없지만 종종 변수를 통해 복잡한 식을 줄이면 템플릿이 단순화됩니다.

다음과 같은 구조를 사용하여 변수를 정의합니다.

```json
"variables": {
   "<variable-name>": "<variable-value>",
   "<variable-name>": { 
       <variable-complex-type-value> 
   }
}
```

다음 예제에서는 두 매개 변수 값에서 생성된 변수를 정의하는 방법을 보여줍니다.

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

다음 예제에서는 복합 JSON 유형인 변수와 다른 변수에서 생성된 변수를 보여줍니다.

```json
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
```

## <a name="resources"></a>리소스
리소스 섹션에서 배포되거나 업데이트되는 리소스를 정의합니다. 여기서는 올바른 값을 제공하기 위해 배포하는 유형을 이해해야 하기 때문에 템플릿이 더 복잡해질 수 있습니다. 

다음과 같은 구조를 사용하여 리소스를 정의합니다.

```json
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
     "copy": {
       "name": "<name-of-copy-loop>",
       "count": "<number-of-iterations>"
     }
     "resources": [
       "<array-of-child-resources>"
     ]
   }
]
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| apiVersion |예 |리소스를 만들 때 사용하는 REST API의 버전입니다. |
| type |예 |리소스 유형입니다. 이 값은 리소스 공급자의 네임스페이스와 리소스 형식을 조합한 값입니다(예: **Microsoft.Storage/storageAccounts**). |
| name |예 |리소스의 이름입니다. 이 이름은 RFC3986에 정의된 URI 구성 요소 제한을 따라야 합니다. 또한 리소스 이름을 외부에 노출하는 Azure 서비스는 다른 ID를 스푸핑하려는 시도가 아님을 확인하기 위해 이름의 유효성을 확인합니다. [리소스 이름 검사](https://msdn.microsoft.com/library/azure/mt219035.aspx)를 참조하세요. |
| location |다름 |제공된 리소스의 지역적 위치를 지원합니다. 사용 가능한 위치중 하나를 선택할 수 있지만 대개는 사용자에게 가까운 하나를 선택하는 것이 좋습니다. 일반적으로 동일한 지역에서 서로 상호 작용하도록 리소스를 배치하는 것도 좋습니다. 대부분의 리소스 종류에는 위치가 필요하지만 일부 종류(예: 역할 할당)에는 위치가 필요하지 않습니다. |
| tags |아니요 |리소스와 연결된 태그입니다. |
| 설명 |아니요 |템플릿에서 리소스를 문서화하는 내용에 대한 참고 |
| dependsOn |아니요 |이 리소스를 배포하기 전에 배포해야 하는 리소스입니다. Resource Manager는 리소스 간의 종속성을 평가한 후 올바른 순서에 따라 리소스를 배포합니다. 리소스는 서로 종속되지 않을 경우 병렬로 배포됩니다. 이 값은 리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다. 이 템플릿에 배포된 리소스만 나열합니다. 이 템플릿에 정의되지 않은 리소스는 이미 존재해야 합니다. 자세한 정보는 [Azure 리소스 관리자 템플릿에서 종속성 정의](resource-group-define-dependencies.md)를 참조하세요. |
| properties |아니요 |리소스별 구성 설정입니다. 속성의 값은 리소스를 만들기 위해 REST API 작업(PUT 메서드)에 대한 요청 본문에 제공하는 값과 동일합니다. 리소스 스키마 설명서 또는 REST API에 대한 링크를 참조하려면 [리소스 관리자 공급자, 지역, API 버전 및 스키마](resource-manager-supported-services.md)를 참조하세요. |
| 복사 |아니요 |인스턴스가 둘 이상 필요한 경우 만드는 리소스의 수입니다. 자세한 내용은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요. |
| 리소스 |아니요 |정의 중인 리소스에 종속되는 하위 리소스입니다. 부모 리소스의 스키마에서 허용되는 리소스 종류만 제공할 수 있습니다. 자식 리소스 형식의 정규화된 이름에는 부모 리소스 형식이 포함됩니다(예: **Microsoft.Web/sites/extensions**). 부모 리소스에 대한 종속성은 암시되지 않습니다. 해당 종속성을 명시적으로 정의해야 합니다. |

**apiVersion**, **type** 및 **location**에 지정하는 값은 명확히 바로 알 수 있는 값이 아닙니다. 다행스럽게도 Azure PowerShell 또는 Azure CLI를 통해 이러한 값을 확인할 수 있습니다.

**PowerShell**에서 모든 리소스 공급자를 가져오려면 다음과 같이 사용합니다.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

반환된 목록에서 관심 있는 리소스 공급자를 찾습니다. 리소스 공급자의 리소스 형식(예: Storage)을 가져오려면 다음과 같이 사용합니다.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes
```

리소스 형식의 API 버전(예: 저장소 계정)을 가져오려면 다음과 같이 사용합니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions
```

리소스 형식의 지원되는 위치를 가져오려면 다음과 같이 사용합니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations
```

**Azure CLI**에서 모든 리소스 공급자를 가져오려면 다음과 같이 사용합니다.

    azure provider list

반환된 목록에서 관심 있는 리소스 공급자를 찾습니다. 리소스 공급자의 리소스 형식(예: Storage)을 가져오려면 다음과 같이 사용합니다.

    azure provider show Microsoft.Storage

지원되는 위치와 API 버전을 가져오려면 다음과 같이 사용합니다.

    azure provider show Microsoft.Storage --details --json

리소스 공급자에 대해 알아보려면 [Resource Manager 공급자, 지역, API 버전 및 스키마](resource-manager-supported-services.md)를 참조하세요.

리소스 섹션에는 배포할 리소스의 배열이 포함되어 있습니다. 각 리소스 내에서 자식 리소스의 배열도 정의할 수 있습니다. 따라서 리소스 섹션에는 다음과 같은 구조가 있을 수 있습니다.

```json
"resources": [
   {
       "name": "resourceA",
   },
   {
       "name": "resourceB",
       "resources": [
           {
               "name": "firstChildResourceB",
           },
           {   
               "name": "secondChildResourceB",
           }
       ]
   },
   {
       "name": "resourceC",
   }
]
```      

다음 예제에서는 **Extensions** 자식 리소스를 포함한 **Microsoft.Web/serverfarms** 리소스 및 **Microsoft.Web/sites** 리소스를 보여 줍니다. 서버 팜이 존재해야 사이트를 배포할 수 있기 때부터 해당 사이트는 서버 팜에 대한 종속으로 표시됩니다. **Extensions** 리소스는 사이트의 자식입니다.

```json
"resources": [
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('hostingPlanName')]",
    "type": "Microsoft.Web/serverfarms",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "HostingPlan"
    },
    "sku": {
      "name": "[parameters('skuName')]",
      "capacity": "[parameters('skuCapacity')]"
    },
    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }
  },
  {
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[parameters('siteName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "environment": "test",
      "team": "Web"
    },
    "dependsOn": [
      "[concat(parameters('hostingPlanName'))]"
    ],
    "properties": {
      "name": "[parameters('siteName')]",
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    },
    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "extensions",
        "name": "MSDeploy",
        "dependsOn": [
          "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
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
```

## <a name="outputs"></a>outputs
Outputs 섹션에서, 배포에서 반환되는 값을 지정합니다. 예를 들어, 배포된 리소스에 액세스하기 위한 URI를 반환할 수 있습니다.

다음 예제에서는 출력 정의의 구조를 보여줍니다.

```json
"outputs": {
   "<outputName>" : {
     "type" : "<type-of-output-value>",
     "value": "<output-value-expression>"
   }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| outputName |예 |출력 값의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| type |예 |출력 값의 유형입니다. 출력 값은 템플릿 입력 매개 변수와 동일한 유형을 지원합니다. |
| value |예 |출력 값으로 계산되어 반환되는 템플릿 언어 식입니다. |

다음 예제에서는 Outputs 섹션에서 반환되는 값을 보여줍니다.

```json
"outputs": {
   "siteUri" : {
     "type" : "string",
     "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
   }
}
```

출력 작업에 대한 자세한 내용은 [Azure Resource Manager 템플릿에서 상태 공유](best-practices-resource-manager-state.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 다른 리소스 그룹 내에 있는 리소스를 사용해야 할 수도 있습니다. 이 시나리오에서는 일반적으로 여러 리소스 그룹에서 공유하는 저장소 계정 또는 가상 네트워크에서 작업합니다. 자세한 내용은 [resourceId 함수](resource-group-template-functions.md#resourceid)를 참조하세요.

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment



<!--HONumber=Dec16_HO1-->


