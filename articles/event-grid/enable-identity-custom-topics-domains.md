---
title: Azure Event Grid 사용자 지정 항목 및 도메인에서 관리 되는 id 사용
description: 이 문서에서는 Azure Event Grid 사용자 지정 토픽 또는 도메인에 대해 관리 서비스 id를 사용 하도록 설정 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 06fd4d6e472b33496e773596b0f3afc8e70be948
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630534"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Event Grid 사용자 지정 토픽 또는 도메인에 시스템 관리 id 할당 
이 문서에서는 Event Grid 사용자 지정 토픽 또는 도메인에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

## <a name="enable-identity-at-the-time-of-creation"></a>만들 때 id 사용

### <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal에서 만들 때 사용자 지정 토픽 또는 도메인에 대해 시스템 할당 id를 사용 하도록 설정할 수 있습니다. 다음 이미지에서는 사용자 지정 항목에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 기본적으로 토픽 만들기 마법사의 **고급** 페이지에서 **시스템 할당 ID 사용** 옵션을 선택합니다. 도메인 만들기 마법사의 **고급** 페이지에도이 옵션이 표시 됩니다. 

![사용자 지정 토픽을 만드는 동안 id 사용](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Azure CLI 사용
또한 Azure CLI를 사용 하 여 시스템 할당 id를 사용 하 여 사용자 지정 토픽 또는 도메인을 만들 수 있습니다. `--identity` 매개 변수를 `systemassigned`로 설정하여 `az eventgrid topic create` 명령을 사용합니다. 이 매개 변수에 대한 값을 지정하지 않으면 기본값 `noidentity`가 사용됩니다. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

마찬가지로, `az eventgrid domain create` 명령을 사용하여 시스템 관리 ID로 도메인을 만들 수 있습니다.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>기존 사용자 지정 토픽 또는 도메인에 대해 id 사용
이 섹션에서는 기존 사용자 지정 토픽 또는 도메인에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 

### <a name="using-azure-portal"></a>Azure Portal 사용
다음 절차에서는 사용자 지정 항목에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 도메인에 대 한 id를 설정 하는 단계는 유사 합니다. 

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **event grid 항목** 을 검색 합니다.
3. 관리 id를 사용 하도록 설정 하려는 **사용자 지정 항목** 을 선택 합니다. 
4. **ID** 탭으로 전환합니다. 
5. 스위치 **를 설정 하** 여 id를 사용 하도록 설정 합니다. 
1. 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="사용자 지정 항목에 대 한 id 페이지"::: 

비슷한 단계를 사용 하 여 event grid 도메인에 대 한 id를 설정할 수 있습니다.

### <a name="use-the-azure-cli"></a>Azure CLI 사용
로 `az eventgrid topic update` 설정 된 명령을 사용 하 여 `--identity` `systemassigned` 기존 사용자 지정 항목에 대해 시스템이 할당 한 id를 사용 하도록 설정 합니다. ID를 사용하지 않도록 설정하려면 `noidentity`를 값으로 지정합니다. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

기존 도메인을 업데이트하는 명령은 유사합니다(`az eventgrid domain update`).


## <a name="next-steps"></a>다음 단계
대상의 적절 한 역할 Service Bus (예: Service Bus 큐)에 id를 추가 합니다. 자세한 단계는 [대상의 Azure 역할에 Id 추가](add-identity-roles.md)를 참조 하세요. 
