<properties 
	pageTitle="OneDrive 커넥터"
	description="OneDrive 커넥터 시작"
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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# 논리 앱에서 OneDrive 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. 

OneDrive 커넥터를 사용하면 OneDrive 계정에서 파일을 업로드\다운로드\삭제할 수 있습니다.

## 논리 앱용 OneDrive 커넥터 만들기 ##
OneDrive 커넥터를 사용하려면 먼저 OneDrive 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > Azure 마켓플레이스"로 이동하여 "OneDrive 커넥터"를 검색합니다.
3.	다음과 같이 OneDrive 커넥터를 구성합니다.
 
	![][1] 
	- **이름** - OneDrive 커넥터의 이름을 지정합니다.
	- **앱 서비스 계획** - 앱 서비스 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.

4. 만들기를 클릭합니다. 새 OneDrive 커넥터가 만들어집니다.
5. API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 OneDrive 커넥터를 사용할 수 있습니다.

## 논리 앱에서 OneDrive 커넥터 사용 ##
API 앱을 만들고 나면 이제 OneDrive 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 OneDrive 커넥터가 있는 동일한 리소스 그룹을 선택합니다. 지침에 따라 [새 논리 앱 만들기].  	
	
2.	만들어진 논리 앱 내에서 "트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.  	
	
3.	OneDrive 커넥터가 오른쪽의 갤러리에 있는 "이 리소스 그룹의 API 앱" 섹션에 나타납니다.
 
	![][2]
4.	"OneDrive 커넥터"를 클릭하여 OneDrive 커넥터 API 앱을 편집기에 놓을 수 있습니다. 권한 부여 단추를 클릭합니다. Microsoft 자격 증명을 제공합니다(자동 로그인하지 않는 경우). 액세스를 허용하려면 "예"를 클릭합니다.
 
	![][3]
	![][4]
	
5.	이제 흐름에서 OneDrive 커넥터를 사용할 수 있습니다. 현재 트리거는 OneDrive 커넥터 내에서 사용할 수 없습니다. 작업은 사용할 수 있습니다(파일 가져오기, 파일 업로드, 파일 삭제 및 파일 나열).
 
	![][5]

6.	"파일 업로드" 경험을 안내하려고 합니다. "파일 업로드" OneDrive 동작을 사용하여 OneDrive 계정으로 파일을 업로드할 수 있습니다.
 
	![][6]

	다음과 같이 "파일 업로드" 동작에 대한 입력 속성을 구성합니다.

 - **파일 경로** - 업로드할 파일 경로를 지정합니다.
 - **Content(내용)** - 업로드할 파일의 내용을 지정합니다.
 - **Content Transfer Encoding(콘텐츠 전송 인코딩)** - none(없음) 또는 base64를 지정합니다.
 - **덮어쓰기** - 이미 있는 경우 "true"로 지정하여 파일을 덮어씁니다. 이는 고급 속성입니다.

7. 이러한 속성이 구성되면 "파일 업로드" 동작이 구성되며 흐름에서 사용할 수 있습니다. 마찬가지로 다른 동작도 구성할 수 있습니다.

8. 논리 앱 외부에서 커넥터를 사용하려면 커넥터를 통해 노출되는 REST API를 활용할 수 있습니다. 탐색->Api 앱->OneDrive 커넥터를 사용하여 이 API 정의를 볼 수 있습니다. 이제 통계 섹션 아래의 API 정의 렌즈를 클릭하여 이 커넥터를 통해 노출되는 모든 API를 볼 수 있습니다.

	![][7]

9. API의 세부 정보는 [OneDrive API 정의]에서 찾을 수 있습니다.

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[새 논리 앱 만들기]: app-service-logic-create-a-logic-app.md
[OneDrive API 정의]: https://msdn.microsoft.com/ko-kr/library/dn974227.aspx


<!--HONumber=52--> 