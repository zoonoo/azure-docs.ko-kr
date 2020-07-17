---
title: Visual Studio Code Azure Stream Analytics 도구에서 IntelliSense
description: 이 문서에서는 Visual Studio Code에 대 한 Azure Stream Analytics 도구에서 IntelliSense 기능을 사용 하는 방법을 설명 합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: df6e7435d072cc88e82f3dc9a323a5b21145c432
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037259"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>Visual Studio Code Azure Stream Analytics 도구에서 IntelliSense

IntelliSense는 [VS Code에 대 한 Azure Stream Analytics 도구](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)에서 [Stream Analytics 쿼리 언어](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) 에 사용할 수 있습니다. IntelliSense는 목록 멤버, 매개 변수 정보, 요약 정보 및 단어 자동 완성 등 여러 기능을 포함하는 코드 완성 지원 기능입니다. IntelliSense 기능을 "코드 완성", "내용 지원" 및 "코드 힌트"와 같은 다른 이름으로 라고도 합니다.

![IntelliSense 데모](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense 기능

VS Code에 대 한 Stream Analytics 도구의 IntelliSense 기능은 언어 서비스를 기반으로 합니다. 언어 서비스는 소스 코드를 분석 하 고 언어 의미 체계에 따라 지능형 코드 완성 기능을 제공 합니다. 언어 서비스에서 가능한 완료를 알고 있는 경우 입력 하면 IntelliSense 제안이 팝업 됩니다. 계속 입력 하면 사용자가 입력 한 문자를 포함 하는 멤버만 포함 하도록 변수 및 메서드와 같은 멤버 목록이 필터링 됩니다. 또는 키를 누르면 `Tab` `Enter` IntelliSense는 사용자가 선택한 멤버를 삽입 합니다.

점 문자와 같은 트리거 문자를 입력 하 여 편집기 창에서 IntelliSense를 트리거할 수 있습니다 `.` .

![intellisense 자동 완성](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 제안 위젯은 CamelCase 필터링을 지원 합니다. 메서드 이름에 대문자 문자를 입력 하 여 제안을 제한할 수 있습니다. 예를 들어 "cra"는 "createApplication"을 신속 하 게 가져옵니다.

### <a name="types-of-completions"></a>완성 유형

IntelliSense for VS Code Stream Analytics 도구는 언어 서버 제안, 코드 조각 및 간단한 단어 기반 텍스트 완성을 포함 하 여 다양 한 유형의 완성 기능을 제공 합니다.

|Completion     |  형식       |
| ----- | ------- |
| 키워드 | `keyword`
| Functions | `build-in function`, `user defined function`  |
| 데이터 집합 이름| `input`, `output`, `intermediate result set`|
| 데이터 집합 열 이름|`input`, `intermediate result set`|

#### <a name="name-completion"></a>이름 완성

키워드 자동 완성 외에도 VS Code에 대 한 Stream Analytics 도구는 작업 입력 및 출력 이름 목록과 구성 된 데이터 원본의 열 이름을 읽습니다. 확장은 몇 가지 키 입력으로 문을 입력 하는 데 유용한 이름 완성 기능을 제공 하기 위해이 정보를 기억 합니다.

코딩 하는 동안 작업 입력 이름, 출력 이름 및 열 이름에 대 한 검색을 수행 하기 위해 편집기에서 나갈 필요가 없습니다. 컨텍스트를 유지 하 고 필요한 정보를 찾고, 코드에 직접 요소를 삽입 하 고, IntelliSense에서 사용자의 입력을 완료할 수 있습니다.

이름 완성 기능을 사용할 수 있도록 로컬 입력 또는 라이브 입력을 구성 하 고 구성 파일을 저장 해야 합니다.

![이름 완성](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>매개 변수 정보

IntelliSense **매개 변수 정보** 옵션은 함수에 필요한 매개 변수의 개수, 이름 및 형식에 대 한 정보를 제공 하는 매개 변수 목록을 엽니다. 굵게 표시 된 매개 변수는 함수를 입력할 때 필요한 다음 매개 변수를 나타냅니다.

또한 중첩 함수에 대한 매개 변수 목록이 표시됩니다. 함수를 다른 함수에 대한 매개 변수로 입력할 경우 내부 함수에 대한 매개 변수가 매개 변수 목록에 표시됩니다. 그런 다음 내부 함수 매개 변수 목록이 완료되면 매개 변수 목록은 외부 함수 매개 변수를 표시하도록 원래대로 바뀝니다.

![매개 변수 정보](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>요약 정보

언어 서비스에서 제공 하는 것 처럼 코드에서 각 식별자에 대 한 **요약 정보** 를 볼 수 있습니다. 식별자의 예로는 입력, 출력, 중간 결과 집합 또는 함수가 있습니다. 마우스 포인터를 식별자 위로 이동 하면 해당 선언이 팝업 창에 표시 됩니다. 입력 (구성 된 경우) 및 중간 데이터 집합에 대 한 속성 및 데이터 스키마가 표시 됩니다.

![요약 정보](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense 문제 해결

이 문제는 데이터를 제공 하는 입력 구성이 누락 되었기 때문에 발생 합니다. [로컬 입력](visual-studio-code-local-run.md#define-a-local-input) 또는 [라이브 입력](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) 이 올바르게 구성 되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용 하 여 라이브 스트림 입력에 대해 로컬로 쿼리 Stream Analytics 테스트](visual-studio-code-local-run-live-input.md)
