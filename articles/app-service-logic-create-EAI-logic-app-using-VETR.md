<properties 
   pageTitle="VETR을 사용하여 EAI 논리 앱 만들기" 
   description="이 항목에서는 BizTalk XML 서비스의 유효성 검사, 인코드 및 변환 기능을 사용합니다." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/23/2015"
   ms.author="rajram"/>


# VETR을 사용하여 EAI 논리 앱 만들기

대부분의 EAI(엔터프라이즈 응용 프로그램 통합) 시나리오에서는 원본과 대상 간에 데이터를 조정합니다. 이러한 시나리오에는 다음과 같은 공통된 요구 사항이 있습니다.

- 다른 시스템의 데이터가 올바른 형식인지 확인합니다.
- 들어오는 데이터에 대해 "조회"를 수행하여 결정을 내립니다.
- 한 형식에서 다른 형식으로 데이터를 변환합니다(예: CRM 시스템의 데이터를 ERP 시스템의 데이터 형식으로 변환).
- 데이터를 원하는 응용 프로그램 또는 시스템으로 경로 지정합니다.

이 항목에서는 일반적인 통합 패턴인 "단방향 메시지 중재" 또는 VETR(유효성 검사, 보강, 변환, 경로 지정)을 살펴봅니다. VETR 패턴은 소스 엔터티와 대상 엔터티 간에 데이터를 중재합니다. 일반적으로 소스 및 대상은 데이터 소스입니다.

주문을 받는 웹사이트를 고려합니다. 사용자가 HTTP를 사용하여 시스템에 명령을 게시합니다. 내부적으로 시스템은 들어오는 데이터가 올바른지 유효성을 검사하고, 정규화하고, 추가 처리를 위해 서비스 버스 큐에 보관합니다. 시스템이 특정 형식을 예상하면서 큐에서 명령을 수행합니다. 따라서 종단 간 흐름은 다음과 같습니다.

HTTP -> 유효성 검사 -> 변환 -> 서비스 버스

![기본 VETR 흐름][1]

다음 BizTalk API 앱을 사용하면 이 패턴을 손쉽게 작성할 수 있습니다.

*HTTP 트리거* - 메시지 이벤트를 트리거하는 소스입니다. *유효성 검사* - 들어오는 데이터의 정확성을 유효성 검사합니다. *변환* - 들어오는 형식에서 다운스트림 시스템에 필요한 형식으로 데이터를 변환합니다. *서비스 버스 커넥터* - 데이터가 전송되는 대상 엔터티입니다.


## 기본 VETR 패턴 구성
### 기본 사항

Azure 관리 포털 화면의 왼쪽 아래에서 **+새로 만들기** 단추를 클릭하고 논리 앱을 클릭합니다. 이름, 위치, 구독, 리소스 그룹 및 작동 위치를 선택합니다. 리소스 그룹은 앱 컨테이너 역할을 하며 앱의 모든 리소스가 동일한 리소스 그룹으로 이동됩니다.

이제 트리거 및 동작을 추가하겠습니다.


## HTTP 트리거 추가

1. 갤러리에서 **HTTP 수신기**를 선택하여 새 수신기를 만듭니다. 이를 **HTTP1**이라고 합니다.
2. **자동으로 응답을 보내나요?** 설정을 false로 그대로 둡니다. _HTTP Method_를 _POST_로 설정하고 _상대 URL_을 _ /OneWayPipeline_으로 설정하여 트리거 동작을 구성합니다.

![HTTP 트리거][2]


## 유효성 검사 동작 추가

이제 트리거가 실행될 때마다 즉, HTTP 끝점에 대한 호출을 받을 때마다 실행되는 동작을 입력합니다.

1. 갤러리에서 **BizTalk XML 유효성 검사기**를 추가하고 이름을 _(Validate1)_로 지정하여 인스턴스를 만듭니다.
2. 들어오는 XML 메시지의 유효성을 검사하도록 XSD 스키마를 구성합니다. _유효성 검사_ 동작을 선택하고 _triggers('httplistener').outputs.Content_를 _inputXml_ 매개 변수의 값으로 선택합니다.

이제, 유효성 검사 동작이 HTTP 수신기 후 첫 번째 작업입니다. 마찬가지로, 작업의 나머지 부분을 추가하겠습니다.

![BizTalk XML 유효성 검사기][3]


## 변환 작업 추가
들어오는 데이터를 정규화하도록 변환을 구성하겠습니다.

1. 갤러리에서 **변환**을 추가합니다. 
2. 들어오는 XML 메시지를 변환하도록 변환을 구성하려면 **변환** 동작을 이 API가 호출될 때 실행할 작업으로 선택하고 ```triggers(‘httplistener’).outputs.Content```를 _inputXml_의 값으로 선택합니다. 들어오는 데이터가 구성된 모든 변환과 일치하므로 Map은 선택적 매개 변수이며, 스키마와 일치하는 항목만 적용됩니다.
3. 마지막으로, 변환은 유효성 검사에 성공한 경우에만 실행됩니다. 이 조건을 구성하려면 _"충족할 조건 추가"_의 오른쪽 위에 있는 기어 아이콘을 선택합니다. 조건을 ```equals(actions('xmlvalidator').status,'Succeeded')```로 설정합니다.


![BizTalk 변환][4]


## 서비스 버스 커넥터 추가
이제, 데이터를 쓸 대상인 서비스 버스 큐를 추가하겠습니다.

1. 갤러리에서 **서비스 버스 커넥터**를 추가합니다. _이름_을 _Servicebus1_로 설정하고, _연결 문자열_을 서비스 버스 인스턴스에 대한 연결 문자열로 설정하고, _엔터티 이름_을 _큐_로 설정하고, _구독 이름_을 건너뜁니다. 
2. **메시지 보내기** 동작을 선택하고 동작의 _메시지_ 필드를 _actions('transformservice').outputs.OutputXml_로 설정합니다.

![서비스 버스][5]


## HTTP 응답 보내기
파이프라인 처리가 완료되면 다음 단계를 통해 성공 및 실패에 대한 HTTP 응답을 다시 보냅니다.

1. 갤러리에서 **HTTP 수신기**를 추가하고 **HTTP 응답 보내기** 동작을 선택합니다.
2. _응답 내용_을 "파이프라인 처리 완료됨"으로 설정하고 _응답 상태 코드_를 "200"으로 설정하여 HTTP 200 OK를 나타내고 조건을 ```@equals(actions('servicebusconnector').status,'Succeeded')``` 식으로 설정합니다.
	
위의 단계를 반복하여 실패한 경우도 HTTP 응답을 보냅니다. 조건을 ```@not(equals(actions('servicebusconnector').status,'Succeeded')).```로 변경합니다.


## 완료
누군가가 HTTP 끝점에 메시지를 보낼 때마다 앱이 트리거되고 방금 만든 작업이 실행됩니다. 앞서 만든 논리 앱을 관리하려면 Azure 관리 포털에서 **찾아보기**를 클릭한 후 **논리 앱**을 클릭합니다. 자세한 내용을 보려면 앱을 클릭합니다.


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG


<!--HONumber=54-->