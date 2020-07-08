---
title: Azure Policy 사용 준수
description: Azure Policy의 기본 제공 정책을 할당 하 여 Azure SignalR 서비스 리소스의 준수를 감사 합니다.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 1e0b6fbcacf13296d1d219da82d1b6f4c74ad7fb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85131982"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Azure Policy를 사용 하 여 Azure SignalR Service 리소스의 준수 감사

[Azure Policy](../governance/policy/overview.md) 는 정책을 만들고 할당 하 고 관리 하는 데 사용 하는 Azure의 서비스입니다. 이러한 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스를 회사 표준 및 서비스 수준 계약을 준수하는 상태로 유지합니다.

이 문서에서는 Azure SignalR Service에 대 한 기본 제공 정책 (미리 보기)을 소개 합니다. 이러한 정책을 사용 하 여 신규 및 기존 SignalR 리소스의 준수를 감사 합니다.

Azure Policy 사용에 대 한 요금은 없습니다.

## <a name="built-in-policy-definitions"></a>기본 제공 정책 정의

다음 기본 제공 정책 정의는 Azure SignalR Service에만 적용 됩니다.

[!INCLUDE [azure-policy-samples-policies-signalr](../../includes/policy/samples/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>정책 정의 할당

* [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), [리소스 관리자 템플릿](../governance/policy/assign-policy-template.md)또는 Azure Policy sdk를 사용 하 여 정책 정의를 할당 합니다.
* 리소스 그룹, 구독 또는 [Azure 관리 그룹](../governance/management-groups/overview.md)에 대 한 정책 할당 범위를 지정 합니다. SignalR 정책 할당은 범위 내의 기존 및 새 SignalR 리소스에 적용 됩니다.
* 언제 든 지 [정책 적용](../governance/policy/concepts/assignment-structure.md#enforcement-mode) 을 사용 하거나 사용 하지 않도록 설정 합니다.

> [!NOTE]
> 정책을 할당 하거나 업데이트 한 후에는 할당을 정의 된 범위의 리소스에 적용 하는 데 약간의 시간이 걸립니다. [정책 평가 트리거에](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)대 한 정보를 참조 하세요.

## <a name="review-policy-compliance"></a>정책 준수 검토

Azure Portal, Azure 명령줄 도구 또는 Azure Policy Sdk를 사용 하 여 정책 할당에 의해 생성 된 호환성 정보에 액세스 합니다. 자세한 내용은 [Azure 리소스의 준수 데이터 가져오기](../governance/policy/how-to/get-compliance-data.md)를 참조 하세요.

리소스가 규정 비준수인 경우 여러 가지 원인이 있을 수 있습니다. 이유를 확인 하거나 변경 내용을 확인 하려면 [비준수 확인](../governance/policy/how-to/determine-non-compliance.md)을 참조 하세요.

### <a name="policy-compliance-in-the-portal"></a>포털의 정책 준수:

1. **모든 서비스**를 선택 하 고 **정책을**검색 합니다.
1. **준수**를 선택 합니다.
1. 필터를 사용 하 여 준수 상태를 제한 하거나 정책을 검색 합니다.
   
    [![포털 ](./media/signalr-howto-azure-policy/azure-policy-compliance.png) 의 정책 준수](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. 집계 준수 세부 정보 및 이벤트를 검토 하려면 정책을 선택 하세요. 필요한 경우 리소스 준수를 위한 특정 SignalR를 선택 합니다.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI에서 정책 준수

Azure CLI를 사용 하 여 준수 데이터를 가져올 수도 있습니다. 예를 들어 CLI에서 [az policy 할당 list](/cli/azure/policy/assignment#az-policy-assignment-list) 명령을 사용 하 여 적용 되는 Azure SignalR 서비스 정책의 정책 id를 가져옵니다.

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

샘플 출력:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

그런 다음 [az policy state list](/cli/azure/policy/state#az-policy-state-list) 를 실행 하 여 특정 리소스 그룹의 모든 리소스에 대해 JSON 형식 준수 상태를 반환 합니다.

```azurecli
az policy state list --g <resourceGroup>
```

또는 특정 SignalR 리소스의 JSON 형식 준수 상태를 반환 하려면 [az policy state list](/cli/azure/policy/state#az-policy-state-list) 를 실행 합니다.

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>다음 단계

* Azure Policy [정의](../governance/policy/concepts/definition-structure.md) 및 [효과](../governance/policy/concepts/effects.md) 에 대 한 자세한 정보

* [사용자 지정 정책 정의](../governance/policy/tutorials/create-custom-policy-definition.md) 만들기

* Azure의 [거 버 넌 스 기능](../governance/index.yml) 에 대 한 자세한 정보


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/