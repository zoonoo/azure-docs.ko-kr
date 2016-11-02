<properties
   pageTitle="Azure Portal에서 함수 만들기 | Microsoft Azure"
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
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="glenga"/>

#Azure Portal에서 함수 만들기

##개요
Azure 기능은 다른 Azure 서비스, SaaS 제품 및 온-프레미스 시스템에서 발생하는 이벤트에 의해 트리거되는 코드를 구현하는 기능으로 기존 Azure 응용 프로그램 플랫폼을 확장하는 이벤트 기반의 주문형 계산 환경입니다. Azure Functions를 통해 응용 프로그램은 요구에 따라 확장하고 사용하는 리소스에 대해서만 비용을 지불합니다. Azure Functions를 사용하여 다양한 프로그래밍 언어로 구현되는 예약되거나 트리거된 코드 단위를 만들 수 있습니다. Azure Functions에 대해 자세히 알아보려면 [Azure Functions 개요](functions-overview.md)를 참조하세요.

이 토픽에서는 Azure Portal을 사용하여 HTTP 트리거에서 호출되는 간단한 "hello world" Node.js Azure 함수를 만드는 방법을 보여 줍니다. Azure App Service에서 함수 앱을 명시적으로 만들어야 Azure Portal에서 함수를 만들 수 있습니다. 함수 앱이 자동으로 만들어지게 하려면 더 간단한 빠른 시작 환경으로서 비디오를 포함하고 있는 [다른 Azure Functions 빠른 시작 자습서](functions-create-first-azure-function.md)를 참조하세요.

##함수 앱 만들기

함수 앱은 Azure에서 함수 실행을 호스트합니다. Azure Portal에서 함수 앱을 만들려면 다음 단계를 수행합니다.

첫 번째 함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [무료 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 Azure 계정으로 로그인합니다.

2. **+새로 만들기** > **웹 + 모바일** > **함수 앱**을 클릭하고 **구독**을 선택하고 함수 앱을 식별하는 고유한 **앱 이름**을 입력한 후 다음 설정을 지정합니다.

	+ **[리소스 그룹](../azure-portal/resource-group-portal.md/)**: **새로 만들기**를 선택하고 새 리소스 그룹에 대한 이름을 입력합니다. 기존 리소스 그룹을 선택할 수도 있지만 함수 앱에 대한 동적 앱 서비스 계획을 만들 수 없을지 모릅니다.
	+ **[앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**: *동적* 또는 *클래식*을 선택합니다.
		+ **동적**: Azure Functions에 대한 기본 계획 유형입니다. 동적 계획을 선택하는 경우, **위치**를 선택하고 **메모리 할당**(MB)를 설정하기도 해야 합니다. 메모리 할당이 비용에 미치는 영향에 대한 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요.
		+ **클래식**: 클래식 앱 서비스 계획을 사용하려면 **앱 서비스 계획/위치**를 만들거나 기존 계획이나 위치를 선택해야 합니다. 이러한 설정은 [위치, 기능, 비용을 결정하고 앱과 연결된 리소스를 계산](https://azure.microsoft.com/pricing/details/app-service/)합니다.
	+ **저장소 계정**: 각 함수 앱에 저장소 계정이 필요합니다. 기존 저장소 계정을 선택하거나 계정을 만들 수 있습니다.

	![Azure Portal에서 새 함수 앱 만들기](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

3. **만들기**를 클릭하여 새 함수 앱을 프로비전하고 배포합니다.

이제 함수 앱이 프로비전되었으므로 첫 번째 함수를 만들 수 있습니다.

## 함수 만들기

이 단계는 Azure Functions 빠른 시작에서 함수를 만듭니다.

1. **빠른 시작** 탭에서 **웹후크 + API** 및 **JavaScript**를 클릭한 다음 **함수 만들기**를 클릭합니다. 새로운 미리 정의된 Node.js 함수가 만들어집니다.

	![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (선택 사항) 빠른 시작의 이 시점에서 포털의 Azure Functions 기능을 둘러보도록 선택할 수 있습니다. 둘러보기를 완료했거나 건너뛴 경우 HTTP 트리거를 사용하여 새 함수를 테스트할 수 있습니다.

##함수 테스트

Azure Functions 빠른 시작에는 함수 코드가 포함되어 있어 새 함수를 즉시 테스트할 수 있습니다.

1. **개발** 탭에서 **코드** 창을 검토하고 이 Node.js 코드에는 메시지 본문 또는 쿼리 문자열에 전달된 *이름* 값과 함께 HTTP 요청이 필요함을 확인합니다. 함수가 실행되면 이 값은 응답 메시지에 반환됩니다.

	![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)

2. **요청 본문** 텍스트 상자 아래로 스크롤하고 *이름* 속성의 값을 이름으로 변경하고 **실행**을 클릭합니다. 실행이 테스트 HTTP 요청에 의해 트리거되고 정보가 스트리밍 로그에 기록되며 "hello" 응답이 **출력**에 표시되는지 확인합니다.

3. 다른 브라우저 창이나 탭에서 동일한 함수의 실행을 트리거하려면 **개발** 탭에서 **함수 URL**을 복사하여 브라우저 주소 표시줄에 붙여 넣은 다음 쿼리 문자열 값 `&name=yourname`을 추가하고 Enter 키를 누릅니다. 같은 정보가 로그에 기록되고 이전처럼 브라우저에 "hello" 응답이 표시됩니다.

##다음 단계

이 빠른 시작은 기본 HTTP 트리거 함수의 매우 간단한 실행을 보여 줍니다. 사용자 앱에서 Azure Functions의 강력한 기능을 활용하는 방법에 대한 자세한 내용은 다음 토픽을 참조하세요.

+ [Azure Functions 개발자 참조](functions-reference.md) 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.
+ [Azure Functions 테스트](functions-test-a-function.md) 함수를 테스트하는 다양한 도구와 기법을 설명합니다.
+ [Azure Functions를 확장하는 방법](functions-scale.md) 동적 서비스 계획 등 Azure Functions에 사용할 수 있는 서비스 계획과 적절한 계획을 선택하는 방법을 설명합니다.
+ [Azure App Service 정의](../app-service/app-service-value-prop-what-is.md) Azure Functions는 배포, 환경 변수 및 진단과 같은 코어 기능을 위해 Azure App Service 플랫폼을 사용합니다.

[AZURE.INCLUDE [시작 메모](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0914_2016-->