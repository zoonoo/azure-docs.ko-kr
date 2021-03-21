---
title: Azure API Management 정책에서 명명된 값을 사용하는 방법
description: Azure API Management 정책에서 명명된 값을 사용하는 방법을 알아봅니다. 명명 된 값은 Azure Key Vault에 저장 된 리터럴 문자열, 정책 식 및 암호를 포함할 수 있습니다.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 2bc9b1c5724fa7bab1fdf5ac9332d87ba03a6d11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545822"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Azure API Management 정책에서 명명 된 값 사용

[API Management 정책은](api-management-howto-policies.md) 게시자가 구성을 통해 API의 동작을 변경할 수 있도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 정책 설명은 리터럴 텍스트 값, 정책 식 및 명명된 값을 사용하여 생성할 수 있습니다.

*명명 된 값* 은 각 API Management 인스턴스에서 이름/값 쌍의 전역 컬렉션입니다. 컬렉션의 항목 수에는 제한이 없습니다. 명명 된 값은 모든 API 구성 및 정책에서 상수 문자열 값 및 암호를 관리 하는 데 사용할 수 있습니다. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Azure Portal의 명명 된 값":::

## <a name="value-types"></a>값 형식

|Type  |설명  |
|---------|---------|
|제목     |  리터럴 문자열 또는 정책 식     |
|비밀     |   API Management로 암호화 된 리터럴 문자열 또는 정책 식      |
|[주요 자격 증명 모음](#key-vault-secrets)     |  Azure 키 자격 증명 모음에 저장 된 암호의 식별자입니다.      |

일반 값 또는 비밀에는 [정책 식이](./api-management-policy-expressions.md)포함 될 수 있습니다. 예를 들어 식은 `@(DateTime.Now.ToString())` 현재 날짜 및 시간을 포함 하는 문자열을 반환 합니다.

명명 된 값 특성에 대 한 자세한 내용은 API Management [REST API 참조](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)를 참조 하세요.

## <a name="key-vault-secrets"></a>주요 자격 증명 모음 비밀

비밀 값을 API Management (사용자 지정 비밀)에 암호화 된 문자열로 저장 하거나 [Azure Key Vault](../key-vault/general/overview.md)에서 비밀을 참조할 수 있습니다. 

키 자격 증명 모음 암호를 사용 하는 것은 API Management 보안 향상에 도움이 되므로 권장 됩니다.

* 키 자격 증명 모음에 저장 된 암호는 서비스에서 재사용할 수 있습니다.
* 세부적인 [액세스 정책을](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) 암호에 적용할 수 있습니다.
* 키 자격 증명 모음에서 업데이트 된 비밀은 API Management에서 자동으로 회전 됩니다. 키 자격 증명 모음에서 업데이트 후에는 API Management의 명명 된 값이 4 시간 이내에 업데이트 됩니다. Azure Portal 또는 관리 REST API를 사용 하 여 암호를 수동으로 새로 고칠 수도 있습니다.

### <a name="prerequisites-for-key-vault-integration"></a>주요 자격 증명 모음 통합을 위한 필수 구성 요소

1. 주요 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal 사용 하 여 주요 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md)를 참조 하세요.
1. API Management 인스턴스에서 시스템 할당 또는 사용자 할당 [관리 id](api-management-howto-use-managed-service-identity.md) 를 사용 하도록 설정 합니다.
1. 자격 증명 모음에서 암호를 가져오고 나열할 수 있는 권한을 사용 하 여 관리 id에 [키 자격 증명 모음 액세스 정책을](../key-vault/general/assign-access-policy-portal.md) 할당 합니다. 정책을 추가 하려면 다음을 수행 합니다.
    1. 포털에서 키 자격 증명 모음으로 이동 합니다.
    1. **설정 > 액세스 정책 > + 액세스 정책 추가** 를 선택 합니다.
    1. **비밀 권한** 을 선택 하 고 **가져오기** 및 **목록** 을 선택 합니다.
    1. **보안 주체 선택** 에서 관리 id의 리소스 이름을 선택 합니다. 시스템이 할당 한 id를 사용 하는 경우 보안 주체는 API Management 인스턴스의 이름입니다.
1. 키 자격 증명 모음에 대 한 암호를 만들거나 가져옵니다. [빠른 시작: Azure Portal를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색을](../key-vault/secrets/quick-create-portal.md)참조 하세요.

키 자격 증명 모음 암호를 사용 하려면 [명명 된 값을 추가 하거나 편집](#add-or-edit-a-named-value)하 고 **key vault** 의 유형을 지정 합니다. 키 자격 증명 모음에서 암호를 선택 합니다.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>명명 된 값 추가 또는 편집

### <a name="add-a-key-vault-secret"></a>키 자격 증명 모음 비밀 추가

[Key vault 통합에 대 한 필수 조건을](#prerequisites-for-key-vault-integration)참조 하세요.

> [!CAUTION]
> API Management에서 키 자격 증명 모음 암호를 사용 하는 경우 키 자격 증명 모음에 액세스 하는 데 사용 되는 비밀, 키 자격 증명 모음 또는 관리 id를 삭제 하지 않도록 주의 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **Api** 에서 **명명 된 값**  >  **+ 추가** 를 선택 합니다.
1. **이름** 식별자를 입력 하 고 정책에서 속성을 참조 하는 데 사용 되는 **표시 이름을** 입력 합니다.
1. **값 형식** 에서 **Key vault** 를 선택 합니다.
1. 키 자격 증명 모음 암호의 식별자 (버전 없음)를 입력 하거나, **선택** 을 선택 하 여 키 자격 증명 모음에서 비밀을 선택 합니다.
    > [!IMPORTANT]
    > 키 자격 증명 모음 비밀 식별자를 직접 입력 하는 경우에는 버전 정보가 포함 되지 않아야 합니다. 그렇지 않으면 키 자격 증명 모음에서 업데이트 후에 API Management 자동으로 암호가 회전 되지 않습니다.
1. **클라이언트 id** 에서 시스템 할당 또는 기존 사용자 할당 관리 id를 선택 합니다. [API Management 서비스에서 관리 되는 id를 추가 하거나 수정](api-management-howto-use-managed-service-identity.md)하는 방법에 대해 알아봅니다.
    > [!NOTE]
    > Id에는 키 자격 증명 모음에서 암호를 가져오고 나열할 수 있는 권한이 필요 합니다. 키 자격 증명 모음에 대 한 액세스를 아직 구성 하지 않은 경우 API Management는 필요한 권한으로 id를 자동으로 구성할 수 있도록 메시지를 표시 합니다.
1. 명명 된 값을 구성 하 고 **저장** 하는 데 도움이 되는 하나 이상의 선택적 태그를 추가 합니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="키 자격 증명 모음 비밀 값 추가":::

### <a name="add-a-plain-or-secret-value"></a>일반 또는 비밀 값 추가

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **Api** 에서 **명명 된 값**  >  **+ 추가** 를 선택 합니다.
1. **이름** 식별자를 입력 하 고 정책에서 속성을 참조 하는 데 사용 되는 **표시 이름을** 입력 합니다.
1. **값 형식** 에서 **일반** 또는 **암호** 를 선택 합니다.
1. **값** 에 문자열 또는 정책 식을 입력 합니다.
1. 명명 된 값을 구성 하 고 **저장** 하는 데 도움이 되는 하나 이상의 선택적 태그를 추가 합니다.
1. **만들기** 를 선택합니다.

명명 된 값을 만든 후에는 이름을 선택 하 여 해당 값을 편집할 수 있습니다. 표시 이름을 변경 하면 해당 명명 된 값을 참조 하는 모든 정책이 새 표시 이름을 사용 하도록 자동으로 업데이트 됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용을 시작하려면 다음을 수행합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

명명 된 값을 추가 하려면 [az apim nv create](/cli/azure/apim/nv#az_apim_nv_create) 명령을 사용 합니다.

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

명명 된 값을 만든 후에는 [az apim nv update](/cli/azure/apim/nv#az_apim_nv_update) 명령을 사용 하 여 업데이트할 수 있습니다. 모든 명명 된 값을 보려면 [az apim nv list](/cli/azure/apim/nv#az_apim_nv_list) 명령을 실행 합니다.

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

이 예제에 대해 만든 명명 된 값의 세부 정보를 보려면 [az apim nv show](/cli/azure/apim/nv#az_apim_nv_show) 명령을 실행 합니다.

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

이 예는 비밀 값입니다. 이전 명령은 값을 반환 하지 않습니다. 값을 확인 하려면 [az apim nv show-secret](/cli/azure/apim/nv#az_apim_nv_show_secret) 명령을 실행 합니다.

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

명명 된 값을 삭제 하려면 [az apim nv delete](/cli/azure/apim/nv#az_apim_nv_delete) 명령을 사용 합니다.

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>명명 된 값 사용

이 섹션의 예에서는 다음 표에 표시 된 명명 된 값을 사용 합니다.

| Name               | 값                      | 비밀 | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | 거짓  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | 거짓  | 

정책에 명명 된 값을 사용 하려면 `{{ContosoHeader}}` 다음 예제에 표시 된 것 처럼와 같이 이중 중괄호 쌍 안에 표시 이름을 추가 합니다.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

이 예제에서 `ContosoHeader`는 `set-header` 정책의 헤더 이름으로 사용되고, `ContosoHeaderValue`는 해당 헤더의 값으로 사용됩니다. API Management 게이트웨이에 대한 요청이나 응답 중에 이 정책을 평가하는 경우 `{{ContosoHeader}}` 및 `{{ContosoHeaderValue}}`가 해당 값으로 바뀝니다.

명명된 값은 위 예제에 표시된 대로 전체 특성 또는 요소 값으로 사용될 수 있지만 다음 예제와 같이 리터럴 텍스트 식의 일부로 삽입되거나 결합될 수도 있습니다.  

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

또한 명명된 값은 정책 식을 포함할 수 있습니다. 다음 예에서는 `ExpressionProperty` 식이 사용 됩니다.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

이 정책을 평가 하면 `{{ExpressionProperty}}` 가 해당 값으로 대체 됩니다 `@(DateTime.Now.ToString())` . 이 값은 정책 식이므로 식이 계산되고 정책이 계속 실행됩니다.

범위에 명명 된 값을 가진 정책이 있는 작업을 호출 하 여 Azure Portal 또는 [개발자 포털](api-management-howto-developer-portal.md) 에서이를 테스트할 수 있습니다. 다음 예제에서는 명명된 값이 있는 이전 두 예제 `set-header` 정책으로 작업이 호출되었습니다. 응답에는 명명 된 값이 있는 정책을 사용 하 여 구성 된 두 개의 사용자 지정 헤더가 포함 되어 있습니다.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API 응답 테스트":::

명명 된 값을 사용 하 여 이전의 두 샘플 정책을 포함 하는 호출에 대 한 아웃 바운드 [API 추적](api-management-howto-api-inspector.md) 을 살펴보면 명명 된 값이 삽입 된 두 개의 `set-header` 정책과 정책 식을 포함 하는 명명 된 값에 대 한 정책 식 평가를 확인할 수 있습니다.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API 검사기 추적":::

> [!CAUTION]
> 정책이 Azure Key Vault의 비밀을 참조 하는 경우 [API 요청 추적](api-management-howto-api-inspector.md)에 사용 하도록 설정 된 구독에 대 한 액세스 권한이 있는 사용자에 게 Key Vault의 값이 표시 됩니다.

정책 식은 명명된 값에 포함할 수 있지만, 다른 명명된 값은 포함할 수 없습니다. 명명된 값 참조가 포함된 텍스트가 `Text: {{MyProperty}}`와 같은 값에 사용되는 경우 해당 참조는 확인되지 않고 바뀌지 않습니다.

## <a name="delete-a-named-value"></a>명명 된 값 삭제

명명 된 값을 삭제 하려면 이름을 선택 하 고 상황에 맞는 메뉴 (**...**)에서 **삭제** 를 선택 합니다.

> [!IMPORTANT]
> API Management 정책에서 명명 된 값을 참조 하는 경우 해당 값을 사용 하는 모든 정책에서 명명 된 값을 제거할 때까지 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계

-   정책 작업에 대한 자세한 정보
    -   [API Management의 정책](api-management-howto-policies.md)
    -   [정책 참조](./api-management-policies.md)
    -   [정책 식](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

