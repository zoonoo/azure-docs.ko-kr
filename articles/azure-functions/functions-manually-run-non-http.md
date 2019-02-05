---
title: HTTP 이외 트리거 Azure Functions를 수동으로 실행
description: HTTP 요청을 사용하여 HTTP 이외 트리거 Azure Functions 실행
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 61bece83697a4907a7bf3c881003f4da9b0e8a84
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466891"
---
# <a name="manually-run-a-non-http-triggered-function"></a>HTTP 이외 트리거 함수를 수동으로 실행

이 문서에서는 특별한 형식의 HTTP 요청을 통해 HTTP 이외 트리거 함수를 수동으로 실행하는 방법에 대해 설명합니다.

일부 컨텍스트에서 간접적으로 트리거되는 "주문형" Azure Function을 실행해야 합니다.  간접 트리거의 예제에는 [일정에 따른 함수](./functions-create-scheduled-function.md)나 [다른 리소스의 작업](./functions-create-storage-blob-triggered-function.md) 결과로 실행되는 함수가 포함됩니다. 

다음 예제에서는 [Postman](https://www.getpostman.com/)을 사용할 수 있지만 [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) 또는 HTTP 요청을 전송하는 도구와 같은 기타 기능을 사용할 수도 있습니다.

## <a name="define-the-request-location"></a>요청 위치 정의

HTTP 이외 트리거 함수를 실행하려면 Azure에 함수를 실행하는 요청을 보낼 방법이 필요합니다. 이 요청에 사용된 URL은 특정 형식을 사용합니다.

![요청 위치를 정의합니다. 함수 이름 + 폴더 경로 + 호스트 이름](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **호스트 이름:** 함수 앱의 공용 위치는 함수 앱의 이름 및 *azurewebsites.net*이나 사용자 지정 도메인으로 구성됩니다.
- **폴더 경로:** HTTP 요청을 통해 HTTP 이외 트리거 함수에 액세스하려면 *admin/functions* 폴더를 통해 요청을 보내야 합니다.
- **함수 이름:** 실행하려는 함수의 이름입니다.

Azure에 대한 요청에서 함수의 마스터 키와 함께 Postman의 요청 위치를 사용하여 함수를 실행합니다.

> [!NOTE]
> 로컬에서 실행하는 경우 함수의 마스터 키가 필요하지 않습니다. `x-functions-key` 헤더를 생략하고 직접 [함수를 호출](#call-the-function)할 수 있습니다.

## <a name="get-the-functions-master-key"></a>함수의 마스터 키 가져오기

Azure Portal에서 해당 함수로 이동하고 **관리**를 클릭하고 **호스트 키** 섹션을 찾습니다. *_master* 행에서 **복사** 단추를 클릭하여 마스터 키를 클립보드에 복사합니다.

![함수 관리 화면에서 마스터 키 복사](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

마스터 키를 복사한 후에 코드 파일 창으로 돌아가려면 함수 이름을 클릭합니다. 다음으로, **로그** 탭을 클릭합니다. Postman에서 함수를 수동으로 실행하는 경우 여기에 기록된 함수의 메시지가 표시됩니다.

> [!CAUTION]  
> 함수 앱에서는 마스터 키를 통해 높은 권한이 부여되므로, 이 키를 제3자와 공유하거나 애플리케이션에 배포해서는 안 됩니다.

## <a name="call-the-function"></a>함수 호출

Postman을 열고 다음 단계를 수행합니다.

1. **URL 텍스트 상자에서 요청 위치**를 입력합니다.
2. HTTP 메서드가 **POST**.로 설정되었는지 확인합니다.
3. **헤더** 탭을 **클릭**합니다.
4. **x-functions-key**를 첫 번째 **키**로 입력하고 클립보드의 마스터 키를 **값** 상자에 붙여넣습니다.
5. **Content-type**을 두 번째 **키**로 입력하고 **application/json**을 **값**으로 입력합니다.

    ![Postman 헤더 설정](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **본문** 탭을 **클릭**합니다.
7. **{ "input": "test" }** 를 요청의 본문으로 입력합니다.

    ![Postman 본문 설정](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. **보내기**를 클릭합니다.

    ![Postman을 사용하여 요청 보내기](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

그런 다음, Postman은 **202 수락됨**이라는 상태를 보고합니다.

다음으로, Azure Portal에서 해당 함수로 돌아갑니다. *로그* 창을 찾으면 함수에 대한 수동 호출에서 들어오는 메시지가 표시됩니다.

![수동 호출의 함수 로그 결과](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>다음 단계

- [Azure Functions에서 코드를 테스트하기 위한 전략](./functions-test-a-function.md)
- [Azure Function Event Grid 트리거 로컬 디버깅](./functions-debug-event-grid-trigger-local.md)
