---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424928"
---
## <a name="invoke-the-function-on-azure"></a>Azure에서 함수 호출

함수에서 HTTP 트리거를 사용하므로 브라우저 또는 도구(예: curl)를 사용하여 URL에 대한 HTTP 요청을 수행하여 호출합니다. 

# <a name="browser"></a>[브라우저](#tab/browser)

publish 명령의 출력에 표시된 **호출 URL** 전체를 브라우저 주소 표시줄에 복사하여 `&name=Functions` 쿼리 매개 변수를 추가합니다. 브라우저에서 함수를 로컬로 실행했을 때와 비슷한 출력이 표시됩니다.

![브라우저에서 보여 주는 Azure에서 실행되는 함수의 출력](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

**호출 URL** 을 사용하고 `&name=Functions` 매개 변수를 추가하여 [`curl`](https://curl.haxx.se/)을 실행합니다. 명령의 출력은 "Hello Functions" 텍스트여야 합니다.

![curl을 사용한 Azure에서 실행되는 함수의 출력](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
