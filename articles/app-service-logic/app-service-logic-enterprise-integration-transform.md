<properties 
	pageTitle="엔터프라이즈 통합 팩 개요 | Microsoft Azure 앱 서비스" 
	description="엔터프라이즈 통합 팩의 기능을 사용하여 Microsoft Azure 앱 서비스를 사용하는 비즈니스 프로세스 및 통합 시나리오 사용" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2016" 
	ms.author="deonhe"/>

# XML 변환과 엔터프라이즈 통합

## 개요
엔터프라이즈 통합 변환 커넥터에서는 데이터의 형식을 변환합니다. 예를 들어 YearMonthDay 형식의 현재 날짜가 포함된 들어오는 메시지가 있을 수 있습니다. 날짜를 MonthDayYear 형식으로 변경하는 데 사용할 수 있습니다.

## 변환의 기능은 무엇인가요?
맵으로 알려진 변환은 원본 XML 스키마(입력)와 대상 XML 스키마(출력)로 구성됩니다. 다양한 기본 제공 함수를 사용하여 문자열 조작, 조건부 할당, 산술 식, 날짜 시간 포맷터, 루핑 구문 등을 비롯한 데이터를 조작하거나 제어할 수 있습니다.

## 변환 생성 방법
Visual Studio [엔터프라이즈 통합 SDK](https://aka.ms/vsmapsandschemas)를 사용하여 변환/맵을 만들 수 있습니다. 변환을 만들고 테스트하는 작업을 완료한 경우 통합 계정으로 업로드합니다.

## 변환 사용 방법
변환을 통합 계정에 업로드한 후에 논리 앱을 만드는 데 사용할 수 있습니다. 논리 앱은 논리 앱이 트리거될 때마다 (그리고 변환해야 하는 입력 콘텐츠가 있는 경우) 변환을 실행합니다.

**변환을 사용하는 단계는 다음과 같습니다**.

### 필수 조건 
미리 보기에서는 다음을 수행해야 합니다.
-  [Azure Functions 컨테이너 만들기](https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Azure Functions 컨테이너 만들기")


>[AZURE.TIP] Azure Functions 컨테이너의 이름을 적어두면 다음 단계에서 필요합니다.


-  [Azure Functions 컨테이너에 함수 추가](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "이 템플릿에서는 논리 앱 통합 시나리오에 사용할 변환 기능을 가진 웹후크 기반 C# Azure Functions를 만듭니다.")


>[AZURE.TIP] 함수의 이름을 적어두면 다음 단계에서 필요합니다.

지금까지 필수 구성 요소를 살펴보았습니다. 이제 논리 앱을 만들 차례입니다.

1. 논리 앱을 만들고 변환을 포함하는 [통합 계정에 연결](./app-service-logic-enterprise-integration-accounts.md "논리 앱에 통합 계정을 연결하는 방법 알아보기")합니다.
2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다. ![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)
3. 먼저 **작업 추가**를 선택하여 **변환 XML** 작업을 추가합니다. ![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)
4. 사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 *변환*이라는 단어를 입력합니다. ![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)
5. **변환 XML** 작업을 선택합니다. ![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)
6. 사용할 함수를 포함하는 **함수 컨테이너**를 선택합니다. 이 단계에서 이전에 만든 Azure Functions 컨테이너의 이름입니다.
7. 사용하려는 **함수**를 선택합니다. 이전에 만든 Azure Functions의 이름입니다.
8. 변환할 XML **콘텐츠**를 추가합니다. HTTP 요청에서 **콘텐츠**로 수신하는 XML 데이터를 사용할 수 있습니다. 이 예제에서는 논리 앱을 트리거한 HTTP 요청의 본문을 선택합니다.
9. 변환을 수행하는 데 사용하려는 **맵**의 이름을 선택합니다. 맵이 이미 통합 계정에 있어야 합니다. 이전 단계에서 맵을 포함한 통합 계정에 논리 앱 액세스 권한을 이미 제공했습니다.
10. 작업을 저장합니다. ![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png)

이 시점에서 맵의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB 응용 프로그램에 변환한 데이터를 저장할 수 있습니다. Salesforce에 변환의 출력을 보내는 작업을 쉽게 수행할 수 있습니다.

이제 HTTP 끝점에 요청하여 변환을 테스트할 수 있습니다.

## 기능 및 사용 사례

- 맵에서 만든 변환은 이름과 주소를 한 문서에서 다른 문서로 복사하는 것과 같이 단순할 수 있습니다. 또는 기본 맵 작업을 사용하여 더 복잡한 변환을 만들 수 있습니다.
- 문자열, 날짜 시간 함수 등 여러 맵 작업이나 함수를 바로 사용할 수 있습니다.
- 스키마 간에 직접 데이터 복사를 수행할 수 있습니다. SDK에 포함된 매퍼에서 원본 스키마의 요소를 대상 스키마의 대응 항목과 연결하는 선을 그리기만 하면 됩니다.
- 맵을 만들 때 만들어진 모든 관계와 링크를 보여 주는 맵의 그래픽 표현이 표시됩니다.
- 맵 테스트 기능을 사용하여 샘플 XML 메시지를 추가합니다. 한 번만 클릭하면 만든 맵을 테스트하고 생성된 출력을 확인할 수 있습니다.
- 기존 데이터 업로드
- XML 형식 지원을 포함합니다.


## 자세한 정보
- [엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")
 

<!---HONumber=AcomDC_0706_2016-->