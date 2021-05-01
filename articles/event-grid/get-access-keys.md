---
title: Event Grid 리소스에 대한 액세스 키 가져오기
description: 이 문서에서는 Event Grid 토픽 또는 도메인에 대한 액세스 키를 가져오는 방법을 설명합니다.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775226"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Event Grid 리소스(토픽 또는 도메인)에 대한 액세스 키 가져오기
액세스 키는 Azure Event Grid 리소스(토픽 및 도메인)에 애플리케이션 게시 이벤트를 인증하는 데 사용됩니다. 키를 정기적으로 다시 생성하고 안전하게 저장하는 것이 좋습니다. 다른 키를 다시 생성하는 동안 키를 사용하여 연결을 유지 관리할 수 있도록 두 개의 액세스 키가 제공됩니다.

이 문서에서는 Azure Portal, PowerShell 또는 CLI를 사용하여 Event Grid 리소스(토픽 또는 도메인)에 대한 액세스 키를 가져오는 방법을 설명합니다. 

## <a name="azure-portal"></a>Azure Portal
Azure Portal에서 토픽 또는 도메인에 대한 **Event Grid 토픽** 또는 **Event Grid 도메인** 페이지의 **액세스 키** 탭으로 전환합니다.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="액세스 키 페이지":::

## <a name="azure-powershell"></a>Azure PowerShell
토픽에 대한 액세스 키를 가져오려면 [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) 명령을 사용합니다. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

도메인에 대한 액세스 키를 가져오려면 [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) 명령을 사용합니다. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
토픽에 대한 액세스 키를 가져오려면 [az eventgrid topic key list](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list)를 사용합니다. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

도메인에 대한 액세스 키를 가져오려면 [az eventgrid domain key list](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list)를 사용합니다. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>다음 단계
[게시 클라이언트 인증](security-authenticate-publishing-clients.md) 문서를 참조하세요. 
