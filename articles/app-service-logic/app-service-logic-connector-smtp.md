<properties
   pageTitle="논리 앱에서 SMTP 커넥터 사용 | Microsoft Azure 앱 서비스"
	description="SMTP 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
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
	ms.date="08/23/2015"
	ms.author="andalmia"/>


# SMTP 커넥터 시작 및 논리 앱에 추가
SMTP 서버에 연결하고 첨부 파일이 있는 메일을 비롯한 메일을 보냅니다. SMTP 커넥터 "전자 메일 보내기" 동작을 사용하면 지정된 전자 메일 주소로 전자 메일을 보낼 수 있습니다.

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 워크플로의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. SMTP 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.


## 트리거 및 작업
*트리거*는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. *작업*은 트리거의 결과입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가될 때 새 고객에게 전자 메일이 전송됩니다.

SMTP 커넥터는 논리 앱에서 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. 현재 이 커넥터에 대한 트리거는 없습니다.

SMTP 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
없음 | 메일 보내기


## SMTP 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. **API 앱**을 선택하고 "SMTP 커넥터"를 검색합니다.
3. 커넥터를 **만듭니다**.
4. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
5. 다음 패키지 설정을 입력합니다.

	이름 | 필수 | 설명
	--- | --- | ---
	사용자 이름 | 예 | SMTP 서버에 연결할 수 있는 사용자 이름을 입력합니다.
	암호 | 예 | 사용자 이름 암호를 입력합니다.
	서버 주소 | 예 | SMTP 서버 이름 또는 IP 주소를 입력합니다.
	서버 포트 | 예 | SMTP 서버 포트 번호를 입력합니다.
	SSL 사용 | 아니요 | SSL/TLS 보안 채널을 통해 SMTP를 사용하려면 *true*를 입력합니다.

6. **만들기**를 선택합니다.

## 논리 앱에서 SMTP 커넥터 사용
커넥터를 만들고 나면 이제 SMTP 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 다음을 수행합니다.

1.	새 논리 앱을 만듭니다. 
	![][2]
2.	**트리거 및 동작**을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 
	![][3]
3.	SMTP 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 표시됩니다. 이를 선택합니다. 
	![][4]
4.	워크플로 디자이너에 자동으로 추가할 SMTP 커넥터를 선택합니다.

이제 워크플로에서 사용되도록 SMTP 커넥터를 구성할 수 있습니다. **메일 보내기** 동작을 선택하고 입력 속성을 구성합니다.

	속성 | 설명
	--- | ---
	받는 사람 | 받는 사람의 메일 주소를 입력합니다. 여러 개의 메일 주소는 세미콜론(;)으로 구분하면 됩니다. 예를 들어, recipient1@domain.com;recipient2@domain.com과 같이 입력할 수 있습니다.
	참조 | 참조에 포함할 사람의 메일 주소를 입력합니다. 여러 개의 메일 주소는 세미콜론(;)으로 구분하면 됩니다. 예를 들어, recipient1@domain.com;recipient2@domain.com과 같이 입력할 수 있습니다.
	제목 | 메일의 제목을 입력합니다.
	본문 | 메일의 본문을 입력합니다.
	HTML | 이 속성을 true로 설정하면 본문의 내용이 HTML로 전송됩니다.
	숨은 참조 | 숨은 참조에 포함할 사람의 메일 주소를 입력합니다. 여러 개의 메일 주소는 세미콜론(;)으로 구분하면 됩니다. 예를 들어, recipient1@domain.com;recipient2@domain.com과 같이 입력할 수 있습니다.
	중요도 | 메일의 중요도를 입력합니다. 보통, 낮음, 높음 중에서 선택할 수 있습니다.
	첨부 파일 | 첨부 파일이 메일과 함께 전송되며, 다음과 같은 필드가 포함되어 있습니다: <ul><li>콘텐츠(문자열)</li><li>콘텐츠 전송 인코딩(열거형) (“none”|”base64”)</li><li>콘텐츠 유형(문자열)</li><li>콘텐츠 ID(문자열)</li><li>파일 이름(문자열)</li></ul>

![][5]
![][6]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!-----HONumber=August15_HO9-->