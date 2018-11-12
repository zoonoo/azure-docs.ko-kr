---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262610"
---
## <a name="test"></a>함수 테스트

cURL을 사용하여 Mac 또는 Linux 컴퓨터나 Windows의 Bash를 사용하여 배포된 함수를 테스트합니다. 다음 cURL 명령을 실행하고 `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꿉니다. 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![브라우저에 표시된 함수 응답.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

명령줄에 사용할 수 있는 cURL이 없는 경우 웹 브라우저의 주소에 동일한 URL을 입력하면 됩니다. 다시, `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꾸고 쿼리 문자열 `&name=<yourname>`을 URL에 추가하고 요청을 실행합니다.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![브라우저에 표시된 함수 응답.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
