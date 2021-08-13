---
title: Azure Functions Event Grid의 로컬 디버깅
description: Event Grid 이벤트에 의해 트리거되는 Azure Functions를 로컬로 디버그하는 방법 알아보기
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 240b721d71ef53ad6f43f076f1d5db0e29b556a4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970925"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure Function Event Grid 트리거 로컬 디버깅

이 문서에서는 스토리지 계정에서 발생하는 Azure Event Grid 이벤트를 처리하는 로컬 함수를 디버그하는 방법을 설명합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 기존 함수 앱 만들기 또는 사용
- 기존 스토리지 계정 만들기 또는 사용 Event Grid 알림 구독은 `BlobStorage`, `StorageV2` 또는 [Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)용 Azure Storage 계정에서 설정할 수 있습니다.
- [ngrok](https://ngrok.com/)를 다운로드하여 Azure에서 로컬 함수를 호출하도록 허용

## <a name="create-a-new-function"></a>새 함수 만들기

Visual Studio에서 함수 앱을 열고 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후, **추가 > 새 Azure 함수** 를 클릭합니다.

*새 Azure 함수* 창에서 **Event Grid 트리거** 를 선택하고 **확인** 을 클릭합니다.

![새 함수 만들기](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

함수가 만들어지면 코드 파일을 열고 파일 맨 위에서 주석 처리된 URL을 복사합니다. 이 위치는 Event Grid 트리거를 구성할 때 사용됩니다.

![복사 위치](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

그런 다음, `log.LogInformation`으로 시작하는 줄에 중단점을 설정합니다.

![중단점 설정](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


다음으로 **F5 키를 눌러** 디버깅 세션을 시작합니다.

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>함수 디버그

Event Grid가 스토리지 컨테이너에 새 파일이 업로드된 것을 인식하면 로컬 함수에서 중단점이 적중됩니다.

![ngrok 시작](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스를 정리하려면 스토리지 계정에서 **test** 컨테이너를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions의 Event Grid 트리거](./functions-bindings-event-grid.md)