---
title: 배포 모드
description: Azure Resource Manager를 사용하여 전체 또는 증분 배포 모드를 사용할지 여부를 지정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460248"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 배포 모드

리소스를 배포할 때 배포를 증분 업데이트 또는 전체 업데이트할지 지정합니다. 이 두 모드의 차이점은 Resource Manager가 템플릿에 없는 리소스 그룹의 기존 리소스를 처리하는 방법입니다.

두 모드에서 모두, Resource Manager는 템플릿에 지정된 모든 리소스를 만들려고 합니다. 리소스 그룹에 이미 리소스가 있고 해당 설정이 변경되지 않은 경우 해당 리소스에 대한 작업이 수행되지 않습니다. 리소스의 속성 값을 변경하는 경우 리소스가 새 값으로 업데이트됩니다. 기존 리소스의 위치 또는 종류를 업데이트하려고 하면 오류가 발생하여 배포에 실패합니다. 대신, 새 리소스를 필요한 위치 또는 유형으로 배포합니다.

기본 모드는 증분입니다.

## <a name="complete-mode"></a>전체 모드

전체 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **삭제**합니다.

[조건이](conditional-resource-deployment.md) false로 평가되기 때문에 템플릿에 배포되지 않은 리소스가 포함된 경우 결과는 템플릿을 배포하는 데 사용하는 REST API 버전에 따라 달라집니다. 2019-05-10 이전 버전을 사용하는 경우 리소스가 **삭제되지 않습니다.** 2019-05-10 이상에서는 리소스가 **삭제됩니다.** Azure PowerShell 및 Azure CLI의 최신 버전은 리소스를 삭제합니다.

복사 루프와 전체 모드를 [사용하여주의해야합니다.](copy-resources.md) 복사 루프를 해결한 후 템플릿에 지정되지 않은 리소스는 삭제됩니다.

[템플릿에서 둘 이상의 리소스 그룹에](cross-resource-group-deployment.md)배포하는 경우 배포 작업에 지정된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제되지 않습니다.

리소스 형식이 전체 모드 삭제를 처리하는 방법에는 몇 가지 차이점이 있습니다. 전체 모드로 배포된 템플릿에 없는 경우 부모 리소스가 자동으로 삭제됩니다. 일부 자식 리소스는 템플릿에 없더라도 자동으로 삭제되지 않습니다. 그러나 상위 리소스가 삭제되면 이러한 하위 리소스가 삭제됩니다.

예를 들어 리소스 그룹에 DNS 영역(Microsoft.Network/dnsZones 리소스 종류)과 CNAME 레코드(Microsoft.Network/dnsZones/CNAME 리소스 종류)가 포함된 경우 DNS 영역은 CNAME 레코드의 부모 리소스입니다. 전체 모드로 배포하고 템플릿에 DNS 영역을 포함하지 않으면 DNS 영역과 CNAME 레코드가 둘 다 삭제됩니다. 템플릿에 DNS 영역을 포함하지만 CNAME 레코드를 포함하지 않으면 CNAME가 삭제되지 않습니다.

리소스 종류의 삭제 처리 방식에 대한 목록은 [전체 모드 배포에 대한 Azure 리소스 삭제](complete-mode-deletion.md)를 참조하세요.

리소스 그룹이 [잠겨](../management/lock-resources.md)있는 경우 완료 모드는 리소스를 삭제하지 않습니다.

> [!NOTE]
> 루트 수준 템플릿만 전체 배포 모드를 지원합니다. [연결된 또는 중첩된 템플릿](linked-templates.md)의 경우 증분 모드만 사용해야 합니다.
>
> [구독 수준 배포는](deploy-to-subscription.md) 전체 모드를 지원하지 않습니다.
>
> 현재 포털은 전체 모드를 지원하지 않습니다.
>

## <a name="incremental-mode"></a>증분 모드

증분 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **변경하지 않고 유지**합니다. 템플릿의 리소스가 리소스 그룹에 **추가됩니다.**

> [!NOTE]
> 증분 모드에서 기존 리소스를 다시 배포하면 모든 속성이 다시 적용됩니다. **속성은 증분으로 추가되지 않습니다.** 일반적인 오해는 템플릿에 지정되지 않은 속성이 변경되지 않은 상태로 유지된다고 생각하는 것입니다. 특정 속성을 지정하지 않으면 Resource Manager는 배포를 해당 값을 덮어쓰는 것으로 해석합니다. 템플릿에 포함되지 않은 속성이 기본값으로 재설정됩니다. 업데이트하는 값뿐만 아니라 리소스에 대한 기본값이 아닌 모든 값을 지정합니다. 템플릿의 리소스 정의에는 항상 리소스의 최종 상태가 포함됩니다. 기존 리소스에 대한 부분 업데이트를 나타낼 수 없습니다.

## <a name="example-result"></a>결과 예제

증분 및 전체 모드 간의 차이를 보여주려면 다음 시나리오를 고려합니다.

**리소스 그룹**은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C

**템플릿**은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

리소스 그룹이 **증분** 모드로 배포된 경우 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C
* 리소스 D

리소스 C가 **전체** 모드로 배포된 경우 삭제됩니다. 리소스 그룹은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

## <a name="set-deployment-mode"></a>배포 모드 설정

PowerShell을 사용하여 배포하는 경우 배포 모드를 설정하려면 `Mode` 매개 변수를 사용합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Azure CLI를 사용하여 배포하는 경우 배포 모드를 설정하려면 `mode` 매개 변수를 사용합니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

다음 예제에서는 증분 배포 모드로 연결된 템플릿 세트를 보여줍니다.

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>다음 단계

* 리소스 관리자 템플릿을 만드는 방법에 대해 자세히 알아보려면 [Azure 리소스 관리자 템플릿 작성을](template-syntax.md)참조하십시오.
* 리소스 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.
