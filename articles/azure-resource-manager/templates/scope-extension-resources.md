---
title: 확장 리소스 형식에 대 한 범위
description: 확장 리소스 유형을 배포할 때 scope 속성을 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681584"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>ARM 템플릿에서 확장 리소스의 범위 설정

확장 리소스는 다른 리소스를 수정 하는 리소스입니다. 예를 들어 리소스에 역할을 할당 하 여 액세스를 제한할 수 있습니다. 역할 할당은 확장 리소스 형식입니다.

확장 리소스 형식에 대 한 전체 목록은 [다른 리소스의 기능을 확장 하는 리소스 종류](../management/extension-resource-types.md)를 참조 하세요.

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 배포할 때 확장 리소스 형식에 대 한 범위를 설정 하는 방법을 보여 줍니다. 리소스에 적용할 때 확장 리소스에 사용할 수 있는 scope 속성을 설명 합니다.

## <a name="apply-at-deployment-scope"></a>배포 범위에서 적용

대상 배포 범위에서 확장 리소스 형식을 적용 하려면 리소스 형식과 마찬가지로 리소스를 템플릿에 추가 합니다. 사용 가능한 범위는 [리소스 그룹](deploy-to-resource-group.md), [구독](deploy-to-subscription.md), [관리 그룹](deploy-to-management-group.md)및 [테 넌 트](deploy-to-tenant.md)입니다. 배포 범위는 리소스 종류를 지원 해야 합니다.

다음 템플릿은 잠금을 배포 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

리소스 그룹에 배포 되 면 리소스 그룹을 잠급니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

다음 예에서는 역할을 할당 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

구독에 배포 하는 경우 구독에 역할을 할당 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>리소스에 적용

리소스에 확장 리소스를 적용 하려면 속성을 사용 `scope` 합니다. 범위 속성을 확장을 추가 하는 리소스의 이름으로 설정 합니다. Scope 속성은 확장 리소스 형식에 대 한 루트 속성입니다.

다음 예제에서는 저장소 계정을 만들고 여기에 역할을 적용 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>다음 단계

* 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.
* SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](secure-template-with-sas-token.md)를 참조하세요.
