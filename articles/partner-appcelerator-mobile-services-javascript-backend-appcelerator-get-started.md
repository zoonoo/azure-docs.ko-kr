<properties pageTitle="Appcelerator Titanium에 대해 Azure 모바일 서비스 시작" metaKeywords="" description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 Appcelerator 개발을 시작할 수 있습니다. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서에서는 Appcelerator가 빌드된 응용 프로그램에서 Azure 모바일 서비스를 활용하는 방법을 보여 줍니다.

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다.

이 자습서를 완료하려면 [Appcelerator Titanium]이 필요합니다.

iOS용으로 빌드할 경우 Xcode 5.1 및 iOS 7.1 SDK 이상 버전도 필요합니다. 

Android용으로 빌드할 경우 Android 4.3 이상 SDK도 필요합니다.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 새 Appcelerator 앱 만들기

1. 모바일 서비스 포털에서 모바일 서비스의 **데이터** 탭을 선택합니다.

2. **테이블 추가**를 클릭하고 **TodoItem**이라는 새 테이블을 만듭니다.

3. 새 new Appcelerator [Todolist 응용 프로그램]을 다운로드하고 프로젝트의 압축을 풉니다.

4. 아직 하지 않은 경우 [Appcelerator Titanium Studio][Appcelerator Titanium] 및 SDK v3.2.1 이상을 다운로드하여 설치합니다. 프로젝트를 실행하려면 Xcode(v 5.0 +) 및/또는 Android SDK(v 4.3+)도 필요합니다.

5. 다시 모바일 서비스 포털의 **대시보드**에서 **키 관리**를 선택하고 **응용 프로그램 키**를 복사합니다.

5. 앱의 index.js에서 `<---App Name---->` 및 `<------------APP KEY------------>`를 모바일 서비스의 값으로 바꿉니다.

## 새 Appcelerator 앱 실행 ##

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1. Titanium Studio를 열고 **File -> Import**로 이동하여 이전에 다운로드한 프로젝트를 가져옵니다.

    ![][0]

2.	다음 화면에서 **Existing Projects into Workspace**를 선택하고 **Next**를 클릭합니다.

    ![][1]

3.	프로젝트 선택 화면에서 Browse 옵션을 사용하여 Azure 관리 포털에서 다운로드한 Azure Titanium 프로젝트를 찾습니다.

    ![][2]

4.	마지막으로 프로젝트 섹션에서 선택한 프로젝트를 표시합니다. 선택적으로 &quot;Copy projects into workspace&quot; 옵션을 선택하면 다운로드된 프로젝트를 작업 영역으로 복사할 수 있습니다. 마지막으로 Finish를 클릭하여 Titanium Studio에서 프로젝트를 엽니다.

    ![][3]

5.	프로젝트를 실행할 플랫폼(iOS/Android)을 선택합니다.

    ![][4]

6.	Run 단추를 눌러 프로젝트를 빌드하고 iPhone 시뮬레이터나 Android 에뮬레이터에서 앱을 시작합니다.

7.	Azure 관리 포털 설정에 따라 항목을 선택합니다.

    ![][5]

8.	다음 화면에서 더하기(+) 아이콘을 클릭하고 의미 있는 전체 텍스트(예: &quot;이 자습서 완료&quot;)를 입력하고 Save 단추를 클릭합니다.<br />

    ![][6]

    ![][7]

Microsoft Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

![][8]


>[WACOM.NOTE] 모바일 서비스에 액세스하는 코드를 검토하여 데이터를 쿼리 및 삽입할 수 있습니다(TodoService.m 파일에 있음).

4. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

   	![][9]

   	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

   	![][10]


## <a name="next-steps"> </a>다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다. 

* [인증 시작(영문)]
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아보세요.



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Todolist 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[인증 시작(영문)]: /ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push

<!--HONumber=35.1-->
