---
title: 확장 리소스 종류에 대한 범위
description: 확장 리소스 종류를 배포할 때 scope 속성을 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/13/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0f24c37c69394358e87407245f5db41ce8b6de80
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752052"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>ARM 템플릿에서 확장 리소스 범위 설정

확장 리소스는 다른 리소스를 수정하는 리소스입니다. 예를 들어 리소스에 역할을 할당할 수 있습니다. 역할 할당은 확장 리소스 종류입니다.

확장 리소스 종류의 전체 목록은 [다른 리소스의 기능을 확장하는 리소스 종류](../management/extension-resource-types.md)를 참조하세요.

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 배포할 때 확장 리소스 종류에 범위를 설정하는 방법을 보여 줍니다. 확장 리소스를 리소스에 적용할 때 사용할 수 있는 scope 속성을 설명합니다.

> [!NOTE]
> scope 속성은 확장 리소스 종류에만 사용할 수 있습니다. 확장 형식이 아닌 리소스 종류에 대해 다른 범위를 지정하려면 중첩 또는 연결된 배포를 사용합니다. 자세한 내용은 [리소스 그룹 배포](deploy-to-resource-group.md), [구독 배포](deploy-to-subscription.md), [관리 그룹 배포](deploy-to-management-group.md) 및 [테넌트 배포](deploy-to-tenant.md)를 참조하세요.

## <a name="apply-at-deployment-scope"></a>배포 범위에서 적용

대상 배포 범위에서 확장 리소스 종류를 적용하려면 리소스 종류에서와 마찬가지로 템플릿에 리소스를 추가합니다. 사용 가능한 범위는 [리소스 그룹](deploy-to-resource-group.md), [구독](deploy-to-subscription.md), [관리 그룹](deploy-to-management-group.md) 및 [테넌트](deploy-to-tenant.md)입니다. 배포 범위가 리소스 종류를 지원해야 합니다.

다음 템플릿에서는 잠금을 배포합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

리소스 그룹에 배포하면 리소스 그룹을 잠급니다.

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

다음 예제에서는 역할을 할당합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

구독에 배포하면 구독에 역할을 할당합니다.

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

리소스에 확장 리소스를 적용하려면 `scope` 속성을 사용합니다. Scope 속성을 확장을 추가하는 리소스의 이름으로 설정합니다. Scope 속성은 확장 리소스 유형의 루트 속성입니다.

다음 예제에서는 스토리지 계정을 만들고 여기에 역할을 적용합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>다음 단계

* 템플릿에서 매개 변수를 정의하는 방식을 이해하려면 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.
* SAS 토큰이 필요한 템플릿을 배포하는 방법에 관한 자세한 내용은 [SAS 토큰으로 프라이빗 ARM 템플릿 배포](secure-template-with-sas-token.md)를 참조하세요.
