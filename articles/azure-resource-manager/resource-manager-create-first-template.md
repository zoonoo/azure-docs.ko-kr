---
title: "첫 번째 Azure Resource Manager 템플릿 만들기 | Microsoft Docs"
description: "첫 번째 Azure Resource Manager 템플릿을 만드는 단계별 가이드입니다. 저장소 계정에 대한 템플릿 참조를 사용하여 템플릿을 만드는 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>첫 번째 Azure Resource Manager 템플릿 만들기
이 항목에서는 첫 번째 Azure Resource Manager 템플릿을 만드는 단계를 안내합니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. Azure 솔루션 배포 및 관리와 관련된 개념을 이해하려면 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요. 기존 리소스가 있고 해당 리소스에 대한 템플릿을 가져오려는 경우 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.

템플릿을 만들고 수정하려면 JSON 편집기가 필요합니다. [Visual Studio Code](https://code.visualstudio.com/)는 간단한 오픈 소스 크로스 플랫폼 코드 편집기입니다. 확장을 통해 Resource Manager 템플릿을 만들고 편집하도록 지원합니다. 이 항목에서는 VS 코드를 사용한다고 가정하지만 다른 JSON 편집기(예: Visual Studio)가 있는 경우 해당 편집기를 사용할 수 있습니다.

## <a name="get-vs-code-and-extension"></a>VS 코드 및 확장 가져오기
1. 필요한 경우 [https://code.visualstudio.com/](https://code.visualstudio.com/)에서 VS 코드를 설치합니다.

2. 빠른 열기(Ctrl+P)에 액세스하고 다음을 실행하여 [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) 확장을 설치합니다. 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. 확장을 사용할 수 있다는 메시지가 나타나면 VS Code를 다시 시작합니다.

## <a name="create-blank-template"></a>빈 템플릿 만들기

템플릿의 기본 섹션만 포함하는 빈 템플릿으로 시작하겠습니다.

1. 파일을 만듭니다. 

2. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. 이 파일을 **azuredeploy.json**으로 저장합니다. 

## <a name="add-storage-account"></a>저장소 계정 추가
1. 배포에 대한 저장소 계정을 정의하려면 해당 저장소 계정을 템플릿의 **리소스** 섹션에 추가합니다. 저장소 계정에 사용할 수 있는 값을 찾으려면 [저장소 계정 템플릿 참조](/azure/templates/microsoft.storage/storageaccounts)를 살펴봅니다. 저장소 계정에 대해 표시된 JSON을 복사합니다. 

3. 해당 JSON을 다음 예제에서와 같이 템플릿의 **리소스** 섹션으로 붙여넣습니다. 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-05-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  앞의 예제에는 저장소 계정에 필요하지 않을 수도 있는 많은 자리 표시자 값 및 몇 가지 속성이 포함됩니다.

## <a name="set-values-for-storage-account"></a>저장소 계정에 대한 값 설정

이제 저장소 계정에 대한 값을 설정할 준비가 되었습니다. 

1. JSON을 복사한 [저장소 계정 템플릿 참조](/azure/templates/microsoft.storage/storageaccounts)를 다시 한 번 살펴봅니다. 속성을 설명하고 사용 가능한 값을 제공하는 여러 가지 테이블이 있습니다. 

2. **속성** 요소 내에서 **customDomain**, **암호화** 및 **accessTier**는 모두 필요하지 않은 것으로 나열됩니다. 이러한 값은 시나리오에 중요할 수 있지만 이 예제를 간단하게 하도록 해당 값을 제거해 보겠습니다.

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-05-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. 현재 **kind** 요소는 자리 표시자 값("string")으로 설정됩니다. VS 코드는 템플릿에서 사용할 값을 파악하는 데 도움이 되는 다수의 기능을 포함합니다. VS 코드는 이 값이 유효하지 않음을 나타냅니다. "string"을 가리키면 VS 코드는 **kind**에 대한 유효한 값이 `Storage` 또는 `BlobStorage`라고 제안합니다. 

   ![VS 코드 제안 값 표시](./media/resource-manager-create-first-template/vs-code-show-values.png)

   사용 가능한 값을 보려면 큰따옴표 사이의 문자를 삭제하고 **Ctrl+스페이스바**를 선택합니다. 사용 가능한 옵션에서 **저장소**를 선택합니다.
  
   ![intellisense 표시](./media/resource-manager-create-first-template/intellisense.png)

   VS 코드를 사용하지 않는 경우 저장소 계정 템플릿 참조 페이지를 확인합니다. 설명은 동일한 유효 값을 나열합니다. 요소를 **저장소**로 설정합니다.

   ```json
   "kind": "Storage",
   ```

템플릿은 이제 다음과 같이 표시됩니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>템플릿 함수 추가

템플릿의 구문을 단순화하고 템플릿을 배포할 때만 사용할 수 있는 값을 검색하는 템플릿 내의 함수를 사용합니다. 템플릿 함수의 전체 집합은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.

저장소 계정이 리소스 그룹과 동일한 위치에 배포되도록 지정하려면 **location** 속성을 다음으로 설정합니다.

```json
"location": "[resourceGroup().location]",
```

다시, VS 코드는 사용 가능한 함수를 제안하여 도움을 줍니다. 

![함수 표시](./media/resource-manager-create-first-template/show-functions.png)

함수는 대괄호로 묶여 있습니다. [resourceGroup](resource-group-template-functions.md#resourcegroup) 함수는 `location`이라는 속성으로 개체를 반환합니다. 리소스 그룹은 솔루션에 관련된 모든 리소스를 보유합니다. “미국 중부"와 같은 값으로 location 속성을 하드 코딩할 수 있지만 다른 위치에 다시 배포하려면 템플릿을 수동으로 변경해야 합니다. `resourceGroup` 함수를 사용하면 이 템플릿을 다른 위치에 있는 다른 리소스 그룹에 손쉽게 다시 배포할 수 있습니다.

템플릿은 이제 다음과 같이 표시됩니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>매개 변수 및 변수 추가
템플릿에서 설정할 두 개의 값 **이름** 및 **sku.name**만이 남아 있습니다. 이러한 속성의 경우 배포하는 동안 이러한 값을 사용자 지정할 수 있도록 하는 매개 변수를 추가합니다. 

저장소 계정 이름에는 설정을 어렵게 하는 몇 가지 제한 사항이 있습니다. 이름은 길이가 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용하고 고유해야 합니다. 제한 사항과 일치하는 고유 값을 추측하기 보다는 [uniqueString](resource-group-template-functions.md#uniquestring) 함수를 사용하여 해시 값을 생성합니다. 이 해시 값에 더 많은 의미를 부여하려면 배포 후 저장소 계정으로 식별하는 데 도움이 되는 접두사를 추가합니다. 

1. 명명 규칙과 일치하는 이름에 대한 접두사를 전달하려면 템플릿의 **매개 변수** 섹션으로 이동합니다. 저장소 계정 이름에 대한 접두사를 허용하는 템플릿에 매개 변수를 추가합니다.

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  `uniqueString`은 13자를 반환하고 이름은 24자를 초과할 수 없으므로 접두사는 최대 11자로 제한됩니다. 배포하는 동안 매개 변수에 대한 값을 전달하지 않는 경우 기본 값이 사용됩니다.

2. 템플릿의 **변수** 섹션으로 이동합니다. 접두사와 고유 문자열에서 이름을 생성하려면 다음 변수를 추가합니다.

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. **리소스** 섹션에서 해당 변수에 저장소 계정 이름을 설정합니다.

   ```json
   "name": "[variables('storageName')]",
   ```

3. 저장소 계정에 대해 다른 SKU 전달을 사용하도록 설정하려면 **매개 변수** 섹션으로 이동합니다. 저장소 이름 접두사에 대한 매개 변수 다음에 허용되는 SKU 값 및 기본값을 지정하는 매개 변수를 추가합니다. 템플릿 참조 페이지 또는 VS 코드에서 허용되는 값을 찾을 수 있습니다. 다음 예제에서는 SKU에 대한 모든 유효한 값이 포함됩니다. 그러나 허용되는 값을 이 템플릿을 통해 배포하려는 SKU의 해당 형식으로만 제한할 수 있습니다.

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     },
     "storageSKU": {
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ],
       "defaultValue": "Standard_LRS",
       "metadata": {
         "description": "The type of replication to use for the storage account."
       }
     }
   },
   ```

3. 매개 변수에서 값을 사용하도록 SKU 속성을 변경합니다.

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. 파일을 저장합니다.

템플릿은 이제 다음과 같이 표시됩니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>다음 단계
* 템플릿이 완료되고 구독에 배포할 준비가 되었습니다. 배포하려면 [Azure에 리소스 배포](resource-manager-quickstart-deploy.md)를 참조하세요.
* 템플릿 구조에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿 작성하기](resource-group-authoring-templates.md)를 참조하세요.
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.

