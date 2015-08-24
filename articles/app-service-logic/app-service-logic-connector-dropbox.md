<properties
	pageTitle="Azure 앱 서비스의 논리 앱에 Dropbox 커넥터 사용"
	description="앱 서비스의 논리 앱에 Dropbox 커넥터를 시작하는 방법"
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
	ms.date="08/09/2015"
	ms.author="sameerch"/>

# Dropbox 커넥터

Dropbox 커넥터를 사용하면 Dropbox 계정에서 파일을 업로드 또는 다운로드할 수 있습니다.

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

## 트리거 및 작업

트리거는 새로운 메시지 도착과 같은 특정 이벤트를 기반으로 새 인스턴스를 시작합니다. 동작은 새 메시지를 받은 후 해당 파일을 Dropbox에 업로드하는 것과 같은 결과입니다.

Dropbox 커넥터는 논리 앱에서 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. Dropbox 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
없음 | <ul><li>파일 삭제</li><li>파일 가져오기</li><li>파일 업로드</li><li>파일 나열</li></ul>


## 논리 앱용 Dropbox 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	“웹 및 모바일 --> Azure 마켓플레이스”로 이동하여 “Dropbox 커넥터”를 검색합니다.
3.	다음과 같이 Dropbox 커넥터를 구성합니다.
 
	![][1]
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **앱 서비스 계획** – 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Dropbox 커넥터의 이름을 지정합니다.  
4. **만들기**를 선택합니다.


## 논리 앱에서 Dropbox 커넥터 사용 ##
API 앱을 만들고 나면 이제 Dropbox 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Dropbox 커넥터가 있는 동일한 리소스 그룹을 선택합니다.

	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.

	![][3]
3.	Dropbox 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다.

	![][4]
4.	“Dropbox 커넥터”를 클릭하여 Dropbox 커넥터 API 앱을 편집기에 놓을 수 있습니다. 권한 부여 단추를 클릭합니다. Dropbox 자격 증명을 제공합니다. “허용”을 클릭합니다.
 
	![][5]
	![][6]
	![][7]
6.	이제 흐름에서 Dropbox 커넥터를 사용할 수 있습니다. "파일 업로드" Dropbox 동작을 사용하여 Dropbox 계정으로 파일을 업로드할 수 있습니다.
 
	![][8]
	![][9]

다음과 같이 "파일 업로드" 작업에 대한 입력 속성을 구성합니다.

- **파일 경로** - 업로드할 대상 Dropbox 파일 경로를 지정합니다. 예: Photos/image.png
- **콘텐츠** - 업로드할 파일의 내용을 지정합니다. 종종 논리 앱에서 이전 단계에서 가져오게 됩니다.
- **콘텐츠 전송 인코딩** - none(없음) 또는 base64를 지정합니다.
- **덮어쓰기** - 이미 있는 경우 "true"로 지정하여 파일을 덮어씁니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

REST API를 사용하여 API 앱을 만듭니다. [커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)를 참조하세요.

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

<!---HONumber=August15_HO7-->