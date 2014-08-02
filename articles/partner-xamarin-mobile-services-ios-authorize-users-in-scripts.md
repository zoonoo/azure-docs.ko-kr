<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-ios" urlDisplayName="Authorize Users in Scripts (Xamarin.iOS)" pageTitle="Authorize users in scripts (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.iOS scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" documentationCenter="Mobile" authors="" />

스크립트를 사용하여 모바일 서비스에서 사용자 인증
=================================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios "iOS")[Android](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android "Android")[HTML](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android "Xamarin.Android")

이 항목에서는 서버 스크립트를 사용하여 인증된 사용자에게 Xamarin.iOS 앱에서 Azure 모바일 서비스의 데이터에 액세스할 수 있는 권한을 부여하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스에 스크립트를 등록하여 인증된 사용자의 userId를 기반으로 쿼리를 필터링함으로써 각 사용자가 자신의 고유 데이터만 볼 수 있도록 만듭니다.

이 자습서는 모바일 서비스 빠른 시작을 기반으로 하며 이전 자습서인 [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)에 내용을 추가했습니다. 이 자습서를 시작하기 전에 먼저 [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)을 완료해야 합니다.

스크립트 등록
-------------

빠른 시작 앱은 데이터를 읽고 삽입하기 때문에 TodoItem 테이블에 대해 이 작업을 수행하는 스크립트를 등록해야 합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][0]

2.  **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][1]

3.  **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

   	![][2]

4.  기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

    이 스크립트는 항목에 userId 값을 추가합니다. 이 값은 TodoItem 테이블에 삽입되기 전의 인증된 사용자의 사용자 ID입니다.

    **참고**

    이 삽입 스크립트를 처음 실행할 때는 동적 스키마를 사용하도록 설정해야 합니다. 동적 스키마를 사용하도록 설정하면 첫 번째 실행 시 모바일 서비스가 **userId** 열을 **TodoItem** 테이블에 자동으로 추가합니다. 동적 스키마는 기본적으로 새 모바일 서비스에 대해 사용하도록 설정되어 있으며 Windows 스토어에 앱을 게시하기 전에 이 스키마를 사용하지 않도록 설정해야 합니다.

5.  3단계와 4단계를 반복하여 기존 **Read** 작업을 다음 함수로 바꿉니다.

        function read(query, user, request) {
            query.where({ userId: user.userId});    
            request.execute();
         }

   	이 스크립트는 반환된 TodoItem 개체를 필터링하여 각 사용자가 자신이 삽입한 항목만 수신하도록 합니다.

앱 테스트
---------

1.  Xamarin Studio 또는 Visual Studio에서 [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios) 자습서를 완료할 때 수정한 프로젝트를 엽니다.

2.  **실행** 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작한 후 선택한 ID 공급자로 로그온합니다.

   	이전 자습서의 TodoItem 테이블에 이미 항목이 있지만, 이번에는 항목이 반환되지 않습니다. 이전 항목이 userId 열 없이 삽입되어 지금 null 값을 가지기 때문에 이런 현상이 발생합니다.

3.  앱에서 **Insert a TodoItem**에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][3]

   	모바일 서비스에서 TodoItem 테이블의 텍스트 및 userId가 모두 삽입됩니다. 새 항목에 올바른 userId 값이 있기 때문에 값이 모바일 서비스에서 반환되어 두 번째 열에 표시됩니다.

4.  [관리 포털](https://manage.windowsazure.com/)로 돌아와 **todoitem** 테이블에서 **찾아보기**를 클릭하고 새로 추가된 각 항목에 이제 관련 userId 값이 있는지 확인합니다.

5.  (옵션) 추가 로그인 계정이 있는 경우 앱을 닫았다가 다시 실행하여 사용자가 자신의 고유 데이터만 볼 수 있는지 여부를 확인할 수 있습니다. 로그인 자격 증명 대화 상자가 표시되면 다른 로그인을 입력한 후 이전 계정으로 입력한 항목이 표시되지 않는지 확인합니다.

다음 단계
---------

이제 인증 관련 작업의 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)
    <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보십시오.

-   [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios)
    <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png