---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279453"
---
## <a name="test"></a>Azure에서 함수 확인

웹 브라우저를 사용하여 배포된 함수를 확인할 수 있습니다.  함수 키를 포함한 URL을 웹 브라우저의 주소 표시줄에 복사합니다. 요청을 실행하기 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

![웹 브라우저를 사용하여 함수를 호출합니다.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

또는 cURL을 사용하여 배포된 함수를 확인할 수 있습니다. 이전 단계에서 복사한 함수 키를 포함한 URL을 사용하여 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

![cURL을 사용하여 Azure에서 함수를 호출합니다.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

