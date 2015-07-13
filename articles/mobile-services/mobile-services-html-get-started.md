<properties
	pageTitle="HTML 5 앱에 대해 Azure 모바일 서비스 시작"
	description="이 자습서에 따라 HTML 개발을 위해 Azure 모바일 서비스 사용을 시작할 수 있습니다."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="ggailey777"/>


# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 자습서에서는 Azure 모바일 서비스를 사용하는 HTML 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법에 대해 설명합니다. 이 자습서에서는 새로운 모바일 서비스 및 이 서비스에 앱 데이터를 저장하는 간단한 <em>To do list</em> 앱을 만들게 됩니다. 오른쪽에 있는 클립을 클릭하여 이 자습서의 동영상 버전을 볼 수 있습니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a> <span class="time">3:51</span></div>
</div>

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

먼저 이 자습서를 완료해야만 HTML 앱에 대한 다른 모든 모바일 서비스 자습서를 학습할 수 있습니다.

> [AZURE.IMPORTANT]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank)을 참조하십시오.

###추가 요구 사항

+ 이 자습서를 학습하려면 로컬 컴퓨터에 다음 웹 서버 중 하나가 실행되고 있어야 합니다.

	+  **Windows**: IIS Express IIS Express는 [Microsoft 웹 플랫폼 설치 관리자]를 통해 설치됩니다.
	+  **MacOS X**: Python(미리 설치되어 있어야 함)
	+  **Linux**: Python [Python의 최신 버전]을 설치해야 합니다.

	앱을 호스트하기 위한 웹 서버로 어떤 것이든 사용할 수 있지만, 다운로드한 스크립트는 위의 웹 서버에서만 지원됩니다.

+ HTML5를 지원하는 웹 브라우저.


## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 HTML 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 퀵 스타트를 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결할 새로운 HTML 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.


2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **Create a new HTML app**을 확장합니다.

   	![][6]

   	모바일 서비스에 연결된 HTML 앱을 만들고 호스트하는 간편한 3단계가 표시됩니다.

  	![][7]

3. **TodoItems 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

4. **Download and run your app** 아래에서 **다운로드**를 클릭합니다.

  	모바일 서비스와 연결되는 샘플 _To do list_ 응용 프로그램용 웹 사이트 파일이 다운로드됩니다. 압축 파일을 로컬 컴퓨터에 저장하고 저장 위치를 적어둡니다.

5. **구성** 탭의 **CORS(크로스-원본 자원 공유)** 아래에 있는 **호스트 이름의 요청 허용** 목록에 `localhost`이(가) 이미 나열되어 있는지 확인합니다. 나열되어 있지 않으면 **호스트 이름** 필드에 `localhost`을(를) 입력하고 **저장**을 클릭합니다.

  	![][9]

	> [AZURE.IMPORTANT]localhost 이외의 웹 서버에 빠른 시작 앱을 배포하는 경우, 웹 서버 호스트 이름을 **호스트 이름의 요청 허용** 목록에 추가해야 합니다. 자세한 내용은 [크로스-원본 자원 공유(CORS)](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank)를 참조하십시오.

## HTML 앱 호스트 및 실행

이 자습서의 마지막 단계는 새로운 앱을 로컬 컴퓨터에 호스트하고 실행하는 것입니다.

1. 압축된 프로젝트 파일을 저장한 위치로 이동하고, 컴퓨터에 파일의 압축을 풀고, **서버** 하위 폴더에서 다음 명령 파일 중 하나를 실행합니다.

	+ **launch-windows**(Windows 컴퓨터)
	+ **launch-mac.command**(Mac OS X 컴퓨터)
	+ **launch-linux.sh**(Linux 컴퓨터)

	> [AZURE.NOTE]Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 `R`을(를) 입력하세요. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.

	새로운 앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

2. 웹 브라우저에서 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>을 열어 앱을 시작합니다.

3. 앱에서 _Enter new task_에 **자습서 완료**와 같은 의미 있는 텍스트를 입력하고 **추가**를 클릭합니다.

   	![][10]

   	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 모바일 서비스에서 반환되고 그 데이터가 앱의 두 번째 열에 표시됩니다.

	> [AZURE.NOTE]모바일 서비스에 액세스하는 코드를 검토하여 데이터를 쿼리 및 삽입할 수 있습니다(app.js 파일에 있음).

4. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

   	![][11]

   	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

   	![][12]

## <a name="next-steps"> </a>다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* **[데이터 시작하기]** <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보십시오.

* **[HTML 응용 프로그램에서 사용자 지정 API 호출]** <br/>HTML 응용 프로그램을 모바일 서비스에서 호스트되는 사용자 지정 API와 연결하세요.

* **[인증 시작하기]** <br/>ID 공급자로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

* **[모바일 서비스 HTML/JavaScript 방법 개념 참조]** <br/>HTML/JavaScript와 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보십시오.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[데이터 시작하기]: /develop/mobile/tutorials/get-started-with-data-html
[인증 시작하기]: /develop/mobile/tutorials/get-started-with-users-html
[HTML 응용 프로그램에서 사용자 지정 API 호출]: mobile-services-html-call-custom-api.md

[Management Portal]: https://manage.windowsazure.com/
[Microsoft 웹 플랫폼 설치 관리자]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[Python의 최신 버전]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[모바일 서비스 HTML/JavaScript 방법 개념 참조]: /develop/mobile/how-to-guides/work-with-html-js-client
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/windowsazure/dn155871.aspx
 

<!---HONumber=July15_HO1-->