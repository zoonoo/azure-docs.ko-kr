<properties
   pageTitle="Azure 함수 개요 | Microsoft Azure"
   description="Azure 함수가 금방 작성할 수 있는 간단한 함수를 만들어서 비동기 워크로드를 최적화할 수 있는 방법을 알아봅니다."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/21/2016"
   ms.author="cfowler;mahender"/>
   
   
# Azure 함수 개요

이 항목에서는 Azure Functions에 대한 간략한 개요를 제공합니다. 지금 바로 Azure Functions로 이동하여 시작하려면 [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)를 시작합니다. 함수에 대한 자세한 기술 정보를 찾으려는 경우 [개발자 참조](functions-reference.md)를 참조하세요.

## 함수에 대한 더 빠른 방법

데이터베이스를 정리하거나 연결된 장치에서 수백만 개의 메시지를 처리하는 기능을 빌드하는 간단한 작업을 해야 할 경우 1분 이내의 함수를 작성합니다. 선택한 개발 언어(C#, Node.JS, Python 등)를 사용합니다. 코드를 실행한 시간에 따라 지불하고 Azure를 신뢰하여 필요에 따라 크기를 조정합니다.

Azure 함수는 클라우드에서 쉽게 소량의 코드 또는 "함수"를 실행하기 위한 솔루션입니다. 실행할 전체 응용 프로그램 또는 인프라를 염려하지 않고 당면한 문제에 필요한 코드를 작성할 수 있습니다. 훨씬 더 생산성 있게 개발할 수 있고 몇 분만에 [첫 번째 함수를 시작](functions-create-first-azure-function.md)할 수 있습니다.

## 함수로 할 수 있는 작업은 무엇인가요?

Azure 함수는 데이터를 처리하고 시스템을 통합하며 IoT(사물 인터넷)으로 작업하고 간단한 API 및 마이크로 서비스를 구축하기 위한 유용한 솔루션입니다.

함수는 다음을 비롯한 일반적인 시나리오에 대한 템플릿의 갤러리를 포함합니다.

* GitHub webhook 요청에 응답
* Azure 저장소에 업로드된 이미지 크기 조정
* 주문 처리 큐로 작업
* 이 외에도 다양한 기능이 있습니다. 

함수의 작동 방법 및 몇 가지 예제 구성을 자세히 알아보려면 [개발자 참조](functions-reference.md)를 참조하세요.

## 기능

Azure 함수는 복잡한 통합 및 연결 태스크를 간소화하는 기능을 함께 제공하는 모든 기능을 갖춘 엔터프라이즈 지원 플랫폼입니다. Azure 함수를 사용하는 개발자는 이 일련의 핵심 기능으로 인프라 조각을 결합하는 대신 해당 목표에 집중하여 생산성을 향상시킬 수 있습니다.

다음 기능은 Azure 함수와 함께 포함됩니다.
    
* **선택한 언어** - C#, Node.js, Python, F#, PHP, 배치, Bash, Java 또는 실행 파일을 사용하는 쓰기 기능입니다.  
* **사용량에 따른 가격 책정 모델** - 코드를 실행하는 데 사용한 시간 동안만을 지불합니다. 아래의 [가격 책정 섹션](#pricing)에서 동적 앱 서비스 계획 옵션을 참조하세요.  
* **고유한 종속성 가져오기** - 함수는 즐겨찾는 라이브러리를 사용할 수 있도록 NuGet 및 NPM을 지원합니다.  
* **통합 보안** -Azure Active Directory, Facebook, Google, Twitter 및 Microsoft 계정과 같은 OAuth 공급자로 HTTP 트리거된 함수를 보호합니다.  
* **코드 없는 통합** - Azure 서비스 및 SaaS(software-as-a-service) 제품을 쉽게 활용합니다. 몇 가지 예제는 아래 [통합 섹션](#integrations)을 참조하세요.  
* **유연한 개발** - 포털 내 편집기를 사용하여 함수를 수정하거나 연속 통합을 설정하고 GitHub, Visual Studio Team Services를 통해 코드를 배포합니다.  
* **오픈 소스** - 함수는 오픈 소스이며 [GitHub에서 사용](https://github.com/azure/azure-webjobs-sdk-script)할 수 있습니다.  

### <a name="integrations"></a>통합

Azure 함수는 Azure 및 타사 서비스와의 다양한 통합을 지원합니다. 이를 사용하여 함수 및 시작 실행을 트리거하거나 코드에 대한 입력 및 출력으로 사용할 수 있습니다. 아래 테이블에서는 Azure 기능에서 지원하는 몇 가지 예제 통합을 보여줍니다.

[AZURE.INCLUDE [동적 계산](../../includes/functions-bindings.md)]

## <a name="pricing"></a>함수는 비용이 얼마입니까?

Azure 함수를 실행하는 방법은 동적 앱 서비스 계획을 사용하거나 기존 앱 서비스 계획을 사용하는 등 두 가지가 있습니다.

**동적 앱 서비스 계획**에서 리소스 관리에 대해 걱정할 필요가 없습니다. 함수를 실행할 때마다 Azure는 필요한 컴퓨팅 리소스를 모두 제공합니다. 코드가 실행하는 데 사용한 시간 동안만 지불합니다. 전체 가격 책정 정보는 [함수 가격 책정 페이지](/pricing/details/functions)에서 제공됩니다.

**클래식 앱 서비스 계획**을 사용하면 웹, 모바일 및 API 앱과 같은 함수를 실행할 수 있습니다. 다른 응용 프로그램에 앱 서비스를 이미 사용중인 경우 적합한 솔루션입니다. 함수는 추가 비용 없이 동일한 계획에서 실행할 수 있습니다 전체 세부 정보는 [앱 서비스 가격 책정 페이지](/pricing/details/app-service/)에서 찾을 수 있습니다.

## 문제 보고

[AZURE.INCLUDE [문제 보고](../../includes/functions-reporting-issues.md)]

##다음 단계

+ [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md) 바로 Azure Functions 빠른 시작을 사용하여 첫 번째 함수를 만듭니다. 
+ [Azure Functions 개발자 참조](functions-reference.md) 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 Azure Functions 런타임 및 참조에 대한 보다 기술적인 정보를 제공합니다.
+ [Azure Functions 테스트](functions-test-a-function.md) 함수를 테스트하는 다양한 도구와 기법을 설명합니다.
+ [Azure Functions를 확장하는 방법](functions-scale.md) 동적 서비스 계획 등 Azure Functions에 사용할 수 있는 서비스 계획과 적절한 계획을 선택하는 방법을 설명합니다. 
+ [Azure 앱 서비스 정의](../app-service/app-service-value-prop-what-is.md) Azure Functions는 배포, 환경 변수 및 진단과 같은 핵심 기능을 위해 Azure 앱 서비스 플랫폼을 활용합니다. 

<!---HONumber=AcomDC_0427_2016-->