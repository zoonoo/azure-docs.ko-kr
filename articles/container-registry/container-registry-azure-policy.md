---
title: Azure 정책을 사용한 규정 준수
description: Azure 컨테이너 레지스트리의 준수 여부를 감사하기 위해 Azure 정책에 기본 제공 정책을 할당합니다.
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330739"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 감사 준수

[Azure Policy는](../governance/policy/overview.md) Azure에서 정책을 만들고 할당하고 관리하는 데 사용하는 서비스입니다. 이러한 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스를 회사 표준 및 서비스 수준 계약을 준수하는 상태로 유지합니다.

이 문서에서는 Azure 컨테이너 레지스트리에 대한 기본 제공 정책(미리 보기)을 소개합니다. 이러한 정책을 사용하여 새 레지스트리와 기존 레지스트리를 감사하여 규정 준수를 검사합니다.

Azure 정책 사용에 대한 요금은 없습니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="built-in-policy-definitions"></a>기본 제공 정책 정의

다음 기본 제공 정책 정의는 Azure 컨테이너 레지스트리와 관련이 있습니다.

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

기본 제공 네트워크 정책 정의도 참조하십시오: [[미리 보기] 컨테이너 레지스트리가상 네트워크 서비스 끝점을 사용 해야합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)

## <a name="assign-policies"></a>정책 할당

* [Azure 포털,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md)리소스 [관리자 템플릿](../governance/policy/assign-policy-template.md)또는 Azure 정책 SDK를 사용하여 정책을 할당합니다.
* 리소스 그룹, 구독 또는 [Azure 관리 그룹에](../governance/management-groups/overview.md)대한 정책 할당범위를 지정합니다. 컨테이너 레지스트리 정책 할당은 범위 내의 기존 및 새 컨테이너 레지스트리에 적용됩니다.
* 언제든지 [정책 적용을](../governance/policy/concepts/assignment-structure.md#enforcement-mode) 활성화하거나 사용하지 않도록 설정합니다.

> [!NOTE]
> 정책을 할당하거나 업데이트한 후 정의된 범위의 리소스에 할당이 적용되는 데 다소 시간이 걸립니다. [정책 평가 트리거에](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)대한 정보를 참조하십시오.

## <a name="review-policy-compliance"></a>정책 준수 검토

Azure 포털, Azure 명령줄 도구 또는 Azure 정책 SDK를 사용하여 정책 할당에서 생성된 규정 준수 정보에 액세스합니다. 자세한 내용은 [Azure 리소스의 준수 데이터 가져오기를](../governance/policy/how-to/get-compliance-data.md)참조하십시오.

리소스가 비준수인 경우 여러 가지 이유가 있을 수 있습니다. 이유를 확인하거나 변경 책임자를 찾으려면 [비준수 확인](../governance/policy/how-to/determine-non-compliance.md)을 참조하십시오.

### <a name="policy-compliance-in-the-portal"></a>포털의 정책 준수:

1. **모든 서비스를**선택하고 **정책**을 검색합니다.
1. **규정 준수를**선택합니다.
1. 필터를 사용하여 규정 준수 상태를 제한하거나 ![포털에서](./media/container-registry-azure-policy/azure-policy-compliance.png)정책 준수 정책을 검색합니다.
1. 집계 규정 준수 세부 정보 및 이벤트를 검토하려면 정책을 선택합니다. 원하는 경우 리소스 준수를 위해 특정 레지스트리를 선택합니다.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI의 정책 준수

Azure CLI를 사용하여 규정 준수 데이터를 얻을 수도 있습니다. 예를 들어 CLI의 [az 정책 할당 목록](/cli/azure/policy/assignment#az-policy-assignment-list) 명령을 사용하여 적용되는 Azure 컨테이너 레지스트리 정책의 정책 등록을 가져옵니다.

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

샘플 출력:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

그런 다음 [az 정책 상태 목록을](/cli/azure/policy/state#az-policy-state-list) 실행하여 특정 정책 ID아래 모든 리소스에 대해 JSON 형식의 규정 준수 상태를 반환합니다.

```azurecli
az policy state list \
  --resource <policyID>
```

또는 [az 정책 상태 목록을](/cli/azure/policy/state#az-policy-state-list) 실행하여 *myregistry와*같은 특정 레지스트리 리소스의 JSON 형식의 준수 상태를 반환합니다.

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>다음 단계

* Azure 정책 [정의](../governance/policy/concepts/definition-structure.md) 및 [효과에](../governance/policy/concepts/effects.md) 대해 자세히 알아보기

* 사용자 [지정 정책 정의](../governance/policy/tutorials/create-custom-policy-definition.md) 만들기

* Azure의 [거버넌스 기능에](../governance/index.yml) 대해 자세히 알아보기


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/