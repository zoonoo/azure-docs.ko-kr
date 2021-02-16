---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809571"
---
## <a name="run-the-function-in-azure"></a>Azure에서 함수 실행

1. 사이드바의 **Azure: Functions** 영역으로 돌아가서 구독, 새 함수 앱 및 **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Windows)하거나 <kbd>Ctrl -</kbd>을 클릭(macOS)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code에서 Azure로 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  

1. 함수가 Azure에서 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다.
