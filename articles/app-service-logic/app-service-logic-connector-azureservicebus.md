<properties
   pageTitle="논리 앱에서 Azure 서비스 버스 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Azure 서비스 버스 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="rajram"/>


# Azure 서비스 버스 커넥터 시작 및 논리 앱에 추가 
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다. 2015-08-01-preview 스키마 버전에 대한 내용을 보려면 [서비스 버스 API](../connectors/connectors-create-api-servicebus.md)를 클릭하세요.

Azure 서비스 버스에 연결하여 큐 및 토픽에 메시지를 보내고 큐 및 구독에서 메시지를 받습니다. 커넥터는 논리 앱에서 "워크플로"의 일부로 사용됩니다.

## 트리거 및 작업
트리거는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. 동작은 트리거의 결과입니다. 예를 들어 주문 또는 새 메시지가 큐에 대기되면 경고 또는 메시지를 보냅니다.

Azure 서비스 버스 커넥터는 논리 앱에서 트리거나 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다.

Azure 서비스 버스 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
메시지 사용 가능 | 메시지 보내기

## Azure 서비스 버스 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. “Azure 서비스 버스 커넥터”를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.

	![][1]

4. 다음 패키지 설정을 입력합니다.

	이름 | 설명
--- | ---
연결 문자열 | Azure 서비스 버스에 대한 연결 문자열입니다. 예를 들어 다음을 입력합니다. *Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]*.
엔터티 이름 | 큐 또는 항목의 이름을 입력합니다.
구독 이름 | 메시지를 받을 구독의 이름을 입력합니다.

5. **만들기**를 클릭합니다.

## 논리 앱에서 서비스 버스 커넥터 사용
커넥터를 만들고 나면 이제 Azure 서비스 버스 커넥터를 논리 앱에 대한 트리거 또는 동작으로 사용할 수 있습니다. 다음을 수행합니다.

1.	새 논리 앱을 만들고 Azure 서비스 버스 커넥터가 있는 동일한 리소스 그룹을 선택합니다.

	![][2]

2.	“트리거 및 동작”을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.

	![][3]

3. Azure 서비스 버스 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다.

	![][4]

4. “Azure 서비스 버스 커넥터”를 클릭하여 Azure 서비스 버스 커넥터를 편집기에 놓을 수 있습니다.

5.	이제 워크플로에서 Azure 서비스 버스 커넥터를 사용할 수 있습니다. 흐름의 다른 동작에서 Azure 서비스 버스 트리거("메시지 사용 가능")로부터 검색된 새 메시지를 사용할 수 있습니다.

	![][5]

	![][6]

Azure 서비스 버스 "메시지 보내기" 동작을 사용할 수도 있습니다.

	![][7]

![][8]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=AcomDC_0803_2016-->