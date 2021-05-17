---
title: 오류 발생 시 성공적인 배포로 롤백
description: 실패한 배포가 성공적인 배포로 롤백되도록 지정합니다.
ms.topic: conceptual
ms.date: 02/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a0c4deef5956804aa74ad24f072bc46e5c2a8cd
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108321504"
---
# <a name="rollback-on-error-to-successful-deployment"></a>오류 발생 시 성공적인 배포로 롤백

배포가 실패하면 배포 기록에서 이전에 성공한 배포를 자동으로 다시 배포할 수 있습니다. 이 기능은 인프라 배포에 대한 알려진 양호한 상태가 있으며 이 상태로 되돌리려는 경우에 유용합니다. 이전의 특정 배포 또는 마지막으로 성공한 배포를 지정할 수 있습니다.

> [!IMPORTANT]
> 이 기능은 이전 배포를 다시 배포하여 실패한 배포를 롤백합니다. 이 결과는 실패한 배포를 실행 취소하는 것과 다를 수 있습니다. 이전 배포를 다시 배포하는 방법을 이해해야 합니다.

## <a name="considerations-for-redeploying"></a>재배포 시 고려 사항

이 기능을 사용하기 전에 재배포 처리 방식에 대한 다음 정보를 고려하세요.

- 이전 배포에서 [증분 모드](./deployment-modes.md#incremental-mode)를 사용한 경우에도 이전 배포가 [전체 모드](./deployment-modes.md#complete-mode)를 사용하여 실행됩니다. 증분을 사용한 이전 배포를 전체 모드로 다시 배포하면 예기치 않은 결과가 발생할 수 있습니다. 전체 모드는 이전 배포에 포함되지 않은 모든 리소스가 삭제됨을 의미합니다. 리소스 그룹에 있는 모든 리소스 및 해당 상태를 나타내는 이전 배포를 지정하세요. 자세한 내용은 [배포 모드](./deployment-modes.md)를 참조하세요.
- 재배포는 동일한 매개 변수를 사용하여 이전에 실행된 것과 정확히 동일하게 실행됩니다. 매개 변수를 변경할 수는 없습니다.
- 재배포는 리소스에만 영향을 주며 데이터 변경 내용은 영향을 받지 않습니다.
- 이 기능은 리소스 그룹 배포에만 사용할 수 있습니다. 구독, 관리 그룹 또는 테넌트 수준 배포는 지원하지 않습니다. 구독 수준 배포에 대한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](./deploy-to-subscription.md)를 참조하세요.
- 루트 수준 배포에만 이 옵션을 사용할 수 있습니다. 중첩된 템플릿의 배포는 다시 배포할 수 없습니다.

이 옵션을 사용하려면 배포 기록에 배포의 고유한 이름이 있어야 합니다. 특정 배포를 식별할 수 있는 고유한 이름이 있어야 사용할 수 있습니다. 고유한 이름이 없으면 실패한 배포가 기록에서 이전에 성공한 배포를 덮어쓸 수 있습니다.

배포 기록에 없는 이전 배포를 지정하는 경우 롤백이 오류를 반환합니다.

## <a name="powershell"></a>PowerShell

성공한 마지막 배포를 다시 배포하려면 `-RollbackToLastDeployment` 매개 변수를 플래그로 추가합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

특정 배포를 다시 배포하려면 `-RollBackDeploymentName` 매개 변수를 사용하고 배포의 이름을 제공합니다. 지정된 배포는 분명히 성공했을 것입니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

성공한 마지막 배포를 다시 배포하려면 `--rollback-on-error` 매개 변수를 플래그로 추가합니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

특정 배포를 다시 배포하려면 `--rollback-on-error` 매개 변수를 사용하고 배포의 이름을 제공합니다. 지정된 배포는 분명히 성공했을 것입니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

현재 배포가 실패할 경우 마지막으로 성공적으로 배포를 다시 배포하려면 다음을 사용합니다.

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

현재 배포가 실패할 경우 특정 배포를 다시 배포하려면 다음을 사용합니다.

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

지정된 배포는 분명히 성공했을 것입니다.

## <a name="next-steps"></a>다음 단계

- 전체 모드 및 증분 모드를 이해하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
