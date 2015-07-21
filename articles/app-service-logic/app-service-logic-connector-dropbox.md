<properties 
	pageTitle="Dropbox 커넥터"
	description="Dropbox 커넥터 시작"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# 논리 앱에서 Dropbox 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

Dropbox 커넥터를 사용하면 Dropbox 계정에서 파일을 업로드 또는 다운로드할 수 있습니다.

## 논리 앱용 Dropbox 커넥터 만들기 ##
Dropbox 커넥터를 사용하려면 먼저 Dropbox 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	“웹 및 모바일 --> Azure 마켓플레이스”로 이동하여 “Dropbox 커넥터”를 검색합니다.
3.	다음과 같이 Dropbox 커넥터를 구성합니다.
 
	![][1]
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들기 원하는 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **앱 서비스 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Dropbox 커넥터에 이름을 부여합니다.
4. 만들기를 클릭합니다. 새 Dropbox 커넥터가 만들어집니다.
5. API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 Dropbox 커넥터를 사용할 수 있습니다.

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

다음과 같이 "파일 업로드" 동작에 대한 입력 속성을 구성합니다.

- **파일 경로** - 업로드할 대상 Dropbox 파일 경로를 지정합니다. 예: Photos/image.png
- **콘텐츠** - 업로드할 파일의 내용을 지정합니다. 종종 논리 앱에서 이전 단계에서 가져오게 됩니다.
- **콘텐츠 전송 인코딩** - none(없음) 또는 base64를 지정합니다.
- **덮어쓰기** - 이미 있는 경우 "true"로 지정하여 파일을 덮어씁니다.


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
 

<!-----HONumber=62-->