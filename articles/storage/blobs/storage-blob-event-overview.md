---
title: Azure Blob Storage 이벤트에 대응 | Microsoft Docs
description: Azure Event Grid를 사용하여 Blob Storage 이벤트를 구독합니다.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444673"
---
# <a name="reacting-to-blob-storage-events"></a>Blob Storage 이벤트에 대응

Azure Storage 이벤트는 최신 서버 리스 아키텍처를 사용 하 여 생성 및 blob의 삭제와 같은 이벤트에 반응 하는 응용 프로그램을 허용 합니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다.

통해 이벤트가 전달 되는 대신 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) Azure Functions, Azure Logic Apps와 같은 구독자 또는 사용자 고유의 사용자 지정 http 수신기 하에 대 한 요금만 있습니다 사용 하 여 합니다.

Blob storage 이벤트는 다양 한 다시 시도 정책 및 배달 못 한 편지를 통해 응용 프로그램에 신뢰할 수 있는 배달 서비스를 제공 하는 Event Grid 서비스를 안정적으로 전송 됩니다.

일반적인 Blob Storage 이벤트 시나리오에는 이미지 또는 비디오 처리, 검색 인덱싱, 또는 파일 중심의 워크플로가 포함됩니다. 비동기 파일 업로드는 이벤트에 매우 적합합니다. 변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

이제 직접 확인 하려는 경우 이러한 빠른 시작 문서 중 하나를 참조 하세요.

|이 도구를 사용 하려면:    |이 문서를 참조 하세요. |
|--|-|
|Azure Portal    |[빠른 시작: Azure portal 사용 하 여 웹 끝점으로 Blob 저장소 이벤트 라우팅](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[빠른 시작: PowerShell 사용 하 여 웹 끝점으로 저장소 이벤트 라우팅](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[빠른 시작: Azure CLI를 사용 하 여 웹 끝점으로 저장소 이벤트 라우팅](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>이벤트 모델

Event grid [이벤트 구독](../../event-grid/concepts.md#event-subscriptions) 구독자에 게 이벤트 메시지 경로입니다. 이 이미지 이벤트 게시자, 이벤트 구독 및 이벤트 처리기 간의 관계를 보여 줍니다.

![Event Grid 모델](./media/storage-blob-event-overview/event-grid-functional-model.png)

첫째, 이벤트에 대 한 끝점을 구독 합니다. 그런 다음 이벤트가 트리거되면 Event Grid 서비스에서 해당 이벤트에 대 한 데이터 끝점에 보냅니다.

참조 된 [Blob storage 이벤트 스키마](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 문서를 보려면:

> [!div class="checklist"]
> * Blob 저장소 이벤트 및 각 이벤트는 발생 하는 방법의 목록은 합니다.
> * 이러한 각 이벤트에 대 한 Event Grid는 데이터의 예로 보냅니다.
> * 데이터에 표시 되는 각 키-값 쌍의 용도입니다.

## <a name="filtering-events"></a>이벤트 필터링

Blob 이벤트 구독은 이벤트 형식에 따라, 그리고 생성 또는 삭제된 개체의 컨테이너 이름 및 Blob 이름을 기준으로 필터링할 수 있습니다.  이벤트 구독의 [생성](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 동안 또는 [나중에](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 이벤트 구독에 필터를 적용할 수 있습니다. Event Grid의 제목 필터는 "시작 문자" 및 "끝 문자" 일치 항목을 기준으로 작동하므로 일치하는 제목이 있는 이벤트는 구독자에게 전달됩니다.

필터를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요 [Event Grid에 대 한 이벤트를 필터링](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)합니다.

Blob Storage 이벤트의 제목은 다음 형식을 사용합니다.

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

저장소 계정의 모든 이벤트와 일치하는 항목을 찾으려면 제목 필터를 비워 둡니다.

접두사를 공유하는 컨테이너 집합에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containerprefix
```

특정 컨테이너에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containername/
```

Blob 이름 접두사를 공유하는 특정 컨테이너에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Blob 접미사를 공유하는 특정 컨테이너에 만들어진 Blob의 이벤트와 일치시키려면 `subjectEndsWith` 필터(예: ".log" 또는 ".jpg")를 사용합니다. 자세한 내용은 [Event Grid 개념](../../event-grid/concepts.md#event-subscriptions)을 참조하세요.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침

Blob Storage 이벤트를 처리하는 애플리케이션은 아래 권장되는 몇 가지 지침을 따라야 합니다.
> [!div class="checklist"]
> * 동일한 이벤트 처리기로 이벤트를 라우팅하도록 여러 구독이 구성될 수 있으므로, 이벤트가 특정 원본에서 온 것이라고 가정하지 않고 메시지의 토픽을 확인하여 예상하는 저장소 계정에서 왔음을 확실히 아는 것이 중요합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * 메시지가 잘못된 순서로 오거나 조금 늦게 도착할 수도 있으므로 etag 필드를 사용하여 개체에 대한 정보가 아직 최신 상태인지 여부를 확인합니다.  또한 sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * blobType 필드를 사용하여 Blob에 허용되는 작업 형식을 파악하고 Blob에 액세스하는 데 사용해야 하는 클라이언트 라이브러리 형식을 확인합니다. 유효한 값은 `BlockBlob` 또는 `PageBlob`입니다. 
> * Blob에 액세스하려면 `CloudBlockBlob` 및 `CloudAppendBlob` 생성자에 URL 필드를 사용합니다.
> * 이해할 수 없는 필드는 무시합니다. 이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
> * 확인 하려는 경우는 **Microsoft.Storage.BlobCreated** 블록 Blob을 완전히 커밋될 때에 이벤트가 트리거되면에 대 한 이벤트를 필터링 합니다 `CopyBlob`, `PutBlob`를 `PutBlockList` 또는 `FlushWithClose` REST API 호출 합니다. 이러한 API 호출 트리거를 **Microsoft.Storage.BlobCreated** 이벤트 데이터를 완벽 하 게 블록 Blob에 커밋된 후에 합니다. 필터를 만드는 방법에 알아보려면 참조 [Event Grid에 대 한 이벤트를 필터링](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)합니다.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Blob Storage 이벤트를 사용해보세요.

- [Event Grid 정보](../../event-grid/overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](storage-blob-event-quickstart.md)
