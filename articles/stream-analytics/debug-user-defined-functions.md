---
title: Azure Stream Analytics의 사용자 정의 함수 디버그
description: 이 문서에서는 Azure Stream Analytics에서 사용자 정의 함수를 디버그하는 방법을 설명합니다.
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019434"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics의 사용자 정의 함수 디버그 

UDF(사용자 정의 함수)가 정상적으로 작동하지 않는 경우 문제를 해결하기 위해 디버그해야 합니다. [Visual Studio Code](visual-studio-code-local-run-live-input.md) 또는 [Visual Studio](stream-analytics-vs-tools-local-run.md)를 사용하여 작업을 로컬로 실행할 때 Stream Analytics 작업의 UDF를 디버그할 수 있습니다.

라이브 입력 스트림에 대해 작업을 로컬로 실행하는 경우 한 노드에서만 클라우드 Azure Stream Analytics 엔진 실행을 모방합니다. 라이브 데이터의 로컬 테스트는 클라우드에서 수행하는 성능 및 확장성 테스트를 대체할 수 없지만, 테스트할 때마다 작업을 클라우드에 제출할 필요가 없으므로 기능 테스트를 수행하는 시간을 절약할 수 있습니다. 또한 로컬 또는 샘플 데이터를 사용하는 로컬 실행에 대해서는 시간 정책이 사용하지 않도록 설정되지만 라이브 데이터 테스트의 경우에는 시간 정책이 지원됩니다.

## <a name="pick-your-language"></a>언어 선택

.NET(C#) 또는 JavaScript를 사용하여 Azure Stream Analytics에 대한 UDF를 작성할 수 있습니다. 

### <a name="functions-in-c"></a>C#의 함수 

[Visual Studio를 사용하여 .NET UDF를 작성](stream-analytics-edge-csharp-udf-methods.md)하는 경우 .NET 클래스 프로젝트의 경우와 동일한 수준의 지원을 얻을 수 있습니다. 이러한 지원이 다음을 포함합니다.

* 구문 검사 및 컴파일러 지원과 같은 컴파일 지원

* Stream Analytics 솔루션에서 C# 프로젝트 및 아티팩트를 추가, 빌드 및 참조하는 기능 

* 공유 가능한 프로젝트에서 캡슐화된 코드를 쉽게 다시 사용 가능 

* Visual Studio에서 직접 디버그 지원. Stream Analytics 프로젝트를 시작 프로젝트로 설정하고 C# 코드에서 중단점을 설정합니다. 그런 다음, **F5** 키를 눌러 다른 C# 프로젝트의 경우와 마찬가지로 C# 코드를 디버깅합니다. 

### <a name="functions-in-javascript"></a>JavaScript의 함수

JavaScript는 Stream Analytics에서 함수를 만들기 위한 또 다른 옵션입니다. JavaScript 코드는 Stream Analytics 프로젝트의 함수 영역에 직접 배치되므로 프로젝트 간에 공유하기가 더 어렵습니다.

컴파일은 Stream Analytics 프로젝트를 컴파일하거나 실행할 때 발생합니다. 런타임에만 문제가 나타날 가능성이 높습니다. Stream Analytics에 JavaScript 함수에 대한 직접적인 디버깅 지원은 없습니다.

## <a name="debug-options-for-javascript"></a>JavaScript에 대한 디버그 옵션

Stream Analytics에 JavaScript 함수에 대한 직접적인 디버깅 지원은 없으므로 HTML 사이트에서 함수를 캡슐화하고 출력을 가져와 디버깅할 수 있습니다.

다음 예제에서는 [Visual Studio Code](quick-create-visual-studio-code.md)의 통합 런타임 환경에서 몇 가지 기능이 제한된 상태에서 JavaScript UDF를 디버깅하는 방법을 보여 줍니다.

### <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 Azure Stream Analytics 프로젝트에 다음 항목이 있는지 확인합니다.

* 입력 
* 출력 
* Stream Analytics 쿼리(.asaql) 
* Stream Analytics 작업 구성(JobConfig.json)
* JavaScript UDF

### <a name="prepare-files"></a>파일 준비

다음 이미지에서 *.asaql* 쿼리 파일에는 UDF, *fxcharCount* 에 대한 호출만 포함됩니다. 이렇게 변경하면 변경된 후에도 프로젝트를 컴파일할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![Stream Analytics 테스트 쿼리 파일](./media/debug-user-defined-functions/asaql-file.png)

**Tests** 에 테스트 파일을 호스트할 추가 폴더를 만듭니다. 이 폴더는 JavaScript 함수를 사용하여 테스트를 실행하기 위해 호출됩니다. 이 예제에서 이 폴더의 이름은 *fxcharCount* 이고 테스트의 이름은 *Test_UDF.js* 입니다. 

아래 이미지는 함수 파일을 로드하고 함수를 실행하는 테스트 파일의 코드를 보여 줍니다. 이 예제는 간단하지만 추가 테스트 데이터 파일을 로드하고 추가 테스트를 반복하여 출력을 얻을 수 있습니다. 파일이 참조되고 런타임에 로드되지 않아 디버그할 수 있기 때문에 함수 호출의 표기법은 일반적인 호출과 약간 다릅니다. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics 테스트 파일](./media/debug-user-defined-functions/test-file.png)

함수에서 다음 코드 줄을 파일에 추가하여 메서드를 노출합니다. Visual Studio Code에서 코드를 컴파일하는 기능에는 영향을 주지 않습니다.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>디버그 지원 설치

디버그하려면 **node.js** 를 [다운로드](https://nodejs.org/en/download/)하고 설치해야 합니다. 사용 중인 플랫폼에 따라 올바른 버전을 설치합니다. node.js 런타임을 설치한 후 Visual Studio Code를 다시 시작하여 변경 내용을 구현합니다. 

**실행 및 디버그** 를 선택하거나 **Ctrl+Shift+D** 를 눌러 디버깅을 시작합니다. **node.js** 를 런타임으로 선택할 수 있는 콤보 상자가 표시됩니다. node.js만 설치되어 있는 경우에는 기본적으로 사용됩니다. F11 키를 사용하여 필요한 경우 코드를 위성 파일까지 단계별로 실행할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics 실행 및 디버그 UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>사용자 정의 집계 디버그 

JavaScript UDF에 대한 디버그 방법을 사용하여 UDA(사용자 정의 집계)를 디버깅할 수 있습니다. 이 예제에서는 *.asaql* 쿼리 파일과 테스트 파일에 UDA가 추가됩니다.

UDF와 마찬가지로 변경 작업을 수행한 후 프로젝트를 컴파일하도록 UDA에 대한 호출을 포함합니다. 

> [!div class="mx-imgBorder"]
> ![asaql에 uda 추가](./media/debug-user-defined-functions/asaql-uda.png)

*Test_UDA.js* 파일에서 UDF를 사용할 때처럼 UDA 파일을 참조합니다. 또한 `main()`, `init()` 및 `accumulate()`를 호출합니다. `accumulate()` 메서드가 상태 스택에 값을 넣는 루프에서 호출됩니다. `computeresult()` 메서드를 호출하여 최종 쿼리를 작성합니다. 

> [!div class="mx-imgBorder"]
> ![UDA 테스트 파일](./media/debug-user-defined-functions/uda-test.png)

UDF 예제와 같이 관련 메서드를 노출하려면 UDA 자체에 일부 코드를 추가해야 합니다.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![UDA에 추가된 코드](./media/debug-user-defined-functions/uda-expose-methods.png)

**실행 및 디버그** 를 선택하거나 **Ctrl+Shift+D** 를 눌러 디버깅을 시작합니다. **node.js** 를 런타임으로 선택할 수 있는 콤보 상자가 표시됩니다. node.js만 설치되어 있는 경우에는 기본적으로 사용됩니다. F11 키를 사용하여 필요한 경우 코드를 위성 파일까지 단계별로 실행할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![Stream Analytics 실행 및 디버그 UDA](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>다음 단계

* [로컬에서 Azure Stream Analytics 작업 개발 및 디버그](develop-locally.md)
* [Visual Studio에서 작업 다이어그램을 사용하여 로컬로 Azure Stream Analytics 쿼리 디버깅](debug-locally-using-job-diagram.md)
* [Azure Stream Analytics의 사용자 정의 함수](functions-overview.md)
 
