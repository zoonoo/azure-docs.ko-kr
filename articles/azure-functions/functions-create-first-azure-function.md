<properties
   pageTitle="첫 번째 Azure Function 만들기 | Microsoft Azure"
   description="2분 이내에 서버가 없는 응용 프로그램인 첫 번째 Azure Function을 작성합니다."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="hero-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/05/2016"
   ms.author="glenga"/>

# 첫 번째 Azure Function 만들기

##개요
Azure 기능은 기타 Azure 서비스, SaaS 제품 및 온-프레미스 시스템에서 발생하는 이벤트로 트리거되는 코드를 구현하는 기능과 함께 기존 Azure 응용 프로그램 플랫폼을 확장하는 이벤트 기반의 주문형 계산 환경입니다. Azure 기능으로 응용 프로그램은 요구에 따라 확장하고 사용한 리소스에 대해서만 비용을 지불합니다. Azure Functions를 통해 다양한 프로그래밍 언어로 구현된 예약되거나 트리거된 코드 단위를 만들 수 있습니다. Azure Functions에 대해 자세히 알아보려면 [Azure Functions 개요](functions-overview.md)를 참조하세요.

이 항목에서는 Azure Functions 포털에서 HTTP 트리거에서 호출되는 간단한 "hello world" Node.js 함수를 만들기 위해 Azure Functions 빠른 시작을 사용하는 방법을 보여 줍니다. 포털에서 이러한 단계는 수행하는 방법을 보여 주는 짧은 비디오를 시청할 수도 있습니다.

## 비디오 보기

다음 비디오는 이 자습서에서 기본 단계를 수행하는 방법을 보여 줍니다.

[AZURE.VIDEO create-your-first-azure-function-simple]

##빠른 시작에서 함수 만들기

함수 앱은 Azure에서 함수 실행을 호스트합니다. 새 함수뿐만 아니라 새 함수 앱을 만들려면 다음 단계를 따릅니다. 첫 번째 함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [무료 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.

1. [Azure Functions 포털](https://functions.azure.com/signin)로 이동하여 Azure 계정으로 로그인합니다.

2. 새 함수 앱에 대해 고유한 **이름**을 입력하거나 생성된 이름을 수락하거나 선호하는 **지역**을 선택한 후 **만들기 + 시작**을 클릭합니다.

3. **빠른 시작** 탭에서 **WebHook + API** > **함수 만들기**를 클릭합니다. 새로운 미리 정의된 Node.js 함수가 만들어집니다.

4. (선택 사항) 빠른 시작의 이 시점에서 포털의 Azure Functions 기능을 둘러보도록 선택할 수 있습니다. 둘러보기를 완료했거나 건너뛴 경우 HTTP 트리거를 사용하여 새 함수를 테스트할 수 있습니다.

##함수 테스트

Azure Functions 빠른 시작에는 함수 코드가 포함되어 새 함수를 즉시 테스트할 수 있습니다.

1. **개발** 탭에서 **코드** 창을 검토하고 이 Node.js 코드에는 메시지 본문 및 쿼리 문자열에 전달된 *이름* 값과 함께 HTTP 요청이 필요함을 확인합니다. 함수가 실행되면 이 값은 응답 메시지에 반환됩니다.

2. **요청 본문** 텍스트 상자 아래로 스크롤하고 *이름* 속성의 값을 이름으로 변경하고 **실행**을 클릭합니다. 실행이 테스트 HTTP 요청에 의해 트리거되고 정보가 스트리밍 로그에 기록되며 "hello" 응답이 **출력**에 표시되는 것을 확인하게 됩니다.

3. 다른 브라우저 창이나 탭에서 동일한 함수의 실행을 트리거하려면 **개발** 탭에서 **함수 URL**을 복사하여 브라우저 주소 표시줄에 붙여 넣은 다음 쿼리 문자열 값 `&name=yourname`을 추가하고 Enter 키를 누릅니다. 같은 정보가 로그에 기록되고 이전처럼 브라우저에 "hello" 응답이 표시됩니다.

##다음 단계

이 빠른 시작은 기본 HTTP 트리거 함수의 매우 간단한 실행을 보여 줍니다. 사용자 앱에서 Azure Functions의 강력한 기능을 활용하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

+ [Azure Functions 개발자 참조](functions-reference.md) 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.
+ [Azure Functions 테스트](functions-test-a-function.md) 함수를 테스트하는 다양한 도구와 기법을 설명합니다.
+ [Azure Functions를 확장하는 방법](functions-scale.md) 동적 서비스 계획 등 Azure Functions에 사용할 수 있는 서비스 계획과 적절한 계획을 선택하는 방법을 설명합니다. 
+ [Azure 앱 서비스 정의](../app-service/app-service-value-prop-what-is.md) Azure Functions는 배포, 환경 변수 및 진단과 같은 핵심 기능을 위해 Azure 앱 서비스 플랫폼을 활용합니다. 

[AZURE.INCLUDE [시작 메모](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0511_2016-->