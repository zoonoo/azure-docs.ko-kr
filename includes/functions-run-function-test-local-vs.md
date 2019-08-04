---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592875"
---
1. 함수를 실행하려면 **F5**를 누릅니다. 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 합니다. 로컬에서 실행하는 경우 권한 부여 수준은 적용되지 않습니다.

2. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![Azure 로컬 런타임](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. 이 URL에 쿼리 문자열 `?name=<YOUR_NAME>`을 추가하고 요청을 실행합니다. 다음은 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다. 

    ![브라우저의 localhost 함수 응답](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 디버깅을 중지하려면 **Shift + F5**를 누릅니다.