<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에서 인증 시작
===========================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

이 항목에서는 HTML 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성](#register)
2.  [테이블 사용 권한을 인증된 사용자로 제한](#permissions)
3.  [앱에 인증 추가](#add-authentication)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-html) 자습서를 완료해야 합니다.

앱 등록인증을 위해 앱 등록 및 모바일 서비스 구성
------------------------------------------------

사용자를 인증할 수 있으려면 ID 공급자에 앱을 등록해야 합니다. 그런 다음 공급자가 생성한 클라이언트 암호를 모바일 서비스에 등록해야 합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

     ![](./media/mobile-services-html-get-started-users/mobile-services-selection.png)

2.  **대시보드** 탭을 클릭하고 **모바일 서비스 URL** 값을 기록해 둡니다.

     ![](./media/mobile-services-html-get-started-users/mobile-service-uri.png)

    앱을 등록할 때 이 값을 ID 공급자에게 제공해야 할 수도 있습니다.

3.  아래 목록에서 지원되는 ID 공급자를 선택하고 해당 공급자에 앱을 등록하는 단계를 따릅니다.

	-   [Microsoft 계정](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Facebook 로그인](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Twitter 로그인](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Google 로그인](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	공급자가 생성한 클라이언트 ID 및 암호 값을 기록해 두어야 합니다.

    	**보안 정보**

    	공급자가 생성한 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마십시오.

1.  관리 포털로 돌아가서 **ID** 탭을 클릭하고 ID 공급자로부터 받은 앱 ID 및 공유 암호 값을 입력한 후 **저장**을 클릭합니다.

     ![](./media/mobile-services-html-get-started-users/mobile-identity-tab.png)

이제 모바일 서비스와 앱이 선택한 인증 공급자를 사용하도록 둘 다 구성되었습니다.

사용 권한 제한사용 권한을 인증된 사용자로 제한
----------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png)

2.  **사용 권한** 탭을 클릭하고 모든 사용 권한을 **인증된 사용자만**으로 설정한 후 **저장**을 클릭합니다. 그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다. 또한 익명 사용자의 가능성을 허용할 필요가 없으므로 다음 자습서의 스크립트가 간소화됩니다.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png)

3.  앱 디렉터리의 **server** 하위 폴더에서 다음 명령 파일 중 하나를 시작합니다.

    -   **launch-windows**(Windows 컴퓨터)
    -   **launch-mac.command**(Mac OS X 컴퓨터)
    -   **launch-linux.sh**(Linux 컴퓨터)

    **참고**

    Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 'R'을 입력하십시오. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.

    새로운 앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

4.  웹 브라우저에서 URL <http://localhost:8000/>을 열어 앱을 시작합니다.

    데이터가 로드되지 않습니다. 이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

5.  (옵션) 사용하는 웹 브라우저용 스크립트 디버거를 열고 페이지를 다시 로드합니다. 액세스 거부 오류가 발생하는지 확인합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 인증을 허용하도록 앱을 업데이트합니다.

인증 추가앱에 인증 추가
-----------------------

**참고**

팝업에서 로그인이 이루어지기 때문에 단추 클릭 이벤트에서 **login** 메서드를 호출해야 합니다. 그렇지 않으면 많은 브라우저에서 로그인 창이 표시되지 않습니다.

1.  프로젝트 파일 index.html을 열고 H1 요소를 찾은 후 그 아래에 다음 코드 조각을 추가합니다.

         <div id="logged-in">
             You are logged in as <span id="login-name"></span>.
             <button id="log-out">Log out</button>
         </div>
         <div id="logged-out">
             You are not logged in.
             <button>Log in</button>
         </div>

    그러면 페이지에서 모바일 서비스에 로그인할 수 있게 됩니다.

2.  app.js 파일의 아래쪽에서 refreshTodoItems 함수를 호출하는 코드 줄을 찾아 다음 코드로 바꿉니다.

         function refreshAuthDisplay() {
             var isLoggedIn = client.currentUser !== null;
             $("#logged-in").toggle(isLoggedIn);
             $("#logged-out").toggle(!isLoggedIn);

             if (isLoggedIn) {
                 $("#login-name").text(client.currentUser.userId);
                 refreshTodoItems();
             }
         }

         function logIn() {
             client.login("facebook").then(refreshAuthDisplay, function(error){
                 alert(error);
             });
         }

         function logOut() {
             client.logout();
             refreshAuthDisplay();
             $('#summary').html('<strong>You must login to access data.</strong>');
         }

         // On page init, fetch the data and set up event handlers
         $(function () {
             refreshAuthDisplay();
             $('#summary').html('<strong>You must login to access data.</strong>');           
             $("#logged-out button").click(logIn);
             $("#logged-in button").click(logOut);
         });

    인증 프로세스를 처리하는 함수 집합이 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다.

    **참고**

    Facebook 이외의 ID 공급자를 사용하는 경우 위의 **login** 메서드에 전달된 값을 다음 중 하나로 변경합니다. *microsoftaccount*, *facebook*, *twitter* 또는 *google*.

3.  앱이 실행 중인 브라우저로 돌아가 페이지를 새로 고칩니다.

    로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

    **참고**

    Internet Explorer를 사용할 때 로그인한 후에 오류가 발생할 수 있습니다. `Cannot reach window opener. It may be on a different Internet Explorer zone`. 이는 팝업이 localhost(인트라넷)와 다른 보안 영역(인터넷)에서 실행되기 때문에 발생합니다. localhost를 사용하여 개발하는 동안에만 앱에 영향을 미칩니다. 해결하려면 **인터넷 옵션**의 **보안** 탭을 열고 **로컬 인트라넷**을 클릭한 후 **사이트**를 클릭한 다음 **인트라넷 네트워크를 자동으로 검색**을 사용하지 않도록 설정합니다. 테스트를 완료하면 이 설정을 다시 변경해야 합니다.

다음 단계
---------

다음 자습서인 [스크립트를 통해 사용자 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html)에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. HTML/JavaScript와 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 HTML/JavaScript 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)를 참조하십시오.

