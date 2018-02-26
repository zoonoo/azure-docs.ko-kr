---
title: "Azure Portal과 Azure Event Grid에 대한 사용자 지정 이벤트 | Microsoft Docs"
description: "Azure Event Grid 및 PowerShell을 사용하여 토픽을 게시하고 해당 이벤트를 구독합니다."
services: event-grid
keywords: 
author: tfitzmac
ms.author: tomfitz
ms.date: 01/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: f37d496d43bb24c51d6e1c11b77d9ceba48b7b23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Azure Portal 및 Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 Azure Portal을 사용하여 사용자 지정 토픽을 만들고 해당 토픽을 구독하며 이벤트를 트리거하여 결과를 확인합니다. 일반적으로 웹후크 또는 Azure Function과 같은 이벤트에 응답하는 끝점으로 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 이벤트를 메시지를 수집하기만 하는 URL로 보냅니다. [RequestBin](https://requestb.in/) 또는 [Hookbin](https://hookbin.com/)에서 타사 도구를 사용하여 이 URL을 만듭니다.

>[!NOTE]
>처리량이 높은 경우에는 **RequestBin** 및 **Hookbin** 사용이 적합하지 않습니다. 이 도구는 순전히 시연을 위해서만 사용되었습니다. 한 번에 둘 이상의 이벤트를 푸시하면 도구에서 모든 이벤트가 표시되지 않을 수 있습니다.

작업을 완료하면 이벤트 데이터가 끝점에 보내진 것을 확인할 수 있습니다.

![이벤트 데이터](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

1. 왼쪽 탐색에서 **리소스 그룹**을 선택합니다. 그런 후 **추가**를 선택합니다.

   ![리소스 그룹 만들기](./media/custom-event-quickstart-portal/create-resource-group.png)

1. 리소스 그룹 이름을 *gridResourceGroup*으로 설정하고 위치를 *westus2*로 설정합니다. **만들기**를 선택합니다.

   ![리소스 그룹 값 제공](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

토픽은 이벤트를 게시하는 사용자 정의 끝점을 제공합니다. 

1. 리소스 그룹에서 항목을 만들려면 **모든 서비스**를 선택하고 *Event Grid*를 검색합니다. 사용 가능한 옵션에서 **Event Grid 항목**을 선택합니다.

   ![Event Grid 항목 만들기](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. **추가**를 선택합니다.

   ![Event Grid 항목 추가](./media/custom-event-quickstart-portal/add-topic.png)

1. 항목의 이름을 입력합니다. DNS 항목으로 표시되기 때문에 토픽 이름은 고유해야 합니다. [지원되는 영역](overview.md) 중 하나를 선택합니다. 앞에서 만든 리소스 그룹을 선택합니다. **만들기**를 선택합니다.

   ![Event Grid 항목 값 제공](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. 항목을 만든 후에 **새로 고침**을 선택하여 항목을 확인합니다.

   ![Event Grid 항목 확인](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>메시지 끝점 만들기

토픽을 구독하기 전에 이벤트 메시지에 대한 끝점을 만들어 보겠습니다. 이벤트에 응답하는 코드를 작성하지 않고 메시지를 볼 수 있도록 메시지를 수집하는 끝점을 만들어 보겠습니다. RequestBin 및 Hookbin은 타사 도구로, 이 도구를 통해 끝점을 만들고 이 끝점에 전송된 요청을 볼 수 있습니다. [RequestBin](https://requestb.in/)으로 이동하고 **RequestBin 만들기**를 클릭하거나 [Hookbin](https://hookbin.com/)으로 이동하고 **새 끝점 만들기**를 클릭합니다.  토픽을 구독할 때 필요하기 때문에 bin URL을 복사합니다.

## <a name="subscribe-to-a-topic"></a>토픽 구독

토픽을 구독하여 Event Grid에 추적하려는 이벤트를 알립니다. 

1. Event Grid 구독을 만들려면 다시 **모든 서비스**를 선택하고 *Event Grid*를 검색합니다. 사용 가능한 옵션에서 **Event Grid 구독**을 선택합니다.

   ![Event Grid 구독 만들기](./media/custom-event-quickstart-portal/create-subscription.png)

1. **+ 이벤트 구독**을 선택합니다.

   ![Event Grid 구독 추가](./media/custom-event-quickstart-portal/add-subscription.png)

1. 이벤트 구독에 대한 고유한 이름을 제공합니다. 항목 형식에서 **Event Grid 항목**을 선택합니다. 인스턴스에서 만든 사용자 지정 항목을 선택합니다. 이벤트 알림에 대한 끝점으로 RequestBin 또는 Hookbin의 URL을 입력합니다. 값 입력을 완료한 후에 **만들기**를 선택합니다.

   ![Event Grid 구독 값 입력](./media/custom-event-quickstart-portal/provide-subscription-values.png)

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 끝점에 어떻게 배포하는지 살펴 보겠습니다. 이 문서를 간소화하기 위해 Cloud Shell을 사용하여 항목에 샘플 이벤트 데이터를 보냅니다. 일반적으로 응용 프로그램 또는 Azure 서비스는 이벤트 데이터를 보냅니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>토픽에 이벤트 보내기

먼저, 토픽에 대한 URL 및 키를 가져오겠습니다. `<topic_name>`의 항목 이름을 사용합니다.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

다음 예제에서는 샘플 이벤트 데이터를 가져옵니다.

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

`echo "$body"`를 수행한 경우 전체 이벤트를 확인할 수 있습니다. JSON의 `data` 요소는 이벤트의 페이로드입니다. 모든 잘 구성된(Well-Formed) JSON은 이 필드에 배치될 수 있습니다. 또한 고급 라우팅 및 필터링을 위해 제목 필드를 사용할 수 있습니다.

CURL은 HTTP 요청을 수행하는 유틸리티입니다. 이 문서에서는 토픽에 이벤트를 보내는 데 CURL을 사용합니다. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 끝점으로 메시지를 보냈습니다. 이전에 만든 끝점 URL로 이동합니다. 또는 열려 있는 브라우저에서 새로 고침을 클릭합니다. 방금 전송 받은 이벤트가 표시됩니다.

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
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>리소스 정리

이 이벤트로 작업을 계속하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 이 문서에서 만든 리소스를 삭제합니다.

리소스 그룹을 선택하고 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

토픽 및 이벤트 구독을 만드는 방법을 배웠으므로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

- [Event Grid 정보](overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 끝점으로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)
