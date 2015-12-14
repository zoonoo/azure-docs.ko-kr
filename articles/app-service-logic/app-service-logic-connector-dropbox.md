<properties
	pageTitle="논리 앱에서 Dropbox 커넥터 사용 | Microsoft Azure 앱 서비스"
	description="Dropbox 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="sameerch"/>

# Dropbox 커넥터 시작 및 논리 앱에 추가
Dropbox 계정에 연결하여 파일을 업로드하거나 다운로드합니다. 논리 앱은 다양한 데이터 원본을 기반으로 트리거되고 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. Dropbox 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 트리거 및 작업

트리거는 새로운 메시지 도착과 같은 특정 이벤트를 기반으로 새 인스턴스를 시작합니다. 동작은 새 메시지를 받은 후 해당 파일을 Dropbox에 업로드하는 것과 같은 결과입니다.

Dropbox 커넥터는 논리 앱에서 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. Dropbox 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
없음 | <ul><li>파일 삭제</li><li>파일 가져오기</li><li>파일 업로드</li><li>파일 나열</li></ul>


## 논리 앱용 Dropbox 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Dropbox 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다. ![][1]
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **앱 서비스 계획** – 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Dropbox 커넥터의 이름을 지정합니다.  
4. **만들기**를 선택합니다.


## 논리 앱에서 Dropbox 커넥터 사용
API 앱을 만들고 나면 이제 Dropbox 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 다음을 수행합니다.

1.	논리 앱에서 **트리거 및 동작**을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. ![][3]
2.	갤러리에 Dropbox 커넥터가 나열됩니다. ![][4]
3.	Dropbox 커넥터를 선택하면 자동으로 디자이너에 추가됩니다. **권한 부여**를 선택하고 자격 증명을 입력한 후 **허용**을 선택합니다. ![][5] ![][6] ![][7]

이제 흐름에서 Dropbox 커넥터를 사용할 수 있습니다. "파일 업로드" Dropbox 동작을 사용하여 Dropbox 계정으로 파일을 업로드할 수 있습니다. ![][8] ![][9]

다음과 같이 "파일 업로드" 작업에 대한 입력 속성을 구성합니다.

- **파일 경로** - 업로드할 대상 Dropbox 파일 경로를 지정합니다. 예: Photos/image.png
- **콘텐츠** - 업로드할 파일의 내용을 지정합니다. 종종 논리 앱에서 이전 단계에서 가져오게 됩니다.
- **콘텐츠 전송 인코딩** - none(없음) 또는 base64를 지정합니다.
- **덮어쓰기** - 이미 있는 경우 "true"로 지정하여 파일을 덮어씁니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=AcomDC_1203_2015-->