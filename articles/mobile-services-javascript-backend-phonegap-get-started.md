<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

# <a name="getting-started"> </a>모바일 서비스 시작

이 자습서에서는 Azure 모바일 서비스를 사용하는 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법에 대해 설명합니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다.

완성된 앱의 스크린샷은 다음과 같습니다.

![][]

### <a name="additional-requirements"></a>추가 요구 사항

이 자습서를 완료하려면 PhoneGap 도구가 필요합니다(Windows Phone 8 프로젝트에는 v3.2+ 필요).

PhoneGap은 다중 플랫폼용 개발을 지원합니다. PhoneGap 도구 자체 외에도 각각의 대상 플랫폼에 맞는 도구를 설치해야 합니다.

-   Windows Phone: [Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone] 설치
-   iOS: [Xcode][Xcode] 설치(v4.4+ 필요)
-   Android: [Android Developer Tools][Android Developer Tools]
     설치(Android용 모바일 서비스 SDK는 Android 2.2 이상용 앱을 지원합니다. 빠른 시작 앱을 실행하려면 Android 4.2 이상이 필요합니다.)

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## <span class="short-header">새 앱 만들기</span>새 PhoneGap 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 PhoneGap 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭의 **플랫폼 선택** 아래에서 **PhoneGap**을 클릭하고 **새 PhoneGap 앱 만들기**를 확장합니다.

    ![][1]

    그러면 모바일 서비스에 연결된 PhoneGap 앱을 만들기 위한 쉬운 3단계가 표시됩니다.

    ![][2]

3.  아직 하지 않은 경우 PhoneGap 및 플랫폼 개발 도구(Windows Phone, iOS 또는 Android) 중 적어도 하나를 다운로드하여 설치합니다.

4.  **TodoItems 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5.  **Download and run your app** 아래에서 **다운로드**를 클릭합니다.

    모바일 서비스 JavaScript SDK와 함께 모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 새 PhoneGap 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1.  압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풉니다.

2.  각 플랫폼에 대한 아래의 지침에 따라 프로젝트를 열고 실행합니다.

    -   **Windows Phone 8**

    1.  Windows Phone 8: Visual Studio 2012 Express for Windows Phone의 **platforms\\wp8** 폴더에서 .sln 파일을 엽니다.

    2.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

        ![][3]

    -   **iOS**

    1.  Xcode의 **platforms/ios** 폴더에서 프로젝트를 엽니다.

    2.  **실행** 단추를 눌러 프로젝트를 빌드하고 이 프로젝트의 기본값인 iPhone 에뮬레이터에서 앱을 시작합니다.

        ![][]

    -   **Android**

        1.  Eclipse에서 **File**과 **Import**를 클릭하고 **Android**를 확장한 후 **Existing Android Code into Workspace**를 클릭한 다음 **Next**를 클릭합니다.

        2.  **Browse**를 클릭하고 압축을 푼 프로젝트 파일 위치로 이동한 후 **OK**를 클릭합니다. TodoActivity 프로젝트가 선택되었는지 확인하고 **Finish**를 클릭합니다.

            현재 작업 영역에 프로젝트 파일을 가져옵니다.

            </p>
        3.  **Run** 메뉴에서 **Run**을 클릭하여 Android 에뮬레이터에서 프로젝트를 시작합니다.

            ![][4]

        > [WACOM.NOTE]Android 에뮬레이터에서 프로젝트를 실행할 수 있으려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.

3.  위의 모바일 에뮬레이터 중 하나에서 앱을 시작한 후 텍스트 상자에 텍스트를 입력하고 **추가**를 클릭합니다.

    Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 **TodoItem** 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

    <div class="dev-callout">

    **중요**
    PhoneGap 도구로 기본 프로젝트를 다시 빌드하는 경우 이 플랫폼 프로젝트에 대한 변경 사항을 덮어쓰게 됩니다. 대신 아래의 섹션에서 설명하는 것처럼 프로젝트의 루트 www 디렉터리에서 변경하십시오.

    </div>

4.  관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

    ![][5]

    이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

    ![][6]

## 앱 업데이트 및 각 플랫폼용 프로젝트 다시 빌드

1.  ´www´ 디렉터리(이 경우 ´todolist/www´)의 코드 파일을 변경합니다.

2.  시스템 경로에서 모든 대상 플랫폼 도구에 액세스할 수 있는지 확인합니다.

3.  루트 프로젝트 디렉터리에서 명령 프롬프트를 열고 다음의 플랫폼별 명령 중 하나를 실행합니다.

    -   **Windows Phone**

        Visual Studio 개발자 명령 프롬프트에서 다음 명령을 실행합니다.

            phonegap local build wp8

    -   **iOS**

        터미널을 열고 다음 명령을 실행합니다.

            phonegap local build ios

    -   **Android**

        명령 프롬프트 또는 터미널 창을 열고 다음 명령을 실행합니다.

            phonegap local build android

4.  이전 섹션에서 설명한 것처럼 적절한 개발 환경에서 각 프로젝트를 엽니다.

> [WACOM.NOTE]모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 js/index.js 파일에 있습니다.

## <a name="next-steps"> </a>다음 단계

이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작][인증 시작]
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

<!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  []: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Android Developer Tools]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
  [2]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
  [3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
  [4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png
  [5]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-html-get-started-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-html-get-started-users
