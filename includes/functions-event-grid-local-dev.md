---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f2dcb92d0a600c57d96348413704863285cea3f0
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987197"
---
## <a name="allow-azure-to-call-your-local-function"></a>Azure에서 로컬 함수를 호출하도록 허용

컴퓨터에서 디버깅 중인 함수를 중단하려면 Azure가 클라우드에서 로컬 함수와 통신하는 방법을 설정해야 합니다.

[ngrok](https://ngrok.com/) 유틸리티는 Azure가 컴퓨터에서 실행되는 함수를 호출하는 방법을 제공합니다. 다음 명령을 사용하여 *ngrok* 를 시작합니다.

```bash
ngrok http -host-header=localhost 7071
```
유틸리티가 설정되면 명령 창은 다음 스크린샷과 비슷하게 표시됩니다.

![“ngrok” 유틸리티를 시작한 후에 명령 프롬프트를 보여주는 스크린샷.](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-ngrok.png)

*ngrok* 가 실행될 때 생성된 **HTTPS** URL을 복사합니다. 이 값은 Event Grid 이벤트 엔드포인트를 구성할 때 사용됩니다.

## <a name="add-a-storage-event"></a>스토리지 이벤트 추가

1. Azure Portal을 열고 스토리지 계정으로 이동한 다음 왼쪽 메뉴에서 **이벤트** 옵션을 선택합니다.

    ![스토리지 계정 이벤트 추가](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-add-event.png)

1. *이벤트* 창에서 **이벤트 구독** 단추를 선택합니다. 

1. *이벤트 구독* 창에서 *엔드포인트 형식* 드롭다운을 클릭하고 **웹후크** 를 선택합니다.

    ![구독 유형 선택](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-type.png)

1. 엔드포인트 유형이 구성되면 **엔드포인트 선택** 을 클릭하여 엔드포인트 값을 구성합니다.

    ![엔드포인트 유형 선택](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint.png)

    *구독자 엔드포인트* 값은 세 가지 다른 값으로 구성됩니다. 접두사는 *ngrok* 에서 생성되는 HTTPS URL입니다. URL의 나머지 부분은 가이드의 앞부분에 복사한 localhost URL에서 가져오며 함수 이름이 끝에 추가됩니다. localhost URL부터 *ngrok* URL이 `http://localhost:7071`을 대체하고 함수 이름이 `{functionname}`을 대체합니다.

1. 다음 스크린샷은 `Event Grid` 트리거 유형을 사용할 때 최종 URL이 어떻게 표시되어야 하는지에 대한 예를 보여 줍니다.

    ![엔드포인트 선택](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint-selection.png)

1. 적절한 값을 입력한 후 **선택 확인** 을 클릭합니다.

> [!IMPORTANT]
> *ngrok* 를 시작할 때마다 HTTPS URL이 다시 생성되고 값이 변경됩니다. 따라서 *ngrok* 를 통해 함수를 Azure에 노출할 때마다 새 이벤트 구독을 만들어야 합니다.

## <a name="upload-a-file"></a>파일 업로드

이제 로컬 함수에서 처리할 Event Grid 이벤트를 트리거하기 위해 스토리지 계정에 파일을 업로드할 수 있습니다. 

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 열고 스토리지 계정에 연결합니다. 

- **BLOB 컨테이너** 확장 
- 마우스 오른쪽 단추를 클릭하고 **BLOB 컨테이너 만들기** 선택
- 컨테이너 이름을 **samples-workitems** 로 지정합니다.
- *samples-workitems* 컨테이너를 선택합니다.
- **업로드** 단추 클릭
- **파일 업로드** 클릭
- 파일을 선택하고 BLOB 컨테이너에 업로드

