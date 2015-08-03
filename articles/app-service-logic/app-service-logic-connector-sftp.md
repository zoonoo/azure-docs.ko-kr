<properties
	pageTitle="SFTP 커넥터"
	description="SFTP 커넥터 시작"
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
	ms.date="07/02/2015"
	ms.author="sameerch"/>

# 논리 앱에서 SFTP 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

SFTP 커넥터를 사용하면 SFTP 서버에서 또는 서버로 데이터를 이동할 수 있습니다. 파일을 다운로드하거나 SFTP 서버에서 또는 서버로 파일 또는 목록 파일을 업로드할 수 있습니다.

## 논리 앱용 SFTP 커넥터 만들기 ##
SFTP 커넥터를 사용하려면 먼저 SFTP 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > API 앱"으로 이동하여 "SFTP 커넥터"를 검색합니다.
3.	다음과 같이 SFTP 커넥터를 구성합니다.

	![][1] - **위치** - 커넥터를 배포할 지리적 위치를 선택합니다. - **구독** - 이 커넥터를 만들기 원하는 구독을 선택합니다. - **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다. - **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다. - **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다. - **이름** - SFTP 커넥터에 이름을 부여합니다.- **패키지 설정** - **서버 주소** - SFTP 서버 이름 또는 IP 주소를 지정합니다. - **모든 SSH 서버 호스트 키 허용** - 서버의 SSH 공개 호스트 키 지문을 허용할지 결정합니다. false로 설정할 경우 호스트 키가 “SSH 서버 호스트 키 지문" 속성에 지정된 키와 일치하게 지정됩니다. **SSH 서버 호스트 키** - SSH 서버에 대한 공개 호스트 키의 지문을 지정합니다. **루트 폴더** - 루트 폴더 경로를 지정합니다. - **암호화 암호** - 지정된 암호를 암호화합니다. - **서버 포트** - SFTP 서버 포트 번호를 지정합니다.
4. 만들기를 클릭합니다. 새 SFTP 커넥터가 만들어집니다.

5. 찾아보기 -> API 앱 -> <Name of the API App just created>을 통해 방금 만든 API 앱으로 이동하면 "보안" 구성 요소가 구성되지 않음을 볼 수 있습니다.

	![][2]
6. "보안" 구성 요소를 클릭하여 SFTP 커넥터에 대한 보안(사용자 이름, 암호, 개인 키, PPK 파일 암호)을 구성합니다. 보안에서 "Password" 또는 "Privatekey" 또는 "MutliFactor" 인증 탭을 선택하고 필요한 속성을 제공합니다.

	![][3] ![][4] ![][5]
6. 보안 구성이 저장되면 동일한 리소스 그룹의 논리 앱을 만들어 SFTP 커넥터를 사용할 수 있습니다.

## 논리 앱에서 SFTP 커넥터 사용 ##
API 앱을 만들고 나면 이제 SFTP 커넥터를 논리 앱에 대한 트리거/작업으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 SFTP 커넥터가 있는 동일한 리소스 그룹을 선택합니다.

	![][6]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.

	![][7]
3.	SFTP 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다.

	![][8]
4.	“SFTP 커넥터”를 클릭하여 SFTP 커넥터 API 앱을 편집기에 놓을 수 있습니다.


6.	이제 흐름에서 SFTP 커넥터를 사용할 수 있습니다. 흐름의 다른 작업에서 SFTP 트리거("TriggerOnFileAvailable")로부터 검색된 파일을 사용할 수 있습니다.

	**참고:** SFTP 트리거 "TriggerOnFileAvailable"은 파일을 처리한 후 검색된 파일을 삭제합니다.

8.	다음과 같이 SFTP 트리거에 대한 입력 속성을 구성합니다.

	- **폴더 경로** - 검색해야 하는 파일이 있는 폴더의 경로를 지정합니다.
	- **파일 형식: 텍스트 또는 이진 파일** - 파일 형식을 선택합니다.
	- **파일 마스크** - 파일을 검색하기 위해 적용할 파일 마스크를 지정합니다. '*'를 사용하면 지정된 폴더에 있는 모든 파일을 검색합니다.
	- **제외 파일 마스크** - 파일 제외에 적용되는 파일 마스크를 지정합니다. "파일 마스크" 속성도 설정한 경우 제외 파일 마스크가 먼저 적용됩니다.


	![][9] ![][10]

7.	유사한 방식으로 흐름에서 SFTP 작업을 사용할 수 있습니다. "파일 업로드" 작업을 사용하여 SFTP 서버에 파일을 업로드할 수 있습니다. 다음과 같이 "파일 업로드" 작업에 대한 입력 속성을 구성합니다.

	- **콘텐츠** - 업로드할 파일의 내용을 지정합니다.
	- **콘텐츠 전송 인코딩** - none 또는 base64를 지정합니다.
	- **파일 경로** - 업로드할 파일 경로를 지정합니다.
	- **덮어쓰기** - "true"로 지정하면 파일이 이미 있는 경우 덮어씁니다.
	- **있는 경우 추가** - "true" 또는 "false"로 지정합니다. "true"로 설정하면 파일이 이미 있는 경우 데이터가 파일에 추가됩니다. "false"로 설정하면 파일이 이미 있는 경우 덮어씁니다.- **임시 폴더** - 제공되는 경우, 어댑터가 해당 파일을 ‘임시 폴더 경로’로 업로드하고 업로드가 완료되면 ‘폴더 경로’로 이동됩니다. 이동 작업이 원자성을 지니도록 하려면 ‘임시 폴더 경로’가 ‘폴더 경로’와 동일한 실제 디스크에 있어야 합니다. 임시 폴더는 ‘있는 경우 추가’ 속성을 사용하지 않도록 설정한 경우에만 사용할 수 있습니다.

	![][11] ![][12]





<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!---HONumber=July15_HO4-->