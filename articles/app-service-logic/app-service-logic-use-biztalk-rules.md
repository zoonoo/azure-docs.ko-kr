<properties
   pageTitle="BizTalk 규칙"
   description="이 항목에서는 BizTalk 규칙의 기능을 설명하고 사용 지침을 제공합니다."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/01/2015"
   ms.author="andalmia"/>

#BizTalk 규칙

비즈니스 규칙은 비즈니스 프로세스를 제어하는 정책 및 의사 결정을 캡슐화합니다. 이러한 정책은 절차 설명서, 계약 또는 규약에 공식적으로 정의되거나 직원이 가지고 있는 지식 또는 전문 지식으로 존재할 수 있습니다. 이러한 정책은 동적이며 비즈니스 계획의 변경, 규정 또는 기타 이유로 시간 경과에 따라 변경될 수 있습니다.

기존 프로그래밍 언어로 이러한 정책을 구현하려면 상당한 시간과 조정이 필요하며 프로그래머가 아닌 사용자가 비즈니스 정책 만들기 및 유지 관리에 참여할 수 없습니다. BizTalk 비즈니스 규칙은 이러한 정책을 신속하게 구현하고 나머지 비즈니스 프로세스를 분리하는 방법을 제공합니다. 이렇게 하면 나머지 비즈니스 프로세스에 영향을 주지 않고 비즈니스 정책을 필요에 따라 변경할 수 있습니다.

##규칙을 사용하는 이유

비즈니스 프로세스에 BizTalk 비즈니스 규칙을 사용하는 3가지 핵심 이유는 다음과 같습니다.

* 응용 프로그램 코드에서 비즈니스 논리 분리
- 비즈니스 분석가가 비즈니스 논리 관리에 대한 제어권을 강화할 수 있음
+ 비즈니스 논리에 대한 변경 내용을 더 빠르게 프로덕션에 적용할 수 있음

##규칙 개념

##어휘

_vocabulary_는 규칙 조건과 작업에 사용된 컴퓨팅 개체의 이름으로 구성된 정의 컬렉션입니다. 어휘 정의를 사용하면 특정 비즈니스 분야에 종사하는 사람들이 보다 쉽게 규칙을 읽고 이해하며 공유할 수 있습니다.

어휘는 비즈니스 의미 체계와 구현 간의 차이를 좁히도록 설계되었습니다. 예를 들어 승인 상태에 대한 데이터 바인딩이 SQL 쿼리로 표현되는, 특정 데이터베이스의 특정 행에 있는 특정 열을 가리킬 수 있습니다. 이러한 종류의 복잡한 표현을 규칙에 삽입하는 대신 해당 데이터 바인딩과 연결된 어휘 정의를 "상태"라는 친숙한 이름으로 만들 수 있습니다. 그런 다음 개수에 관계없이 원하는 규칙에 "상태"를 포함할 수 있으며 규칙 엔진이 테이블에서 해당 데이터를 검색할 수 있습니다.

##규칙

비즈니스 규칙은 비즈니스 프로세스의 수행을 제어하는 선언문입니다. 규칙은 조건과 동작으로 구성됩니다. 조건이 평가되고, 평가 결과가 true이면 규칙 엔진이 하나 이상의 작업을 시작합니다. 비즈니스 규칙 프레임워크의 규칙은 다음 형식으로 정의됩니다.

_IF_ _조건_ _THEN_ _동작_

다음 예제를 살펴보세요.

*금액이 사용 가능한 자금보다 작거나 같은 경우*  
*그러면 트랜잭션을 수행하고 영수증을 인쇄함*

이 규칙은 두 금액 값, 즉 트랜잭션 금액과 사용 가능한 자금의 비교 형태로 비즈니스 논리를 적용하여 트랜잭션을 수행할지 여부를 결정합니다.
비즈니스 규칙을 사용하여 비즈니스 규칙을 만들고 수정 및 배포할 수 있습니다. 또는 프로그래밍 방식으로 앞의 작업을 수행할 수 있습니다.

###조건

조건은 하나 이상의 조건자로 구성된 true/false(부울) 식입니다. 이 예제에서는 보다 작거나 같음 조건자가 금액 및 사용 가능한 자금에 적용됩니다. 이 조건은 항상 true 또는 false로 평가됩니다.
조건자는 논리 연산자 AND, OR 및 NOT과 조합되어 잠재적으로 매우 큰 논리 식을 형성할 수 있지만 항상 true 또는 false로 평가됩니다.

###동작

동작은 조건 평가의 함수 결과입니다. 규칙 조건이 충족되면 해당 작업이 시작됩니다.
이 예제에서 "트랜잭션 수행" 및 "영수증 인쇄"는 조건(이 경우 "금액이 사용 가능한 자금보다 작거나 같은 경우")이 true인 경우에만(필요충분조건) 수행되는 동작입니다.
비즈니스 규칙 프레임워크에서 작업은 XML 문서에 대해 설정된 작업을 수행하는 것으로 표현됩니다.

##정책

정책은 규칙의 논리적 그룹화입니다. 정책을 작성, 저장 및 테스트하고 결과에 만족할 경우 프로덕션 환경에 사용합니다.

###정책 작성

규칙 포털에서 정책을 작성할 수 있습니다. 정책은 임의로 큰 규칙 집합을 포함할 수 있지만 일반적으로 정책을 사용할 응용 프로그램의 컨텍스트 내에서 특정 비즈니스 분야와 관련된 규칙에서 정책을 작성합니다.

###정책 테스트
프로덕션 환경에서 정책을 사용하기 전에 정책 테스트 실행을 효과적으로 수행할 수 있습니다. 규칙 포털을 통해 정책에 대한 입력을 제공하고 정책을 실행하며 해당 출력을 확인할 수 있습니다. 출력에는 로그, 규칙 실행, 조건 평가 및 결과 출력이 포함됩니다.

##샘플 시나리오 - 보험금 청구
샘플 시나리오를 사용하여 이에 대한 비즈니스 논리를 작성하는 과정을 알아봅니다.

![대체 텍스트][1]

단순한 보험금 청구 시나리오에서는 청구인이 웹 사이트, 휴대폰 앱 등과 같은 클라이언트를 통해 보험금 청구를 제출합니다. 이 청구 요청은 해당 비즈니스의 청구 처리 장치로 전송되어 처리 결과에 따라 승인 또는 거부되거나 추가 수동 처리를 위해 전송될 수 있습니다.
이 시나리오에서 청구 처리 장치는 해당 시스템에 대한 비즈니스 논리를 포함하는 것이 됩니다. 이 장치를 자세히 살펴보면 다음을 확인할 수 있습니다.

![대체 텍스트][2]

이제 비즈니스 규칙을 사용하여 이 비즈니스 논리를 구현하겠습니다.


##규칙 API 앱 만들기


1. Azure 포털에 로그인하여 홈페이지로 이동합니다.
1. 새로 만들기->Azure 마켓플레이스->API 앱->BizTalk 규칙->만들기를 클릭합니다.
![대체 텍스트][3]
1. 열리는 새 블레이드에서 다음 정보를 입력합니다.  
	1. 이름 – 규칙 API 앱의 이름을 지정합니다.
	1. 앱 호스팅 계획 – 웹 호스팅 계획을 선택하거나 만듭니다.
	1. 가격 책정 계층 – 이 앱이 상주할 가격 책정 계층을 선택합니다.
	1. 리소스 그룹 – 앱이 상주할 리소스 그룹을 선택하거나 만듭니다.
	1. 위치 – 앱을 배포할 지리적 위치를 선택합니다.
4.	만들기를 클릭합니다. 몇 분 내에 BizTalk 규칙 API 앱이 만들어집니다.

##어휘 만들기
BizTalk 규칙 API 앱을 만든 후 다음 단계는 어휘를 만드는 것입니다. 일반적으로 개발자가 이 연습을 수행할 것으로 예상됩니다. 어휘를 만들려면 다음 단계를 따르세요.


1. 찾아보기->API 앱-><Your Rules API App>을 클릭하여 직접 만든 API 앱을 찾습니다. 그러면 아래와 유사한 규칙 API 앱 대시보드로 이동합니다.

   ![대체 텍스트][4]

2. 그런 다음 "어휘 정의"를 클릭합니다. 그러면 어휘 작성 화면이 표시됩니다. "추가"를 클릭하여 새 어휘 정의 추가를 시작합니다. 현재 지원되는 두 가지 유형의 어휘 정의는 리터럴과 XML입니다.

##리터럴 정의
1.	"추가"를 클릭하면 새 "정의 추가" 블레이드가 열립니다. 다음 값을 입력합니다.
  1.	이름 – 특수 문자 없이 영숫자 문자만 사용해야 합니다. 또한 기존 어휘 정의 목록에 고유한 이름이어야 합니다.
  2.	설명 – 선택적 필드입니다.
  3.	유형 – 두 가지 유형이 지원됩니다. 이 예제에서는 리터럴을 선택합니다.
  4.	입력 유형 – 사용자가 정의의 데이터 형식을 선택할 수 있습니다. 현재 다음 네 가지 데이터 형식을 선택할 수 있습니다. 
    i.	문자열 – 이 값은 큰따옴표 안에 입력해야 합니다("예제 문자열").  
    ii.	부울 – true 또는 false일 수 있습니다.  
    iii.	숫자 – 임의의 10진수일 수 있습니다.  
    iv.	DateTime – 정의가 날짜 데이터 형식임을 의미합니다. mm/dd/yyyy hh:mm:ss AM\PM 형식으로 데이터를 입력해야 합니다.  
    v.	입력 – 정의 값을 입력합니다. 여기서 입력하는 값은 선택한 데이터 형식을 준수해야 합니다. 단일 값, 쉼표로 구분된 값 집합 또는 to 키워드를 사용한 값 범위를 입력할 수 있습니다. 예를 들어 고유 값 1, 집합 1, 2, 3 또는 1 ~ 5 범위(1 to 5)를 입력할 수 있습니다. 범위는 숫자에만 지원됩니다.

![대체 텍스트][5]
##XML 정의
어휘 유형을 XML로 선택한 경우 다음과 같은 입력을 지정해야 합니다.  
  a.	스키마 – 이 입력을 클릭하면 이미 업로드된 스키마 목록에서 선택하거나 새 스키마를 업로드할 수 있는 새 블레이드가 열립니다.   
  b.	XPATH – 이 입력은 이전 단계에서 스키마를 선택한 후에만 잠금 해제됩니다. 이 입력을 클릭하면 선택된 스키마가 표시되어 어휘 정의를 만들어야 하는 노드를 선택할 수 있습니다.  
  c.	팩트 – 이 입력은 처리를 위해 규칙 엔진에 공급되는 데이터 개체를 식별합니다. 이 입력은 고급 속성이며 기본적으로 선택한 XPATH의 부모로 설정됩니다. FACT는 체인 및 컬렉션 시나리오에 특히 중요합니다.

![대체 텍스트][6]

### 일괄 추가
위 단계는 어휘 정의를 만드는 환경을 캡처한 것입니다. 만들고 나면 만든 어휘 정의가 목록 형태로 나타납니다. 위 단계를 매번 반복하는 대신 동일한 스키마에서 여러 정의를 생성할 수 있어야 합니다. 이 경우 일괄 추가 기능이 매우 유용합니다. 
"일괄 추가"를 클릭하면 새 블레이드로 이동합니다. 첫 번째 단계는 여러 정의를 만들 스키마를 선택하는 것입니다. 이 입력을 클릭하면 이미 업로드된 스키마 목록에서 선택하거나 새 스키마를 업로드할 수 있는 새 블레이드가 열립니다. 
이제 XPATH 속성이 잠금 해제됩니다. 이 입력을 클릭하면 여러 노드를 선택할 수 있는 스키마 뷰어가 열립니다. 
만든 여러 정의의 이름은 선택한 노드의 이름으로 기본 설정됩니다. 이러한 이름은 만든 후 언제든지 수정할 수 있습니다.

![대체 텍스트][7]

##정책 만들기
개발자가 필요한 어휘를 만들고 나면 비즈니스 분석가가 Azure 포털을 통해 비즈니스 정책을 만듭니다.  
	1.	만든 규칙 앱에는 정책 필터가 있으며, 필터를 클릭하면 사용자가 정책 만들기 페이지로 이동됩니다.  
	2.	이 페이지에는 이 특정 규칙 앱에 있는 정책 목록이 표시됩니다. 정책 이름을 입력하고 탭을 누르기만 하면 새 정책을 추가할 수 있습니다. 여러 정책이 단일 규칙 API 앱에 상주할 수 있습니다.  
	3.	만든 정책을 클릭하면 정책 정보 페이지로 이동합니다. 여기서 정책에 포함된 규칙을 볼 수 있습니다.  
	![대체 텍스트][8] 
	4.	"새로 추가"를 클릭하여 새 규칙을 추가합니다. 그러면 새 블레이드로 이동합니다.

##규칙 만들기
규칙은 조건 및 동작문의 컬렉션입니다. 조건이 true로 평가되면 동작이 실행됩니다. 규칙 만들기 블레이드에서 해당 정책에 고유한 규칙 이름 및 설명(선택 사항)을 입력합니다.
조건 상자를 사용하여 복잡한 조건문을 만들 수 있습니다. 지원되는 키워드는 다음과 같습니다.
1. 	And – 조건부 연산자  
2. 	Or - 조건부 연산자  
3. 	does_not_exist  
4. 	exists  
5. 	false  
6. 	is_equal_to  
7. 	is_greater_than  
8. 	is_greater_than_equal_to  
9. 	is_in  
10. is_less_than  
11. is_less_than_equal_to  
12. is_not_in  
13. is_not_equal_to  
14. mod  
15. true 

동작(Then) 상자에 한 줄에 하나씩 여러 문을 입력하여 실행할 동작을 만들 수 있습니다. 지원되는 키워드는 다음과 같습니다.  
1.	equals  
2.	false  
3.	true  
4.	halt  
5.	mod  
6.	null  
7.	update  

조건 및 동작 상자는 규칙을 빠르게 작성하는 데 도움이 되는 Intellisense를 제공합니다. Ctrl+스페이스바를 누르거나 단순히 입력을 시작하면 이 기능을 트리거할 수 있습니다. 입력한 문자와 일치하는 키워드가 자동으로 필터링되어 표시됩니다. Intellisense 창에 모든 키워드 및 어휘 정의가 표시됩니다. 
![대체 텍스트][9]

##명시적 연결 전달
BizTalk 규칙은 명시적 연결 전달을 지원합니다. 즉, 사용자가 특정 동작에 대한 응답으로 규칙을 다시 평가하려는 경우 특정 키워드를 사용하여 이를 트리거할 수 있습니다. 지원되는 키워드는 다음과 같습니다. 
   1.	update <vocabulary definition> - 이 키워드는 지정된 어휘 정의를 조건에 사용하는 모든 규칙을 다시 평가합니다.  
   2.	Halt – 이 키워드는 모든 규칙 실행을 중지합니다.

##규칙 사용\사용 안 함
정책의 각 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다. 기본적으로 모든 규칙은 사용하도록 설정됩니다. 사용하지 않도록 설정된 규칙은 정책 실행 중에 실행되지 않습니다. 규칙 사용\사용 안 함은 규칙 블레이드에서 직접 수행하거나(블레이드 맨 위에 있는 명령 모음에서 명령을 사용할 수 있음), 정책의 상황에 맞는 메뉴(규칙을 마우스 오른쪽 단추로 클릭)에 있는 사용\사용 안 함 옵션을 통해 수행할 수 있습니다.

##규칙 우선 순위
정책의 모든 규칙은 순서대로 실행됩니다. 실행 우선 순위는 정책에서 발생하는 순서에 따라 결정됩니다. 규칙을 끌어서 놓기만 하면 이 우선 순위를 변경할 수 있습니다.

##정책 테스트
정책을 작성한 후 프로덕션에서 사용하기 전에 정책을 테스트하기 위한 프로비전이 있습니다. "정책 테스트" 명령을 사용하여 정책 테스트 블레이드로 이동할 수 있습니다. 이 블레이드에서 사용자 입력이 필요한 정책에 사용된 어휘 정의 목록을 확인할 수 있습니다. 사용자는 테스트 시나리오에 맞게 이러한 입력 값을 수동으로 추가할 수 있습니다. 또는 입력에 대한 테스트 XML를 가져올 수도 있습니다. 모든 입력이 완료되면 테스트를 실행할 수 있으며 각 어휘 정의에 대한 출력이 비교하기 쉽도록 출력 열에 표시됩니다. 비즈니스 분석가용 로그를 보려면 "로그 보기"를 클릭하여 실행 로그를 확인합니다. 로그를 저장하려면 "출력 저장" 옵션을 사용하여 독립적인 분석을 위해 모든 테스트 관련 데이터를 저장할 수 있습니다.

## 논리 앱에 규칙 사용
정책이 작성 및 테스트되고 나면 사용할 준비가 완료되었습니다. 새로 만들기->논리 앱을 수행하여 새 논리 앱을 만들 수 있습니다. 디자이너의 오른쪽 갤러리에서 BizTalk 규칙을 사용할 수 있습니다. 이제 디자이너 화면으로 끌어서 놓을 수 있습니다. 이 작업이 완료되고 나면 대상으로 할 규칙 API 앱(동작)을 선택할 수 있는 옵션이 있습니다. 동작에는 실행할 정책의 목록이 포함됩니다. 특정 정책을 선택한 후 정책에 필요한 입력을 입력해야 합니다. 추가 의사 결정을 위해 규칙 API 앱의 출력을 다운스트림으로 사용할 수 있습니다.

## API를 통한 규칙 사용
사용 가능한 풍부한 API 집합을 통해 규칙 API 앱을 호출할 수도 있습니다. 이 방식으로 사용자는 흐름만 사용하도록 제한되지 않고 REST 호출을 통해 응용 프로그램에서 규칙을 사용할 수 있습니다. 사용 가능한 정확한 REST API는 Rules 대시보드의 "API 정의" 렌즈를 클릭하여 확인할 수 있습니다.

![대체 텍스트][10]

다음은 C#에서 이 API를 사용하는 방법의 한 가지 예입니다.

   			// Constructing the JSON message to use in API call to Rules API App

			// xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0="http://tempuri.org/XMLSchema.xsd">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

			// The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
			// In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
			// This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

			// The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

위의 규칙 API 앱은 보안 설정이 "공용(익명)"으로 설정되어 있습니다. 이 설정은 모든 설정->응용 프로그램 설정->액세스 수준을 사용하여 API 앱 내에서 설정할 수 있습니다.

![대체 텍스트][11]

## 어휘 및 정책 편집
비즈니스 규칙을 사용할 경우의 주요 이점 중 하나는 비즈니스 논리 변경 내용을 훨씬 빨리 프로덕션으로 푸시할 수 있다는 것입니다. 어휘 및 정책 변경 내용이 프로덕션에 즉시 적용됩니다. 사용자가 해당 어휘 정의나 정책을 찾아서 변경하기만 하면 변경 내용이 적용됩니다.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG

<!---HONumber=Oct15_HO2-->