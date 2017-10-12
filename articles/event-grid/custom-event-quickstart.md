---
title: "Azure Event Grid에 대한 사용자 지정 이벤트 | Microsoft Docs"
description: "Azure Event Grid를 사용하여 토픽을 게시하고 해당 이벤트를 구독합니다."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 08/15/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: cd285471196f75f6a8c8ead0e2895fd71414f223
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-route-custom-events-with-azure-event-grid"></a>Azure Event Grid로 사용자 지정 이벤트 만들기 및 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 Azure CLI를 사용하여 사용자 지정 토픽을 만들고 해당 토픽을 구독하며 이벤트를 트리거하여 결과를 확인합니다. 일반적으로 웹후크 또는 Azure Function과 같은 이벤트에 응답하는 끝점으로 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 이벤트를 메시지를 수집하기만 하는 URL로 보냅니다. 오픈 소스이면서 [RequestBin](https://requestb.in/)이라는 타사 도구를 사용하여 이 URL을 만듭니다.

>[!NOTE]
>**RequestBin**은 높은 처리량 사용을 위해 설계되지 않은 오픈 소스 도구입니다. 여기서는 순전히 시연을 위해서만 이 도구를 사용합니다. 한 번에 둘 이상의 이벤트를 푸시하면 도구에서 모든 이벤트가 표시되지 않을 수 있습니다.

작업을 완료하면 이벤트 데이터가 끝점에 보내진 것을 확인할 수 있습니다.

![이벤트 데이터](./media/custom-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 최신 버전의 Azure CLI(2.0.14 이상)을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. 

다음 예제에서는 *westus2* 위치에 *gridResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

토픽은 이벤트를 게시하는 사용자 정의 끝점을 제공합니다. 다음 예제에서는 리소스 그룹에 토픽을 만듭니다. `<topic_name>`을 토픽의 고유한 이름으로 바꿉니다. DNS 항목으로 표시되기 때문에 토픽 이름은 고유해야 합니다. 미리 보기 릴리스의 경우 Event Grid는 **westus2** 및 **westcentralus** 위치를 지원합니다.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>메시지 끝점 만들기

토픽을 구독하기 전에 이벤트 메시지에 대한 끝점을 만들어 보겠습니다. 이벤트에 응답하는 코드를 작성하지 않고 메시지를 볼 수 있도록 메시지를 수집하는 끝점을 만들어 보겠습니다. RequestBin은 오픈 소스이면서 타사 도구로, 이 도구를 통해 끝점을 만들고 끝점에 전송된 요청을 볼 수 있습니다. [RequestBin](https://requestb.in/)으로 이동하고 **RequestBin 만들기**를 클릭합니다.  토픽을 구독할 때 필요하기 때문에 bin URL을 복사합니다.

## <a name="subscribe-to-a-topic"></a>토픽 구독

토픽을 구독하여 Event Grid에 추적하려는 이벤트를 알립니다. 다음 예제에서는 사용자가 만든 토픽을 구독하고 RequestBin의 URL을 이벤트 알림에 대한 끝점으로 전달합니다. `<event_subscription_name>`을 구독의 고유한 이름으로, `<URL_from_RequestBin>`을 이전 섹션의 값으로 바꿉니다. 구독할 때 끝점을 지정하면 Event Grid에서 해당 끝점으로 이벤트 라우팅을 처리합니다. `<topic_name>`에는 앞에서 만든 값을 사용합니다. 

```azurecli-interactive
az eventgrid topic event-subscription create --name <event_subscription_name> \
  --endpoint <URL_from_RequestBin> \
  -g gridResourceGroup \
  --topic-name <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>토픽에 이벤트 보내기

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 끝점에 어떻게 배포하는지 살펴 보겠습니다. 먼저, 토픽에 대한 URL 및 키를 가져오겠습니다. 다시, `<topic_name>`의 토픽 이름을 사용합니다.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

이 문서를 간소화하기 위해 토픽에 보낼 샘플 이벤트 데이터를 설정했습니다. 일반적으로 응용 프로그램 또는 Azure 서비스는 이벤트 데이터를 보냅니다. 다음 예제는 이벤트 데이터를 가져옵니다.

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

`echo "$body"`를 수행한 경우 전체 이벤트를 확인할 수 있습니다. JSON의 `data` 요소는 이벤트의 페이로드입니다. 모든 잘 구성된(Well-Formed) JSON은 이 필드에 배치될 수 있습니다. 또한 고급 라우팅 및 필터링을 위해 제목 필드를 사용할 수 있습니다.

CURL은 HTTP 요청을 수행하는 유틸리티입니다. 이 문서에서는 토픽에 이벤트를 보내도록 CURL을 사용합니다. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 끝점으로 메시지를 보냈습니다. 이전에 만든 RequestBin URL로 이동합니다. 또는 열려 있는 RequestBin 브라우저에서 새로 고침을 클릭합니다. 방금 전송 받은 이벤트가 표시됩니다. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>리소스 정리
이 이벤트로 작업을 계속하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>다음 단계

토픽 및 이벤트 구독을 만드는 방법을 배웠으므로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

- [Event Grid 정보](overview.md)
- [Blob 저장소 이벤트를 사용자 지정 웹 끝점으로 라우팅(미리 보기)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid 및 Logic Apps를 사용하여 가상 컴퓨터 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)
