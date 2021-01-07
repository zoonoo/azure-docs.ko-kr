---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056569"
---
1. 함수를 실행하려면 Visual Studio에서 F5를 누릅니다. 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다. 함수를 로컬로 실행하는 경우 권한 부여 수준이 적용되지 않습니다.

2. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![Azure 로컬 런타임](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. `?name=<YOUR_NAME>` 쿼리 문자열을 이 URL에 추가하고 요청을 실행합니다. 다음 이미지에서는 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다. 

    ![브라우저의 localhost 함수 응답](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 디버깅을 중지하려면 Visual Studio에서 Shift+F5를 누릅니다.