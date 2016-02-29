<properties
	pageTitle="모바일 앱을 사용하여 Apache Cordova에 인증 추가| Azure 앱 서비스"
	description="Azure 앱 서비스에서 모바일 앱을 사용하여 Google, Facebook, Twitter, Microsoft를 비롯한 다양한 ID 공급자를 통해 Apache Cordova 앱의 사용자를 인증하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Apache Cordova 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## 요약

이 자습서에서는 지원되는 ID 공급자를 사용하여 Apache Cordova의 할 일 모음 빠른 시작 프로젝트에 인증을 추가합니다. 이 자습서는 [모바일 앱 시작] 자습서를 기반으로 하며 이를 먼저 완료해야 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Visual Studio에서 [모바일 앱 시작] 자습서를 완료했을 때 만든 프로젝트를 연 다음 **Google Android 에뮬레이터**에서 응용 프로그램을 실행하고 앱이 시작된 후 예기치 않은 연결 오류가 표시되는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하기 때문에 발생합니다. 백 엔드는 OAuth를 사용하여 인증 페이지로 사용자를 리디렉션합니다. 그러나 응용 프로그램은 OAuth 끝점을 신뢰하지 않습니다.

다음으로 앱을 업데이트하여 모바일 앱 백 엔드에서 리소스를 요청하기 전에 사용자를 인증하도록 합니다.

##<a name="add-authentication"></a>앱에 인증 추가

1. **Visual Studio**에서 프로젝트를 연 다음 편집을 위해 <tt>www/index.html</tt> 파일을 엽니다.

2. 헤드 섹션에서 `Content-Security-Policy` Meta 태그를 찾습니다. 허용된 원본 목록에 OAuth 호스트를 추가해야 합니다.

    | 공급자 | SDK 공급자 이름 | OAuth 호스트 |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    다음은 Content-Security-Policy(Azure Active Directory용으로 구현됨) 예제입니다.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    <tt>https://login.windows.net</tt>을 위 표의 OAuth 호스트로 바꿔야 합니다. 이 Meta 태그에 대한 자세한 내용은 [Content-Security-Policy 설명서]를 참조하세요.

    일부 인증 공급자에서는 적절한 모바일 장치에서 사용하는 경우 Content-Security-Policy 변경이 필요하지 않습니다. 예를 들어 Android 장치에서 Google 인증을 사용하는 경우 Content-Security-Policy를 변경하지 않아도 됩니다.

3. 편집을 위해 <tt>www/js/index.js</tt> 파일을 엽니다. 프로젝트가 빌드되고 이미 변경된 내용으로 실행되지만 로그인 작업을 시작하는 데 login() 메서드를 명시적으로 호출하는 것이 모범 사례로 간주됩니다. `onDeviceReady()` 메서드를 찾습니다. 클라이언트 생성 코드에서 다음을 추가합니다.

        // Login to the service
        client.login('SDK Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    예를 들어 Azure Active Directory의 경우 다음을 사용합니다.

        client.login('aad')

    login() 메서드는 JavaScript 프라미스를 반환하는 비동기 함수입니다. 초기화의 나머지는 login() 메서드가 완료될 때까지 실행되지 않도록 프라미스 응답 안에 배치됩니다.

4. 프로젝트를 실행합니다. 프로젝트 초기화가 완료되면 응용 프로그램에서 선택한 인증 공급자에 대한 OAuth 로그인 페이지를 표시합니다.

##<a name="next-steps"></a>다음 단계

* Azure 앱 서비스의 [인증 정보]에 대해 자세히 알아봅니다.
* [푸시 알림]을 Apache Cordova 앱에 추가하여 자습서를 계속합니다.

<!-- URLs. -->
[모바일 앱 시작]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy 설명서]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[푸시 알림]: app-service-mobile-cordova-get-started-push.md
[인증 정보]: app-service-mobile-auth.md

<!---HONumber=AcomDC_0218_2016-->