---
title: Azure Event Grid 사용자 지정 항목 및 도메인에서 관리 ID 사용
description: 이 문서에서는 Azure Event Grid 사용자 지정 항목 또는 도메인에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278221"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Event Grid 사용자 지정 항목 또는 도메인에 시스템 관리 ID 할당 
이 문서에서는 Event Grid 사용자 지정 항목 또는 도메인에 대해 시스템 관리 ID를 사용하도록 설정하는 방법을 보여줍니다. 관리 ID에 대해 알아보려면 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="enable-identity-at-the-time-of-creation"></a>생성 시 ID 사용

### <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal에서 만드는 동안 사용자 지정 항목 또는 도메인에 대해 시스템 할당 ID를 사용하도록 설정할 수 있습니다. 다음 이미지에서는 사용자 지정에 대해 시스템 관리 ID를 사용하도록 설정하는 방법을 보여줍니다. 기본적으로 토픽 만들기 마법사의 **고급** 페이지에서 **시스템 할당 ID 사용** 옵션을 선택합니다. 이 옵션은 도메인 만들기 마법사의 **고급** 페이지에도 표시됩니다. 

![사용자 지정 항목을 만드는 동안 ID 사용](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용하여 시스템 할당 ID로 사용자 지정 항목 또는 도메인을 만들 수도 있습니다. `--identity` 매개 변수를 `systemassigned`로 설정하여 `az eventgrid topic create` 명령을 사용합니다. 이 매개 변수에 대한 값을 지정하지 않으면 기본값 `noidentity`가 사용됩니다. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

마찬가지로, `az eventgrid domain create` 명령을 사용하여 시스템 관리 ID로 도메인을 만들 수 있습니다.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>기존 사용자 지정 항목 또는 도메인에 대한 ID 사용
이 섹션에서는 기존 사용자 지정 항목 또는 도메인에 대해 시스템 관리 ID를 사용하도록 설정하는 방법을 알아봅니다. 

### <a name="using-azure-portal"></a>Azure Portal 사용
다음 절차에서는 사용자 지정 항목에 대해 시스템 관리 ID를 사용하도록 설정하는 방법을 보여줍니다. 도메인에 대한 ID를 설정하는 단계는 유사합니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **이벤트 그리드 토픽** 을 검색합니다.
3. 관리 ID를 사용하도록 설정할 **사용자 지정 항목** 을 선택합니다. 
4. **ID** 탭으로 전환합니다. 
5. 이 전환을 **설정** 하여 ID를 사용하도록 설정합니다. 
1. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="사용자 지정 항목에 대한 ID 페이지"::: 

비슷한 단계를 사용하여 Event Grid 도메인에 대한 ID를 사용하도록 설정할 수 있습니다.

### <a name="use-the-azure-cli"></a>Azure CLI 사용
`--identity`를 `systemassigned`로 설정하여 `az eventgrid topic update` 명령을 사용하면 기존 사용자 지정 항목에 대해 시스템 할당 ID를 사용하도록 설정할 수 있습니다. ID를 사용하지 않도록 설정하려면 `noidentity`를 값으로 지정합니다. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

기존 도메인을 업데이트하는 명령은 유사합니다(`az eventgrid domain update`).


## <a name="next-steps"></a>다음 단계
대상(예: Service Bus 큐)의 적절한 역할(예: Service Bus 데이터 보낸 사람)에 ID를 추가합니다. 자세한 단계는 [관리 ID에 Event Grid 대상에 대한 액세스 권한 부여](add-identity-roles.md)를 참조하세요. 
