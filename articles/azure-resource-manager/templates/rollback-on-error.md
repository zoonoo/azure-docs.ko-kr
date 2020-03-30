---
title: 오류 롤백하여 성공적인 배포
description: 실패한 배포가 성공적인 배포로 롤백되도록 지정합니다.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460146"
---
# <a name="rollback-on-error-to-successful-deployment"></a>오류 롤백하여 성공적인 배포

배포가 실패하면 배포 기록에서 이전에 성공한 배포를 자동으로 다시 배포할 수 있습니다. 이 기능은 인프라 배포에 대해 알려진 상태가 있고 이 상태로 되돌리려는 경우에 유용합니다. 주의 사항 및 제한 사항은 다음과 같습니다.

- 재배포는 동일한 매개 변수로 이전에 실행된 것과 정확히 동일하게 실행됩니다. 매개 변수는 변경할 수 없습니다.
- 이전 배포는 전체 [모드를](./deployment-modes.md#complete-mode)사용하여 실행됩니다. 이전 배포에 포함되지 않은 리소스는 삭제되고 리소스 구성은 이전 상태로 설정됩니다. 배포 모드를 완전히 이해해야 [합니다.](./deployment-modes.md)
- 재배포는 리소스에만 영향을 미치며 데이터 변경내용은 영향을 받지 않습니다.
- 이 기능은 구독 또는 관리 그룹 수준 배포가 아닌 리소스 그룹 배포에서만 사용할 수 있습니다. 구독 수준 배포에 대한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기를](./deploy-to-subscription.md)참조하십시오.
- 루트 수준 배포에만 이 옵션을 사용할 수 있습니다. 중첩된 템플릿의 배포는 다시 배포할 수 없습니다.

이 옵션을 사용하려면 배포가 배포 기록에서 식별될 수 있도록 고유한 이름을 지정해야 합니다. 고유한 이름이 없으면 현재 실패한 배포가 기록에서 이전에 성공한 배포를 덮어쓸 수 있습니다.

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

- 둘 이상의 지역에서 서비스를 안전하게 출시하려면 [Azure Deployment Manager](deployment-manager-overview.md)를 참조하세요.
- 리소스 그룹에 있지만 템플릿에 정의되지 않은 리소스를 처리하는 방법을 지정하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
- SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](secure-template-with-sas-token.md)를 참조하세요.
