---
title: 매개 변수 파일 만들기
description: Azure Resource Manager 템플릿을 배포하는 동안 값을 전달하기 위한 매개 변수 파일 만들기
ms.topic: conceptual
ms.date: 05/11/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8b7d9afc91462ec3dc61b25135460e347b121e5c
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960286"
---
# <a name="create-resource-manager-parameter-file"></a>Resource Manager 매개 변수 파일 만들기

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용할 수 있습니다. 이 문서에서는 JSON 템플릿에서 사용하는 매개 변수 파일을 만드는 방법을 보여 줍니다.

## <a name="parameter-file"></a>매개 변수 파일

매개 변수 파일은 다음 형식을 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

매개 변수 파일은 매개 변수 값을 일반 텍스트로 저장합니다. 이 방식은 리소스 SKU와 같이 중요하지 않은 값에 적용됩니다. 일반 텍스트는 암호와 같이 중요한 값에는 적용되지 않습니다. 중요한 값이 포함된 매개 변수를 전달해야 하는 경우 값을 키 자격 증명 모음에 저장합니다. 그런 다음, 매개 변수 파일에서 키 자격 증명 모음을 참조하세요. 중요한 값은 배포 중에 안전하게 검색됩니다.

다음 매개 변수 파일에는 키 자격 증명 모음에 저장된 일반 텍스트 값과 중요한 값이 포함됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

키 자격 증명 모음에서 값을 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](key-vault-parameter.md)을 참조하세요.

## <a name="define-parameter-values"></a>매개 변수 값 정의

매개 변수 이름과 값을 정의하는 방법을 결정하려면 JSON 템플릿을 열고 `parameters` 섹션을 검토합니다. 다음 예제는 JSON 템플릿 매개 변수를 보여 줍니다.

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

매개 변수 파일에서 확인할 첫 번째 세부 사항은 각 매개 변수의 이름입니다. 매개 변수 파일의 매개 변수 이름은 템플릿의 매개 변수 이름과 일치해야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

매개 변수 유형을 확인합니다. 매개 변수 파일의 매개 변수 형식은 템플릿과 동일한 형식을 사용해야 합니다. 이 예에서 두 매개 변수 형식은 모두 문자열입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

템플릿에서 기본값이 있는 매개 변수를 확인합니다. 매개 변수에 기본값이 있는 경우 매개 변수 파일에 값을 제공할 수 있지만 필수는 아닙니다. 매개 변수 파일 값은 템플릿의 기본값을 재정의합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

템플릿의 허용되는 값과 최대 길이 등의 제한을 확인합니다. 해당 값은 매개 변수에 제공할 수 있는 값의 범위를 지정합니다. 이 예제에서 `storagePrefix`는 최대 11자일 수 있으며 `storageAccountType`은 허용된 값을 지정해야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

> [!NOTE]
> 매개 변수 파일에는 템플릿에 정의된 매개 변수 값만 포함할 수 있습니다. 매개 변수 파일에 템플릿의 매개 변수와 일치하지 않는 추가 매개 변수가 포함된 경우 오류가 발생합니다.

## <a name="parameter-type-formats"></a>매개 변수 유형 형식

다음 예제에서는 문자열, 정수, 부울, 배열 및 개체와 같은 다양한 매개 변수 형식의 형식을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

## <a name="deploy-template-with-parameter-file"></a>매개 변수 파일을 사용하여 템플릿 배포

Azure CLI에서 `@` 및 매개 변수 파일 이름을 사용하여 로컬 매개 변수 파일을 전달합니다. 예들 들어 `@storage.parameters.json`입니다.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

자세한 내용은 [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](./deploy-cli.md#parameters)를 참조하세요.

Azure PowerShell에서 `TemplateParameterFile` 매개 변수를 사용하여 로컬 매개 변수 파일을 전달합니다.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

자세한 내용은 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](./deploy-powershell.md#pass-parameter-values)를 참조하세요.

> [!NOTE]
> 포털에서 사용자 지정 템플릿 블레이드에 매개 변수 파일을 사용할 수 없습니다.

> [!TIP]
> [Visual Studio의 Azure 리소스 그룹 프로젝트](create-visual-studio-deployment-project.md)를 사용하는 경우 매개 변수 파일의 **빌드 작업** 이 **콘텐츠** 로 설정되어 있는지 확인합니다.

## <a name="file-name"></a>파일 이름

매개 변수 파일의 일반적인 이름 지정 규칙은 템플릿 이름에 _parameters_ 를 포함하는 것입니다. 예를 들어, 템플릿의 이름이 _azuredeploy.json_ 인 경우 매개 변수 파일의 이름은 _azuredeploy.parameters.json_ 이 됩니다. 이 명명 규칙은 템플릿과 매개 변수 간의 연결을 확인하는 데 도움이 됩니다.

다른 환경에 배포하려면 둘 이상의 매개 변수 파일을 만듭니다. 매개 변수 파일의 이름을 지정할 때 개발 및 프로덕션 등의 용도를 확인합니다. 예를 들어 _azuredeploy.parameters-dev.json_ 및 _azuredeploy.parameters-prod.json_ 을 사용하여 리소스를 배포합니다.

## <a name="parameter-precedence"></a>매개 변수 우선 순위

동일한 배포 작업에서 인라인 매개 변수 및 로컬 매개 변수 파일을 사용할 수 있습니다. 예를 들어 로컬 매개 변수 파일에서 일부 값을 지정하고 배포하는 동안 인라인으로 다른 값을 추가할 수 있습니다. 로컬 매개 변수 파일 및 인라인에서 매개 변수에 대한 값을 제공하는 경우 인라인 값이 우선합니다.

파일에 URI를 제공하여 외부 매개 변수 파일을 사용할 수 있습니다. 외부 매개 변수 파일을 사용할 때 인라인 또는 로컬 파일에서 다른 값을 전달할 수 없습니다. 모든 인라인 매개 변수가 무시됩니다. 외부 파일에서 모든 매개 변수 값을 제공해야 합니다.

## <a name="parameter-name-conflicts"></a>매개 변수 이름 충돌

템플릿에 PowerShell 명령의 매개 변수 중 하나와 이름이 같은 매개 변수가 포함되어 있으면 PowerShell에서 접미사가 `FromTemplate`인 템플릿에서 매개 변수를 제공합니다. 예를 들어 템플릿의 `ResourceGroupName`이라는 매개 변수가 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet의 `ResourceGroupName` 매개 변수와 충돌합니다. `ResourceGroupNameFromTemplate`에 대한 값을 제공하라는 메시지가 표시됩니다. 이와 같은 혼동을 방지하려면 배포 명령에 사용되지 않은 매개 변수 이름을 사용합니다.

## <a name="next-steps"></a>다음 단계

- 템플릿의 매개 변수를 정의하는 방법에 대한 자세한 내용은 [ARM 템플릿의 매개 변수](./parameters.md)를 참조하세요.
- 키 자격 증명 모음에서 값을 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](key-vault-parameter.md)을 참조하세요.