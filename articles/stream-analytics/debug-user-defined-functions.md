---
title: Azure Stream Analytics에서 사용자 정의 함수 디버깅
description: 이 문서에서는 Azure Stream Analytics에서 사용자 정의 함수를 디버그 하는 방법을 설명 합니다.
author: jenssuessmeyer
ms.author: jenss
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 46aa9879af4bda1cd4a5e06894ea2d964f838f32
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86181355"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics에서 사용자 정의 함수 디버깅 

UDF (사용자 정의 함수)가 정상적으로 작동 하지 않는 경우 문제를 해결 하려면 디버깅 해야 합니다. [Visual Studio Code](visual-studio-code-local-run-live-input.md) 또는 [Visual Studio](stream-analytics-vs-tools-local-run.md)를 사용 하 여 작업을 로컬로 실행할 때 Stream Analytics 작업에 대 한 udf를 디버그할 수 있습니다.

라이브 입력 스트림에 대해 로컬에서 작업을 실행 하는 경우 한 노드에서 클라우드 Azure Stream Analytics 엔진 실행만 모방 합니다. 실시간 데이터 로컬 테스트는 클라우드에서 수행 하는 성능 및 확장성 테스트를 대체할 수 없지만 테스트할 때마다 클라우드에 작업을 제출 하지 않고도 기능 테스트 중에 시간을 절약할 수 있습니다. 또한 로컬 또는 샘플 데이터를 사용 하 여 로컬 실행에 대 한 시간 정책을 사용 하지 않지만 라이브 데이터 테스트에 대 한 시간 정책이 지원 됩니다.

## <a name="pick-your-language"></a>언어 선택

.NET (c #) 또는 JavaScript를 사용 하 여 Azure Stream Analytics에 대 한 Udf를 작성할 수 있습니다. 

### <a name="functions-in-c"></a>C의 함수 # 

[Visual Studio를 사용 하 여 .Net udf를 작성](stream-analytics-edge-csharp-udf-methods.md)하는 경우 .net 클래스 프로젝트의 경우와 동일한 수준의 지원 기능을 얻을 수 있습니다. 이러한 지원이 다음을 포함합니다.

* 구문 검사 및 컴파일러 지원과 같은 컴파일 지원

* Stream Analytics 솔루션에서 c # 프로젝트 및 아티팩트를 추가, 빌드 및 참조 하는 기능입니다. 

* 공유 가능한 프로젝트에서 캡슐화 된 코드를 쉽게 다시 사용할 수 있습니다. 

* Visual Studio에서 직접 디버그 지원. Stream Analytics 프로젝트를 시작 프로젝트로 설정 하 고 c # 코드에서 중단점을 설정 합니다. 그런 다음 **F5** 키를 눌러 다른 c # 프로젝트의 경우와 마찬가지로 c # 코드를 디버깅 합니다. 

### <a name="functions-in-javascript"></a>JavaScript의 함수

JavaScript는 Stream Analytics에서 함수를 만들기 위한 또 다른 옵션입니다. JavaScript 코드는 Stream Analytics 프로젝트의 함수 영역에 직접 배치 되므로 프로젝트 간에 공유 하기가 더 어려워집니다.

컴파일은 Stream Analytics 프로젝트를 컴파일하거나 실행할 때 발생 합니다. 런타임에만 문제를 찾을 수 있는 기회가 더 높습니다. Stream Analytics에서 JavaScript 함수에 대 한 디버깅은 직접 지원 되지 않습니다.

## <a name="debug-options-for-javascript"></a>JavaScript에 대 한 디버그 옵션

Stream Analytics에서 JavaScript 함수에 대 한 디버깅이 지원 되지 않기 때문에 HTML 사이트에서 함수를 캡슐화 하 고 여기에서 출력을 가져오면 디버그할 수 있습니다.

다음 예제에서는 [Visual Studio Code](quick-create-vs-code.md)의 통합 런타임 환경에서 몇 가지 제한 사항을 사용 하 여 JavaScript udf를 디버그 하는 방법을 보여 줍니다.

### <a name="prerequisites"></a>필수 조건

시작 하기 전에 Azure Stream Analytics 프로젝트에 다음과 같은 항목이 있어야 합니다.

* 입력 
* 출력 
* Stream Analytics 쿼리 (. script.asaql) 
* Stream Analytics 작업 구성 (JobConfig.js)
* JavaScript UDF

### <a name="prepare-files"></a>파일 준비

다음 그림에서 *. script.asaql* 쿼리 파일에는 UDF에 대 한 호출 ( *fxcharCount*)만 포함 되어 있습니다. 이렇게 변경 하면 변경 내용이 적용 된 후에도 프로젝트를 컴파일할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![쿼리 파일 Stream Analytics 테스트](./media/debug-user-defined-functions/asaql-file.png)

테스트 파일을 호스팅할 테스트 **에 추가** 폴더를 만듭니다 .이는 JavaScript 함수로 테스트를 실행 하기 위해 호출 됩니다. 이 예제에서 폴더 이름은 *fxcharCount* 이 고 테스트 이름은 *Test_UDF.js*입니다. 

아래 이미지는 함수 파일을 로드 하 고 함수를 실행 하는 테스트 파일의 코드를 보여 줍니다. 이 예제는 간단 하지만 추가 테스트를 통해 추가 테스트 데이터 파일을 로드 하 고 루프를 실행 하 여 출력을 가져올 수 있습니다. 함수 호출의 notation은 파일이 참조 되 고 런타임에 로드 되지 않으므로 디버그할 수 있으므로 일반적인 호출과는 약간 다릅니다. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics 테스트 파일](./media/debug-user-defined-functions/test-file.png)

함수에서 다음 코드 줄을 파일에 추가 하 여 메서드를 노출 합니다. Visual Studio Code에서 코드를 컴파일하는 기능에는 영향을 주지 않습니다.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![JavaScript UDF Stream Analytics](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>디버그 지원 설치

디버그 하려면 **node.js**를 [다운로드](https://nodejs.org/en/download/) 하 고 설치 해야 합니다. 사용 중인 플랫폼에 따라 올바른 버전을 설치 합니다. node.js 런타임을 설치한 후 Visual Studio Code를 다시 시작 하 여 변경 내용을 구현 합니다. 

**실행 및 디버그** 를 선택 하거나 **ctrl + SHIFT + D** 를 눌러 디버깅을 시작 합니다. **node.js** 를 런타임으로 선택할 수 있는 콤보 상자가 표시 됩니다. node.js 설치 되어 있는 경우에는 기본적으로 사용 됩니다. F11 키를 사용 하 여 필요한 경우 코드를 단계별로 실행 하 고 위성 파일을 실행할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![Udf Stream Analytics 실행 및 디버그](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>사용자 정의 집계 디버그 

JavaScript Udf에 대해 debug 메서드를 사용 하 여 UDA (사용자 정의 집계)를 디버그할 수 있습니다. 이 예제에서는 *script.asaql* 쿼리 파일과 테스트 파일에 UDA를 추가 합니다.

UDF와 마찬가지로 변경 작업을 수행한 후 프로젝트를 컴파일하도록 UDA에 대 한 호출을 포함 합니다. 

> [!div class="mx-imgBorder"]
> ![Script.asaql에 uda 추가](./media/debug-user-defined-functions/asaql-uda.png)

*Test_UDA.js* 파일에서 UDF를 사용 하 여 수행한 것 처럼 UDA 파일을 참조 합니다. 또한, 및를 `main()` 호출 `init()` `accumulate()` 합니다. `accumulate()`상태 스택에 값을 넣는 루프에서 메서드가 호출 됩니다. `computeresult()`메서드를 호출 하 여 최종 쿼리를 작성 합니다. 

> [!div class="mx-imgBorder"]
> ![UDA 테스트 파일](./media/debug-user-defined-functions/uda-test.png)

UDF 예제에서와 같이 관련 메서드를 노출 하려면 일부 코드를 UDA 자체에 추가 해야 합니다.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![UDA에 추가 된 코드](./media/debug-user-defined-functions/uda-expose-methods.png)

**실행 및 디버그** 를 선택 하거나 **ctrl + SHIFT + D** 를 눌러 디버깅을 시작 합니다. **node.js** 를 런타임으로 선택할 수 있는 콤보 상자가 표시 됩니다. node.js 설치 되어 있는 경우에는 기본적으로 사용 됩니다. F11 키를 사용 하 여 필요한 경우 코드를 단계별로 실행 하 고 위성 파일을 실행할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![Uda Stream Analytics 실행 및 디버그](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 작업을 로컬로 개발 및 디버그](develop-locally.md)
* [Visual Studio에서 작업 다이어그램을 사용 하 여 로컬에서 Azure Stream Analytics 쿼리 디버그](debug-locally-using-job-diagram.md)
* [Azure Stream Analytics의 사용자 정의 함수](functions-overview.md)
 
