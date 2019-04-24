---
title: Azure Functions Event Grid의 로컬 디버깅
description: Event Grid 이벤트에 의해 트리거되는 Azure Functions를 로컬로 디버그하는 방법 알아보기
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 함수, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 96d88fafd6824ed85f1d91bab59374b3490a55b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60428264"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure Function Event Grid 트리거 로컬 디버깅

이 문서에서는 저장소 계정에서 발생하는 Azure Event Grid 이벤트를 처리하는 로컬 함수를 디버그하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

- 기존 함수 앱 만들기 또는 사용
- 기존 저장소 계정 만들기 또는 사용
- [ngrok](https://ngrok.com/)를 다운로드하여 Azure에서 로컬 함수를 호출하도록 허용

## <a name="create-a-new-function"></a>새 함수 만들기

Visual Studio에서 함수 앱을 열고 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후, **추가 > 새 Azure 함수**를 클릭합니다.

*새 Azure 함수* 창에서 **Event Grid 트리거**를 선택하고 **확인**을 클릭합니다.

![새 함수 만들기](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

함수가 만들어지면 코드 파일을 열고 파일 맨 위에서 주석 처리된 URL을 복사합니다. 이 위치는 Event Grid 트리거를 구성할 때 사용됩니다.

![복사 위치](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

그런 다음, `log.LogInformation`으로 시작하는 줄에 중단점을 설정합니다.

![중단점 설정](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


다음으로 **F5 키를 눌러** 디버깅 세션을 시작합니다.

## <a name="allow-azure-to-call-your-local-function"></a>Azure에서 로컬 함수를 호출하도록 허용

컴퓨터에서 디버깅 중인 함수를 중단하려면 Azure가 클라우드에서 로컬 함수와 통신하는 방법을 설정해야 합니다.

[ngrok](https://ngrok.com/) 유틸리티는 Azure가 컴퓨터에서 실행되는 함수를 호출하는 방법을 제공합니다. 다음 명령을 사용하여 *ngrok*를 시작합니다.

```bash
ngrok http -host-header=localhost 7071
```
유틸리티가 설정되면 명령 창은 다음 스크린샷과 비슷하게 표시됩니다.

![ngrok 시작](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

*ngrok*가 실행될 때 생성된 **HTTPS** URL을 복사합니다. 이 값은 Event Grid 이벤트 엔드포인트를 구성할 때 사용됩니다.

## <a name="add-a-storage-event"></a>저장소 이벤트 추가

Azure Portal을 열고 저장소 계정으로 이동한 후 **이벤트** 옵션을 클릭합니다.

![저장소 계정 이벤트 추가](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

*이벤트* 창에서 **이벤트 구독** 단추를 클릭합니다. *이벤트 구독* 창에서 *엔드포인트 유형* 드롭다운을 클릭하고 **웹후크**를 선택합니다.

![구독 유형 선택](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

엔드포인트 유형이 구성되면 **엔드포인트 선택**을 클릭하여 엔드포인트 값을 구성합니다.

![엔드포인트 유형 선택](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

*구독자 엔드포인트* 값은 세 가지 다른 값으로 구성됩니다. 접두사는 *ngrok*에서 생성되는 HTTPS URL입니다. URL의 나머지는 함수 코드 파일에 있는 URL과 끝에 파일 이름을 추가하여 생성됩니다. 함수 코드 파일의 URL에서 시작하여, `http://localhost:7071` 대신 *ngrok* URL이 사용되고, `{functionname}` 대신 함수 이름이 사용됩니다.

다음 스크린샷에서는 마지막 URL 모습을 보여 줍니다.

![엔드포인트 선택](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

적절한 값을 입력한 후 **선택 확인**을 클릭합니다.

> [!IMPORTANT]
> *ngrok*를 시작할 때마다 HTTPS URL이 다시 생성되고 값이 변경됩니다. 따라서 *ngrok*를 통해 함수를 Azure에 노출할 때마다 새 이벤트 구독을 만들어야 합니다.

## <a name="upload-a-file"></a>파일 업로드

이제 로컬 함수에서 처리할 Event Grid 이벤트를 트리거하기 위해 저장소 계정에 파일을 업로드할 수 있습니다. 

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 열고 스토리지 계정에 연결합니다. 

- **BLOB 컨테이너** 확장 
- 마우스 오른쪽 단추를 클릭하고 **BLOB 컨테이너 만들기** 선택
- 컨테이너 이름을 **test**로 지정
- *test* 컨테이너 선택
- **업로드** 단추 클릭
- **파일 업로드** 클릭
- 파일을 선택하고 BLOB 컨테이너에 업로드

## <a name="debug-the-function"></a>함수 디버그

Event Grid가 저장소 컨테이너에 새 파일이 업로드된 것을 인식하면 로컬 함수에서 중단점이 적중됩니다.

![ngrok 시작](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스를 정리하려면 저장소 계정에서 **test** 컨테이너를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions의 Event Grid 트리거](./functions-bindings-event-grid.md)
