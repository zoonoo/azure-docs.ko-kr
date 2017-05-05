---
title: "Azure Portal에서 첫 번째 함수 만들기 | Microsoft Docs"
description: "Azure를 시작합니다. Azure Portal에서 첫 번째 Azure Function을 만듭니다."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/10/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7542280ca6dbe1a8d110155e521228d675c0d994
ms.lasthandoff: 04/12/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal에서 첫 번째 Azure Function을 만듭니다.

이 토픽에서는 Azure Functions를 사용하여 HTTP 요청에서 호출되는 간단한 "hello world" 함수를 만드는 방법을 보여 줍니다. Azure Portal에서 함수를 만들려면 먼저 서버를 사용하지 않는 함수 실행을 호스트하는 함수 앱을 만들어야 합니다.

이 빠른 시작을 완료하려면 Azure 계정이 있어야 합니다. [무료 계정](https://azure.microsoft.com/free/)을 사용할 수 있습니다. 또한 Azure에 등록하지 않고 [Azure Functions를 사용](https://azure.microsoft.com/try/app-service/functions/)할 수도 있습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

자세한 내용은 [Azure Portal에서 함수 앱 만들기](functions-create-function-app-portal.md)를 참조하세요.

## <a name="create-a-function"></a>함수 만들기
이 단계에서는 Azure Functions 빠른 시작을 사용하여 새로운 함수 앱에서 함수를 만듭니다.

1. **새로 만들기** 단추를 클릭하고 **웹후크 + API**를 클릭한 후 대한 언어를 선택하고 **함수 만들기**를 클릭합니다. 함수는 HTTP 트리거 함수 템플릿을 사용하여 선택한 언어로 만들어집니다.  
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

함수가 만들어진 후에는 HTTP 요청을 전송하여 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

함수 템플릿에는 작업 코드가 포함되므로 포털에서 바로 새 함수를 테스트할 수 있습니다.

1. 함수 앱에서 새 함수를 클릭하고 템플릿에서 코드를 검토합니다. 함수에는 *name* 값이 메시지 본문 또는 쿼리 문자열에 전달된 HTTP 요청이 필요합니다. 함수가 실행되면 이 값은 응답 메시지에 반환됩니다. 표시된 예제는 JavaScript 함수입니다.
   
2. **실행**을 클릭하여 함수를 실행합니다. 실행이 테스트 HTTP 요청에 의해 트리거되고 정보가 로그에 기록되며 "hello..." 응답이 **테스트** 탭의 **출력**에 표시되는지 확인합니다.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

3. **요청 본문** 텍스트 상자에서 *이름* 속성의 값을 자신의 이름으로 변경하고 **실행**을 다시 클릭합니다. 이번에는 **출력**의 응답에 사용자 이름이 포함됩니다.   

4. HTTP 테스트 도구 또는 다른 브라우저 창에서 동일한 함수의 실행을 트리거하려면 **</> 함수 URL 가져오기**를 클릭하고 요청 URL을 복사한 후 도구 또는 브라우저 주소 표시줄에 붙여 넣습니다. URL에 쿼리 문자열 값 `&name=yourname`을 추가하고 요청을 실행합니다. 동일한 정보가 로그에 기록되고 동일한 문자열이 응답 메시지의 본문에 포함됩니다.

    ![](./media/functions-create-first-azure-function/function-app-browser-testing.png)


## <a name="next-steps"></a>다음 단계
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


