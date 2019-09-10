---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170862"
---
## <a name="test"></a>Azure에서 함수 테스트

cURL을 사용하여 배포된 함수를 테스트합니다. 이전 단계에서 복사한 함수 키를 포함한 URL을 사용하여 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

![cURL을 사용하여 Azure에서 함수를 호출합니다.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

함수 키를 포함하여 복사한 URL을 웹 브라우저의 주소에 붙여넣을 수도 있습니다. 다시 한 번 URL에 쿼리 문자열 `&name=<yourname>`을 추가한 후 요청을 실행합니다.

![웹 브라우저를 사용하여 함수를 호출합니다.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
