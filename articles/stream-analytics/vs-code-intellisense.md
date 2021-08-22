---
title: Visual Studio Code용 Azure Stream Analytics 도구의 IntelliSense
description: 이 문서에서는 Visual Studio Code용 Azure Stream Analytics 도구에서 IntelliSense 기능을 사용하는 방법을 설명합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 0e022023ba9ceefd8426280b07d62b78a79d83e5
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913678"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>Visual Studio Code용 Azure Stream Analytics 도구의 IntelliSense

IntelliSense는 [VS Code용 Azure Stream Analytics 도구](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)의 [Stream Analytics 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference?toc=/azure/stream-analytics/toc.json)에 사용할 수 있습니다. IntelliSense는 다음과 같은 다양한 기능을 포함하는 코드 완성 지원 도구입니다. 멤버 목록, 매개 변수 정보, 요약 정보 및 전체 단어. IntelliSense 기능을 “코드 완성”, “콘텐츠 지원” 및 “코드 힌트”라고도 합니다.

![IntelliSense 데모](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense 기능

VS Code용 Stream Analytics 도구의 IntelliSense 기능은 언어 서비스를 기반으로 합니다. 언어 서비스는 소스 코드를 분석하고 언어 의미 체계를 기반으로 인텔리전트 코드 완성을 제공합니다. 언어 서비스가 가능한 완성을 알고 있으면 사용자가 입력할 때 IntelliSense 제안이 표시됩니다. 계속 입력하면 변수, 메서드 등의 멤버 목록은 사용자가 입력한 문자를 포함하는 멤버만 포함하도록 필터링됩니다. `Tab` 또는 `Enter` 키를 누르면 IntelliSense는 사용자가 선택한 멤버를 삽입합니다.

점 문자 `.` 같은 트리거 문자를 입력하여 편집기 창에서 IntelliSense를 트리거할 수 있습니다.

![Intellisense 자동 완성](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 제안 위젯은 카멜 대/소문자 필터링을 지원합니다. 메서드 이름에 대문자로 문자를 입력하여 제안을 제한할 수 있습니다. 예를 들어, “cra”는 “createApplication”을 빠르게 표시합니다.

### <a name="types-of-completions"></a>완성 유형

VS Code용 Stream Analytics 도구의 IntelliSense는 언어 서버 제안, 코드 조각, 간단한 단어 기반 텍스트 완성을 포함하여 다양한 유형의 완성을 제공합니다.

|Completion     |  유형       |
| ----- | ------- |
| 키워드 | `keyword`
| Functions | `build-in function`, `user defined function`  |
| 데이터 세트 이름| `input`, `output`, `intermediate result set`|
| 데이터 세트 열 이름|`input`, `intermediate result set`|

#### <a name="name-completion"></a>이름 완성

키워드 자동 완성 외에도 VS Code용 Stream Analytics 도구는 작업 입력 및 출력 이름 목록과 구성된 경우 데이터 원본의 열 이름을 읽습니다. 확장은 이 정보를 기억하여 몇 가지 키 입력으로 문을 입력하는 데 유용한 이름 완성 기능을 제공합니다.

코딩하는 동안 작업 입력 이름, 출력 이름, 열 이름에서 검색을 수행하기 위해 편집기를 벗어나지 않아도 됩니다. 컨텍스트를 유지하고, 필요한 정보를 찾고, 요소를 코드에 직접 삽입하고, IntelliSense가 사용자를 대신하여 입력을 완성하도록 할 수 있습니다.

이름 완성을 사용할 수 있으려면 로컬 입력이나 라이브 입력을 구성하고 구성 파일을 저장해야 합니다.

![이름 완성](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>매개 변수 정보

IntelliSense **매개 변수 정보** 옵션은 함수에 필요한 매개 변수의 개수, 이름, 유형에 관한 정보를 제공하는 매개 변수 목록을 엽니다. 굵게 표시된 매개 변수는 함수를 입력할 때 필요한 다음 매개 변수를 나타냅니다.

또한 중첩 함수에 대한 매개 변수 목록이 표시됩니다. 함수를 다른 함수에 대한 매개 변수로 입력할 경우 내부 함수에 대한 매개 변수가 매개 변수 목록에 표시됩니다. 그런 다음 내부 함수 매개 변수 목록이 완료되면 매개 변수 목록은 외부 함수 매개 변수를 표시하도록 원래대로 바뀝니다.

![매개 변수 정보](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>요약 정보

언어 서비스에서 제공하는 것처럼 코드에서 각 식별자의 **빠른 정보** 를 볼 수 있습니다. 식별자의 몇 가지 예로는 입력, 출력, 중간 결과 집합 또는 함수가 있습니다. 마우스 포인터를 식별자 위로 이동하면 노란색 팝업 창에 해당 선언이 표시됩니다. 입력의 속성과 데이터 스키마(구성된 경우) 및 중간 데이터 세트가 표시됩니다.

![요약 정보](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense 문제 해결

이 문제는 데이터를 제공하는 입력 구성이 누락되어 발생합니다. [로컬 입력](visual-studio-code-local-run.md#define-a-local-input) 또는 [라이브 입력](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)이 올바르게 구성되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용하여 라이브 스트림 입력에 대해 로컬로 Azure Stream Analytics 쿼리 테스트](visual-studio-code-local-run-live-input.md)