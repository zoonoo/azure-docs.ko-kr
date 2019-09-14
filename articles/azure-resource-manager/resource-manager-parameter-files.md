---
title: Azure Resource Manager 매개 변수 파일 만들기
description: Azure Resource Manager 템플릿을 배포 하는 동안 값을 전달 하기 위한 매개 변수 파일 만들기
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: 4305213d272172cb89bfdd207b6c8106af3f4939
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983943"
---
# <a name="create-resource-manager-parameter-file"></a>리소스 관리자 매개 변수 파일 만들기

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용하는 것이 더 쉬울 수 있습니다. 이 문서에서는 매개 변수 파일을 만드는 방법을 보여 줍니다.

## <a name="parameter-file"></a>매개 변수 파일

매개 변수 파일의 형식은 다음과 같습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "<first-parameter-name>": {
            "value": "<first-value>"
        },
        "<second-parameter-name>": {
            "value": "<second-value>"
        }
    }
}
```

매개 변수 값은 매개 변수 파일에 일반 텍스트로 저장 됩니다. 이 방법은 리소스의 SKU를 지정 하는 것과 같이 중요 하지 않은 값에 대해 작동 합니다. 암호와 같은 중요 한 값에 대해서는 작동 하지 않습니다. 중요 한 값을 매개 변수로 전달 해야 하는 경우 키 자격 증명 모음에 값을 저장 하 고 매개 변수 파일에서 키 자격 증명 모음을 참조 합니다. 중요 한 값은 배포 중에 안전 하 게 검색 됩니다.

다음 매개 변수 파일에는 키 자격 증명 모음에 저장 된 일반 텍스트 값과 값이 포함 됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "<first-parameter-name>": {
            "value": "<first-value>"
        },
        "<second-parameter-name>": {
            "reference": {
                "keyVault": {
                    "id": "<resource-id-key-vault>"
                },
                "secretName": "<secret-name>"
            }
        }
    }
}
```

키 자격 증명 모음의 값 사용에 대 한 자세한 내용은 [Azure Key Vault를 사용 하 여 배포 중에 보안 매개 변수 값 전달](resource-manager-keyvault-parameter.md)을 참조 하세요.

## <a name="define-parameter-values"></a>매개 변수 값 정의

매개 변수 값을 정의 하는 방법을 확인 하려면 배포 하는 템플릿을 엽니다. 템플릿의 매개 변수 섹션을 확인 합니다. 다음 예제에서는 템플릿의 매개 변수를 보여 줍니다.

```json
"parameters": {
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
}
```

가장 먼저 알 수 있는 세부 정보는 각 매개 변수의 이름입니다. 매개 변수 파일의 값은 이름과 일치 해야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
        },
        "storageAccountType": {
        }
    }
}
```

매개 변수의 형식을 확인 합니다. 매개 변수 파일의 값은 동일한 형식 이어야 합니다. 이 템플릿의 경우 두 매개 변수를 모두 문자열로 제공할 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": ""
        },
        "storageAccountType": {
            "value": ""
        }
    }
}
```

그런 다음 기본값을 찾습니다. 매개 변수에 기본값이 있는 경우 값을 제공할 수 있지만 필요 하지는 않습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": "" // This value must be provided.
        },
        "storageAccountType": {
            "value": "" // This value is optional. Template will use default value if not provided.
        }
    }
}
```

마지막으로 허용 되는 값과 최대 길이와 같은 제한을 확인 합니다. 매개 변수에 제공할 수 있는 값의 범위를 알려 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": "storage"
        },
        "storageAccountType": {
            "value": "Standard_ZRS"
        }
    }
}
```

## <a name="parameter-type-formats"></a>매개 변수 형식 형식

다음 예제에서는 다양 한 매개 변수 형식의 형식을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "exampleString": {
            "value": "test string"
        },
        "exampleInt": {
            "value": 4
        },
        "exampleBool": {
            "value": true
        },
        "exampleArray": {
            "value": [
                "value 1",
                "value 2"
            ]
        },
        "exampleObject": {
            "value": {
                "property1": "value1",
                "property2": "value2"
            }
        }
   }
}
```

## <a name="file-name"></a>파일 이름

매개 변수 파일의 이름을 지정 하는 일반적인 규칙은 템플릿 이름에 **. 매개 변수** 를 추가 하는 것입니다. 예를 들어 템플릿의 이름이 **azuredeploy. json**인 경우 매개 변수 파일의 이름은 **azuredeploy. json**입니다. 이 명명 규칙은 템플릿과 매개 변수 간의 연결을 확인 하는 데 도움이 됩니다.

다른 환경에 배포 하려면 둘 이상의 매개 변수 파일을 만듭니다. 매개 변수 파일의 이름을 지정할 때 사용을 식별 하는 방법을 추가 합니다. 예를 들어 **azuredeploy. parameters-dev** 및 **azuredeploy** 를 사용 합니다.


## <a name="parameter-precedence"></a>매개 변수 우선 순위

동일한 배포 작업에서 인라인 매개 변수 및 로컬 매개 변수 파일을 사용할 수 있습니다. 예를 들어 로컬 매개 변수 파일에서 일부 값을 지정하고 배포하는 동안 인라인으로 다른 값을 추가할 수 있습니다. 로컬 매개 변수 파일 및 인라인에서 매개 변수에 대한 값을 제공하는 경우 인라인 값이 우선합니다.

하지만 외부 매개 변수 파일을 사용하면 인라인 또는 로컬 파일에서 다른 값을 전달할 수 없습니다. 모든 인라인 매개 변수는 무시 됩니다. 외부 파일에서 모든 매개 변수 값을 제공해야 합니다.

## <a name="parameter-name-conflicts"></a>매개 변수 이름 충돌

템플릿에 PowerShell 명령의 매개 변수 중 하나와 이름이 같은 매개 변수가 포함되어 있으면 PowerShell에서 접미사가 **FromTemplate**인 템플릿에서 매개 변수를 제공합니다. 예를 들어 템플릿의 **ResourceGroupName** 매개 변수는 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet의 **ResourceGroupName** 매개 변수와 충돌합니다. **ResourceGroupNameFromTemplate**에 대한 값을 제공하라는 메시지가 표시됩니다. 배포 명령에 사용 되지 않는 매개 변수 이름을 사용 하 여 이러한 혼동을 피할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 템플릿에서 매개 변수를 정의 하는 방법을 이해 하려면 [Azure Resource Manager 템플릿의 매개 변수](template-parameters.md)를 참조 하세요.
- 키 자격 증명 모음의 값 사용에 대 한 자세한 내용은 [Azure Key Vault를 사용 하 여 배포 중에 보안 매개 변수 값 전달](resource-manager-keyvault-parameter.md)을 참조 하세요.
- 매개 변수에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 매개 변수](template-parameters.md)를 참조 하세요.
