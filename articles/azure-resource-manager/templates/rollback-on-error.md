---
title: 성공적인 배포에 대 한 오류 발생 시 롤백
description: 실패 한 배포가 성공적인 배포로 롤백해야 하도록 지정 합니다.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460146"
---
# <a name="rollback-on-error-to-successful-deployment"></a>성공적인 배포에 대 한 오류 발생 시 롤백

배포가 실패하면 배포 기록에서 이전에 성공한 배포를 자동으로 다시 배포할 수 있습니다. 이 기능은 인프라 배포에 대 한 알려진 양호한 상태를가지고 있으며이 상태로 되돌리려는 경우에 유용 합니다. 몇 가지 주의 사항과 제한 사항이 있습니다.

- 재배포는 동일한 매개 변수를 사용 하 여 이전에 실행 된 것과 동일 하 게 실행 됩니다. 매개 변수를 변경할 수 없습니다.
- 이전 배포는 [전체 모드](./deployment-modes.md#complete-mode)를 사용 하 여 실행 됩니다. 이전 배포에 포함 되지 않은 모든 리소스는 삭제 되 고 모든 리소스 구성은 이전 상태로 설정 됩니다. [배포 모드](./deployment-modes.md)를 완전히 이해 해야 합니다.
- 재배포는 리소스에만 영향을 주며 데이터 변경 내용은 영향을 받지 않습니다.
- 이 기능은 구독 또는 관리 그룹 수준 배포가 아닌 리소스 그룹 배포에만 사용할 수 있습니다. 구독 수준 배포에 대 한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](./deploy-to-subscription.md)를 참조 하세요.
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
