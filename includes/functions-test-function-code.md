---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148074"
---
## <a name="test"></a>함수 테스트

cURL을 사용하여 Mac 또는 Linux 컴퓨터에 배포된 함수를 테스트하거나 Powershell을 사용하여 Windows에 배포된 함수를 테스트합니다. 다음 cURL 명령을 실행하고 `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꿉니다. 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![브라우저에 표시된 함수 응답.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

명령줄에서 `cURL` 또는 `Invoke-WebRequest`를 사용할 수 없는 경우 웹 브라우저의 주소에 동일한 URL을 입력합니다. 다시, `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꾸고 쿼리 문자열 `&name=<yourname>`을 URL에 추가하고 요청을 실행합니다.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![브라우저에 표시된 함수 응답.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
