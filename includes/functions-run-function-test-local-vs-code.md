---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842399"
---
## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio Code는 [Azure Functions Core 도구](../articles/azure-functions/functions-run-local.md)와 통합되어 Azure에 게시하기 전에 로컬 개발 컴퓨터에서 이 프로젝트를 실행할 수 있습니다.

1. 함수를 호출하려면 <kbd>F5</kbd>를 눌러 함수 앱 프로젝트를 시작합니다. 핵심 도구의 출력이 **터미널** 패널에 표시됩니다. Windows에서 실행하는 데 문제가 있는 경우 Visual Studio Code의 기본 터미널이 **WSL Bash** 로 설정되어 있지 않은지 확인합니다.

1. Azure Functions Core Tools를 아직 설치하지 않은 경우 프롬프트에서 **설치** 를 선택합니다. 핵심 도구가 설치되면 **터미널** 패널에서 앱이 시작됩니다. 로컬에서 실행 중인 HTTP 트리거 함수의 URL 엔드포인트를 볼 수 있습니다.

    ![로컬 함수 VS Code 출력](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Core Tools를 실행 중인 상태에서 다음 URL로 이동하여 `?name=Functions` 쿼리 문자열이 포함된 GET 요청을 실행합니다.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. 브라우저에서 다음과 같이 표시되는 응답이 반환됩니다.

    ![Browser - localhost 예제 출력](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 요청에 대한 정보는 **터미널** 패널에 표시됩니다.

    ![작업 호스트 시작 - VS Code 터미널 출력](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. <kbd>Ctrl + C</kbd>를 눌러 Core Tools를 중지하고 디버거 연결을 끊습니다.
