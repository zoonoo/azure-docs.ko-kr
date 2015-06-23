<properties 
	pageTitle="인증 시작(HTML 5) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 HTML 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="glenga"/>

# 모바일 서비스 앱에 인증 추가 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

이 항목에서는 PhoneGap 또는 Cordova 앱을 비롯한 HTML 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 설명합니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. 앱 디렉터리의 **server** 하위 폴더에서 다음 명령 파일 중 하나를 시작합니다.

	+ **launch-windows**(Windows 컴퓨터) 
	+ **launch-mac.command**(Mac OS X 컴퓨터)
	+ **launch-linux.sh**(Linux 컴퓨터)

	>[AZURE.NOTE]Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 `R`을(를) 입력하세요. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.

	새로운 앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

2. 웹 브라우저에서 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>을 열어 앱을 시작합니다.

	데이터가 로드되지 않습니다. 이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

3. (옵션) 사용하는 웹 브라우저용 스크립트 디버거를 열고 페이지를 다시 로드합니다. 액세스 거부 오류가 발생하는지 확인합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 인증을 허용하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

>[AZURE.NOTE]팝업에서 로그인이 이루어지기 때문에 단추 클릭 이벤트에서 <strong>login</strong> 메서드를 호출해야 합니다. 그렇지 않으면 많은 브라우저에서 로그인 창이 표시되지 않습니다.

1. 프로젝트 파일 index.html을 열고 H1 요소를 찾은 후 그 아래에 다음 코드 조각을 추가합니다.

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	그러면 페이지에서 모바일 서비스에 로그인할 수 있게 됩니다.

2. app.js 파일의 아래쪽에서 refreshTodoItems 함수를 호출하는 코드 줄을 찾아 다음 코드로 바꿉니다.
	
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

    인증 프로세스를 처리하는 함수 집합이 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다. Facebook 이외의 ID 공급자를 사용하는 경우, 위의 <strong>login</strong> 메서드에 전달된 값을 다음 중 하나로 변경합니다. <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> 또는 <em>aad</em>.

	>[AZURE.IMPORTANT]PhoneGap 앱에서 프로젝트에 다음 플러그 인도 추가해야 합니다.
	><ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. 앱이 실행 중인 브라우저로 돌아가 페이지를 새로 고칩니다.

	   로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

	>[AZURE.NOTE]Internet Explorer를 사용할 때 로그인한 후에 오류가 발생할 수 있습니다. <code>Cannot reach window opener. It may be on a different Internet Explorer zone</code>. 이는 팝업이 localhost(인트라넷)와 다른 보안 영역(인터넷)에서 실행되기 때문에 발생합니다. localhost를 사용하여 개발하는 동안에만 앱에 영향을 미칩니다. 해결하려면 <strong>인터넷 옵션</strong>의 <strong>보안</strong> 탭을 열고 <strong>로컬 인트라넷</strong>을 클릭한 후 <strong>사이트</strong>를 클릭한 다음 <strong>인트라넷 네트워크를 자동으로 검색</strong>을 사용하지 않도록 설정합니다. 테스트를 완료하면 이 설정을 다시 변경해야 합니다.

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. HTML/JavaScript와 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 HTML/JavaScript 방법 개념 참조]를 참조하십시오.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[모바일 서비스 시작]: mobile-services-html-get-started.md
[Get started with data]: mobile-services-html-get-started-data.md
[스크립트를 통해 사용자 권한 부여]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[모바일 서비스 HTML/JavaScript 방법 개념 참조]: /documentation/articles/mobile-services-html-how-to-use-client-library

<!--HONumber=54--> 