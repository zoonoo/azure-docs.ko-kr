<properties
	pageTitle="PhoneGap/cordova 앱에 대한 Azure 모바일 서비스 시작 | Microsoft Azure"
	description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 iOS, Android 및 Windows Phone용 PhoneGap 개발을 시작할 수 있습니다."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="10/05/2015"
	ms.author="ggailey777"/>

# 모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

이 자습서에서는 Azure 모바일 서비스를 사용하는 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법에 대해 설명합니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다.

완성된 앱의 스크린샷은 다음과 같습니다.

![][3]

### 추가 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

+ PhoneGap 도구(Windows Phone 8 프로젝트의 경우 v3.2 이상 필요)

+ 활성 Microsoft Azure 계정.

+ PhoneGap은 다중 플랫폼용 개발을 지원합니다. PhoneGap 도구 자체 외에도 각각의 대상 플랫폼에 맞는 도구를 설치해야 합니다.

	- Windows Phone: [Visual Studio 2012 Express for Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) 설치
	- iOS: [Xcode] 설치(v4.4 이상 필요)
	- Android: [Android 개발자 도구][Android SDK] <br/>설치(Android용 모바일 서비스 SDK는 Android 2.2 이상용 앱을 지원합니다. 퀵 스타트 앱을 실행하려면 Android 4.2 이상이 필요합니다.)

## 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 PhoneGap 앱 만들기

이 섹션에서는 모바일 서비스에 연결된 새 PhoneGap 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 빠른 시작 탭의 **플랫폼 선택** 아래에서 **PhoneGap**을 클릭하고 **새 PhoneGap 앱 만들기**를 확장합니다.

   	![][0]

   	그러면 모바일 서비스에 연결된 PhoneGap 앱을 만들기 위한 쉬운 3단계가 표시됩니다.

  	![][1]

3. 아직 하지 않은 경우 PhoneGap 및 플랫폼 개발 도구(Windows Phone, iOS 또는 Android) 중 적어도 하나를 다운로드하여 설치합니다.

4. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5. **Download and run your app** 아래에서 **다운로드**를 클릭합니다.

	모바일 서비스 JavaScript SDK와 함께 모바일 서비스에 연결된 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 새 PhoneGap 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1.	압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풉니다.

2.	각 플랫폼에 대한 아래의 지침에 따라 프로젝트를 열고 실행합니다.

	+ **Windows Phone 8**

		1. Windows Phone 8: Visual Studio 2012 Express for Windows Phone의 **platforms\\wp8** 폴더에서 .sln 파일을 엽니다.

		2. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

	  	![][2]

	+ **iOS**

		1. Xcode의 **platforms/ios** 폴더에서 프로젝트를 엽니다.

		2. **실행** 단추를 눌러 프로젝트를 빌드하고 이 프로젝트의 기본값인 iPhone 에뮬레이터에서 앱을 시작합니다.

	  	![][3]

	+ **Android**

		1. Eclipse에서 **File**과 **Import**를 클릭하고 **Android**를 확장한 후 **Existing Android Code into Workspace**를 클릭한 다음 **Next**를 클릭합니다.

		2. **Browse**를 클릭하고 압축을 푼 프로젝트 파일 위치로 이동한 후 **OK**를 클릭합니다. TodoActivity 프로젝트가 선택되었는지 확인하고 **Finish**를 클릭합니다. <p>현재 작업 영역에 프로젝트 파일을 가져옵니다.</p>

		3. **Run** 메뉴에서 **Run**을 클릭하여 Android 에뮬레이터에서 프로젝트를 시작합니다.

			![][4]

		>[AZURE.NOTE]Android 에뮬레이터에서 프로젝트를 실행할 수 있으려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.


3. 위의 모바일 에뮬레이터 중 하나에서 앱을 시작한 후 텍스트 상자에 텍스트를 입력하고 **추가**를 클릭합니다.

	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 **TodoItem** 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

	> [AZURE.IMPORTANT]PhoneGap 도구로 기본 프로젝트를 다시 빌드하는 경우 이 플랫폼 프로젝트에 대한 변경 사항을 덮어쓰게 됩니다. 대신 아래의 섹션에서 설명하는 것처럼 프로젝트의 루트 www 디렉터리에서 변경하세요.

4. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)


## 앱 업데이트 및 각 플랫폼용 프로젝트 다시 빌드

1. ´www´ 디렉터리(이 경우 ´todolist/www´)의 코드 파일을 변경합니다.

2. 시스템 경로에서 모든 대상 플랫폼 도구에 액세스할 수 있는지 확인합니다.

2. 루트 프로젝트 디렉터리에서 명령 프롬프트를 열고 다음의 플랫폼별 명령 중 하나를 실행합니다.

	+ **Windows Phone**

		Visual Studio 개발자 명령 프롬프트에서 다음 명령을 실행합니다.

    		phonegap local build wp8

	+ **iOS**

		터미널을 열고 다음 명령을 실행합니다.

    		phonegap local build ios

	+ **Android**

		명령 프롬프트 또는 터미널 창을 열고 다음 명령을 실행합니다.

		    phonegap local build android

4. 이전 섹션에서 설명한 것처럼 적절한 개발 환경에서 각 프로젝트를 엽니다.

>[AZURE.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 js/index.js 파일에 있습니다.

## 다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* **[앱에 인증 추가]** ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.  

* **[앱에 푸시 알림 추가](https://msdn.microsoft.com/magazine/dn879353.aspx)** 앱에 등록하고 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* **[모바일 서비스 HTML/JavaScript 사용 방법 개념 참조](mobile-services-html-how-to-use-client-library.md)** JavaScript 클라이언트 라이브러리를 사용하여 데이터에 액세스하고, 사용자 지정 API를 호출하고, 인증을 수행하는 방법에 대해 자세히 알아봅니다.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[앱에 인증 추가]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Management Portal]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
 

<!---HONumber=Oct15_HO2-->