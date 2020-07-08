---
title: Event Grid 리소스에 대 한 액세스 키 가져오기
description: 이 문서에서는 Event Grid 토픽 또는 도메인에 대 한 액세스 키를 가져오는 방법을 설명 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 75ab09de40a4305ebd215985f2f9f181bfd69a24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414861"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Event Grid 리소스 (토픽 또는 도메인)에 대 한 액세스 키 가져오기
액세스 키는 리소스 (토픽 및 도메인)를 Azure Event Grid 응용 프로그램 게시 이벤트를 인증 하는 데 사용 됩니다. 키를 정기적으로 다시 생성 하 고 안전 하 게 저장 하는 것이 좋습니다. 다른 키를 다시 생성 하는 동안 한 키를 사용 하 여 연결을 유지할 수 있도록 두 개의 액세스 키가 제공 됩니다.

이 문서에서는 Azure Portal, PowerShell 또는 CLI를 사용 하 여 Event Grid 리소스 (토픽 또는 도메인)에 대 한 액세스 키를 가져오는 방법을 설명 합니다. 

## <a name="azure-portal"></a>Azure portal
Azure Portal에서 토픽 또는 도메인에 대 한 **Event Grid 토픽** 또는 **Event Grid 도메인** 페이지의 **액세스 키** 탭으로 전환 합니다.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="액세스 키 페이지":::

## <a name="azure-powershell"></a>Azure PowerShell
항목에 대 한 액세스 키를 가져오려면 [AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0) 명령을 사용 합니다. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

[AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0) 명령을 사용 하 여 도메인에 대 한 액세스 키를 가져옵니다. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
항목에 대 한 액세스 키를 가져오려면 [az event grid 토픽 키 목록을](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) 사용 합니다. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

[Az event grid 도메인 키 목록을](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) 사용 하 여 도메인에 대 한 액세스 키를 가져옵니다. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>다음 단계
[게시 클라이언트 인증](security-authenticate-publishing-clients.md)문서를 참조 하세요. 
