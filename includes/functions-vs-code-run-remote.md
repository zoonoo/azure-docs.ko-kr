---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493516"
---
## <a name="run-the-function-in-azure"></a>Azure에서 함수 실행

1. 사이드바의 **Azure: Functions** 영역으로 돌아가서 **로컬 프로젝트** > **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Windows)하거나 <kbd>Ctrl -</kbd>을 클릭(macOS)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code에서 Azure로 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  

1. 함수가 Azure에서 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다.
