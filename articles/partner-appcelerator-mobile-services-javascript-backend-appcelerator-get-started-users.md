<properties pageTitle="인증 시작(Appcelerator) | 모바일 개발자 센터" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# 모바일 서비스에서 인증 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

이 항목은 앱에서 Microsoft Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작](영문) 자습서를 완료해야 합니다.

이 자습서를 완료하려면 Appcelerator Titanium Studio 3.2.1 이상, iOS 7.0 이상 및 Android 4.3 이상이 필요합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> 사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	Appcelerator Titanium Studio에서 [모바일 서비스 시작](영문) 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.	Run 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.
    
    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 TodoItem 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

1.	프로젝트 파일 index.js를 열고 테이블 이벤트 Lister 메서드에서 `case 2:`를 찾습니다.

    앱에서 사용자에게 사용할 수 있는 ID 공급자가 포함된 메시지를 표시하거나 ID 공급자 중 하나를 자동으로 제공할 수 있습니다.

2.	모든 사용 가능한 ID 공급자를 제공하려면 다음 코드를 사용합니다.

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var appName = 'appctest';
        azureMobileServices.setAppName(appName);
        var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
        var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
        });
        dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        });
        dialog.show();

3.	특정 ID 공급자를 제공하려면 다음 코드를 사용합니다.

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

>[WACOM.NOTE] Google 이외의 ID 공급자를 사용하는 경우 **authorizeClient**에 전달된 값을 다음 중 하나로 변경합니다. *microsoftaccount*, *facebook*, *twitter* 또는 *windowsazureactivedirectory*

4.	Run 단추를 눌러 프로젝트를 빌드하고 iPhone 시뮬레이터나 Android 에뮬레이터에서 앱을 시작하고 With Login 옵션을 클릭하여 ID 공급자로 로그온합니다.

    로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.


<!-- Anchors. -->

[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started

<!--HONumber=35.1-->
