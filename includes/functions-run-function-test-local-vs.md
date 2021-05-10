---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701411"
---
1. 함수를 실행하려면 Visual Studio에서 <kbd>F5</kbd>를 누릅니다. 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다. 함수를 로컬로 실행하는 경우 권한 부여 수준이 적용되지 않습니다.

2. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![Azure 로컬 런타임](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. 브라우저의 주소 표시줄에 HTTP 요청에 대한 URL을 붙여 넣습니다. `?name=<YOUR_NAME>` 쿼리 문자열을 이 URL에 추가하고 요청을 실행합니다. 다음 이미지에서는 함수가 반환한 로컬 GET 요청에 대한 응답을 브라우저에 보여 줍니다. 

    ![브라우저의 localhost 함수 응답](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 디버깅을 중지하려면 Visual Studio에서 <kbd>Shift</kbd>+<kbd>F5</kbd>를 누릅니다.
