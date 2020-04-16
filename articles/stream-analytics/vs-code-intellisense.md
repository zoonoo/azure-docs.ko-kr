---
title: 비주얼 스튜디오 코드에 대 한 Azure 스트림 분석 도구에서 IntelliSense
description: 이 문서에서는 Visual Studio 코드에 대한 Azure 스트림 분석 도구에서 IntelliSense 기능을 사용하는 방법에 대해 설명합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394398"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>비주얼 스튜디오 코드에 대 한 Azure 스트림 분석 도구에서 IntelliSense

INtelliSense는 [VS 코드에 대한 Azure 스트림 분석 도구에서](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) [스트림 분석 쿼리 언어에](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) 사용할 수 있습니다. IntelliSense는 목록 멤버, 매개 변수 정보, 요약 정보 및 단어 자동 완성 등 여러 기능을 포함하는 코드 완성 지원 기능입니다. IntelliSense 기능은 "코드 완성", "콘텐츠 지원"및 "코드 힌트"와 같은 다른 이름으로 호출됩니다.

![인텔리센스 데모](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense 기능

VS 코드에 대한 스트림 분석 도구의 IntelliSense 기능은 언어 서비스에 의해 구동됩니다. 언어 서비스는 소스 코드를 분석하고 언어 의미 체계를 기반으로 지능형 코드 완성을 제공합니다. 언어 서비스가 가능한 완료를 알고 있는 경우 입력할 때 IntelliSense 제안이 나타납니다. 입력을 계속하면 변수 및 메서드와 같은 멤버 목록이 필터링되어 입력한 문자가 포함된 멤버만 포함하도록 필터링됩니다. 또는 `Enter` 키를 `Tab` 누르면 IntelliSense가 선택한 멤버를 삽입합니다.

도트 문자와 `.`같은 트리거 문자를 입력하여 모든 편집기 창에서 IntelliSense를 트리거할 수 있습니다.

![인텔리센스 자동 완성](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 제안 위젯은 낙타 케이스 필터링을 지원합니다. 메서드 이름에 대문자로 된 문자를 입력하여 제안을 제한할 수 있습니다. 예를 들어"cra"는 "createApplication"을 빠르게 불러올 것입니다.

### <a name="types-of-completions"></a>완료 유형

VS 코드 IntelliSense에 대한 스트림 분석 도구는 언어 서버 제안, 조각 및 간단한 단어 기반 텍스트 완료를 포함하여 다양한 유형의 완료를 제공합니다.

|       |         |       |
| ----- | ------- | ----- |
| 키워드 | `keyword`
| Functions | `build-in function`, `user defined function`  |
| 데이터 집합 이름| `input`, `output`, `intermediate result set`|
| 데이터 집합 열 이름|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>이름 완성

VS 코드용 스트림 분석 도구는 키워드 자동 완성 외에도 작업 입력 및 출력 이름 목록과 구성 시 데이터 원본의 열 이름을 읽습니다. 확장은 키 입력이 거의 없는 문을 입력하는 데 유용한 이름 완성 기능을 제공하기 위해 이 정보를 기억합니다.

코딩하는 동안 작업 입력 이름, 출력 이름 및 열 이름에 대한 검색을 수행하기 위해 편집기를 떠날 필요가 없습니다. 컨텍스트를 유지하고, 필요한 정보를 찾고, 코드에 직접 요소를 삽입하고, IntelliSense에서 입력을 완료할 수 있습니다.

이름 완성을 사용할 수 있도록 로컬 입력 또는 라이브 입력을 구성하고 구성 파일을 저장해야 합니다.

![이름 완성](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>매개 변수 정보

IntelliSense **매개 변수 정보** 옵션은 함수에 필요한 매개 변수의 수, 이름 및 유형에 대한 정보를 제공하는 매개 변수 목록을 엽니다. 굵게 표시된 매개 변수는 함수를 입력할 때 필요한 다음 매개 변수를 나타냅니다.

또한 중첩 함수에 대한 매개 변수 목록이 표시됩니다. 함수를 다른 함수에 대한 매개 변수로 입력할 경우 내부 함수에 대한 매개 변수가 매개 변수 목록에 표시됩니다. 그런 다음 내부 함수 매개 변수 목록이 완료되면 매개 변수 목록은 외부 함수 매개 변수를 표시하도록 원래대로 바뀝니다.

![매개 변수 정보](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>요약 정보

언어 서비스에서 제공하는 대로 코드의 각 식별자에 대한 **빠른 정보를** 볼 수 있습니다. 식별자의 몇 가지 예는 입력, 출력, 중간 결과 집합 또는 함수입니다. 식별자 위로 마우스 포인터를 이동하면 해당 선언이 팝업 창에 표시됩니다. 구성된 경우 입력에 대한 속성 및 데이터 스키마와 중간 데이터 집합이 표시됩니다.

![빠른 정보](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense 문제 해결

이 문제는 데이터를 제공하는 입력 구성이 누락되어 발생합니다. [로컬 입력](visual-studio-code-local-run.md#define-a-local-input) 또는 라이브 [입력이](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) 올바르게 구성되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 시각적 스튜디오 코드에서 Azure 스트림 분석 작업 만들기](quick-create-vs-code.md)
* [Visual Studio 코드를 사용하여 샘플 데이터로 로컬에서 테스트 스트림 분석 쿼리](visual-studio-code-local-run.md)
* [Visual Studio 코드를 사용하여 실시간 스트림 입력에 대해 로컬로 스트림 분석 쿼리 테스트](visual-studio-code-local-run-live-input.md)
