---
title: Azure Policy 사용 준수
description: Azure Policy의 기본 제공 정책을 할당하여 Azure Container Registry의 준수 감사
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784176"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Azure Policy를 사용하여 Azure Container Registry의 준수 감사

[Azure Policy](../governance/policy/overview.md)는 정책을 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다. 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스가 회사 표준 및 서비스 수준 약정을 준수하도록 유지합니다.

이 문서에서는 Azure Container Registry에 대한 기본 제공 정책을 소개합니다. 이러한 정책을 사용하여 새로운 레지스트리 및 기존 레지스트리를 준수하도록 감사합니다.

Azure Policy 사용에 따른 비용은 없습니다.

## <a name="built-in-policy-definitions"></a>기본 제공 정책 정의

다음 기본 제공 정책 정의는 Azure Container Registry에만 적용됩니다.

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>정책 할당

* [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), [Resource Manager 템플릿](../governance/policy/assign-policy-template.md) 또는 Azure Policy SDK를 사용하여 정책을 할당합니다.
* 리소스 그룹, 구독 또는 [Azure 관리 그룹](../governance/management-groups/overview.md)으로 정책 할당 범위를 지정합니다. 컨테이너 레지스트리 정책 할당은 범위 내의 기존 컨테이너 레지스트리 및 새 컨테이너 레지스트리에 적용됩니다.
* 언제든지 [정책 시행](../governance/policy/concepts/assignment-structure.md#enforcement-mode)을 사용하거나 사용하지 않도록 설정할 수 있습니다.

> [!NOTE]
> 정책을 할당하거나 업데이트한 후에는 정의된 범위의 리소스에 할당을 적용하는 데 약간의 시간이 걸립니다. [정책 평가 트리거](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)에 대한 정보를 참조하세요.

## <a name="review-policy-compliance"></a>정책 준수 검토

Azure Portal, Azure 명령줄 도구 또는 Azure Policy SDK를 사용하여 정책 할당에 의해 생성된 호환성 정보에 액세스합니다. 자세한 내용은 [Azure 리소스의 규정 준수 데이터 가져오기](../governance/policy/how-to/get-compliance-data.md)를 참조하세요.

리소스가 규정 비준수인 경우 여러 가지 원인이 있을 수 있습니다. 이유를 확인하거나 책임 있는 변경을 찾으려면 [비준수 확인](../governance/policy/how-to/determine-non-compliance.md)을 참조하세요.

### <a name="policy-compliance-in-the-portal"></a>포털의 정책 준수:

1. **모든 서비스** 를 선택하고 **정책** 을 검색합니다.
1. **규정 준수** 를 선택합니다.
1. 필터를 사용하여 준수 상태를 제한하거나 정책을 검색합니다.

    ![포털의 정책 준수](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. 준수 세부 정보 및 이벤트 집계를 검토하려면 정책을 선택합니다. 필요한 경우 리소스 준수에 대한 특정 레지스트리를 선택합니다.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI에서 정책 준수

Azure CLI를 사용하여 준수 데이터를 가져올 수도 있습니다. 예를 들어 CLI에서 [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) 명령을 사용하여 적용된 Azure Container Registry 정책의 정책 ID를 가져옵니다.

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

샘플 출력:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

그런 다음 [az policy state list](/cli/azure/policy/state#az_policy_state_list)를 실행하여 특정 정책 ID의 모든 리소스에 대해 JSON 형식의 준수 상태를 반환합니다.

```azurecli
az policy state list \
  --resource <policyID>
```

또는 [az policy state list](/cli/azure/policy/state#az_policy_state_list)를 실행하여 *myregistry* 와 같은 특정 레지스트리 리소스의 JSON 형식 준수 상태를 반환합니다.

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>다음 단계

* Azure Policy [정의](../governance/policy/concepts/definition-structure.md) 및 [효과](../governance/policy/concepts/effects.md)에 대해 자세히 알아봅니다.

* [사용자 지정 정책 정의](../governance/policy/tutorials/create-custom-policy-definition.md)를 만듭니다.

* Azure의 [거버넌스 기능](../governance/index.yml)에 대해 자세히 알아봅니다.
