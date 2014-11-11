<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc" />

# Azure Active Directory 그래프 정보 액세스

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET 백 엔드">.NET 백 엔드</a> |
    <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript 백 엔드" class="current">JavaScript 백 엔드</a>
</div>

모바일 서비스에서 제공되는 다른 ID 공급자와 마찬가지로 AAD(Azure Active Directory) 공급자에서도 디렉토리에 대한 프로그래밍 방식의 액세스를 위해 사용할 수 있는 리치 [그래프 API][그래프 API]가 지원됩니다. 이 자습서에서는 [그래프 API][그래프 API]를 사용하여 디렉토리에서 검색하는 추가 사용자 정보를 기반으로 인증된 사용자의 앱 환경을 개인 설정하기 위해 ToDoList 앱을 업데이트합니다.

> [WACOM.NOTE] 이 자습서에서는 Azure Active Directory에서 인증에 대한 다양한 지식 정보를 제공합니다. 이 자습서 이전에 Azure Active Directory 인증 공급자를 사용하여 [인증 시작][인증 시작]을 완료해야 합니다. 이 자습서에서는 [인증 시작][인증 시작] 자습서에서 사용된 TodoItem 응용 프로그램을 계속 업데이트합니다.

이 자습서에서는 다음 단계를 단계별로 안내합니다.

1.  [AAD에서 앱 등록을 위한 액세스 키 생성][AAD에서 앱 등록을 위한 액세스 키 생성]
2.  [GetUserInfo 사용자 지정 API 만들기][GetUserInfo 사용자 지정 API 만들기]
3.  [사용자 지정 API를 사용하도록 앱 업데이트][사용자 지정 API를 사용하도록 앱 업데이트]
4.  [앱 테스트][앱 테스트]

## 필수 조건

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

-   [인증 시작][인증 시작]
    TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

-   [사용자 지정 API 자습서l][사용자 지정 API 자습서l]
    사용자 지정 API를 호출하는 방법을 보여 줍니다.

## <a name="generate-key"></a>AAD에서 앱 등록을 위한 액세스 키 생성

[인증 시작][인증 시작] 자습서에서는 [Azure Active Directory 로그인 사용 등록][Azure Active Directory 로그인 사용 등록] 단계를 완료할 때 통합 응용 프로그램에 대한 등록을 만들었습니다. 이 섹션에서는 해당 통합 응용 프로그램의 클라이언트 ID로 디렉터리 정보를 읽을 때 사용할 키를 생성합니다.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-api"></a>GetUserInfo 사용자 지정 API 만들기

이 섹션에서는 AAD에서 사용자에 대한 추가 정보를 검색하기 위해 [그래프 REST API][그래프 REST API]를 사용할 GetUserInfo 사용자 지정 API를 만듭니다.

모바일 서비스에서 사용자 지정 API를 사용해본 경험이 없으면 이 섹션을 완료하기 전에 [사용자 지정 API 자습서][사용자 지정 API 자습서l]를 검토하는 것이 좋습니다.

1.  [Azure 관리 포털][Azure 관리 포털]에서 모바일 서비스에 대한 새로운 GetUserInfo 사용자 지정 API를 만들고 **Only Authenticated Users**에 대한 get 메서드의 권한을 설정합니다.

    ![][0]

2.  새 GetUserInfo API 및 스크립트 맨 위의 다음 변수에 대한 스크립트 편집기를 엽니다.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  `getAADToken` 함수에 대해 다음 정의를 추가합니다.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    *tenant\_domain*, 통합 응용 프로그램 *client id* 및 응용 프로그램 *key*가 제공된 경우 이 함수는 디렉터리 정보를 읽기 위해 사용되는 그래프 액세스 토큰을 제공합니다.

4.  그래프 API를 사용하여 사용자의 정보를 반환하는 다음 `getUser` 함수를 추가합니다.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    이 함수는 그래프 REST API의 [사용자 가져오기][사용자 가져오기] 끝점에 사용되는 씬 래퍼입니다. 이 함수는 사용자의 개체 ID를 사용하여 사용자 정보를 가져오기 위해 그래프 액세스 토큰을 사용합니다.

5.  내보낸 `get` 메서드를 다음과 같이 업데이트하여 다른 함수를 사용하여 사용자 정보를 반환합니다.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };

## <a name="update-app"></a>GetUserInfo를 사용하도록 앱 업데이트

이 섹션에서는 [인증 시작][인증 시작] 자습서에서 구현한 `AuthenticateAsync` 메서드를 업데이트하여 사용자 지정 API를 호출하고 AAD에서 사용자에 대한 추가 정보를 반환합니다.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]

## <a name="test-app"></a>앱 테스트

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]

## <a name="next-steps"></a>다음 단계

다음 자습서인 [모바일 서비스의 AAD에서 역할 기반 액세스 제어][모바일 서비스의 AAD에서 역할 기반 액세스 제어]에서는 액세스를 허용하기 전에 AAD(Azure Active Directory)에서 역할 기반 액세스 제어를 사용하여 그룹 구성원을 확인합니다.

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [그래프 API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [AAD에서 앱 등록을 위한 액세스 키 생성]: #generate-key
  [GetUserInfo 사용자 지정 API 만들기]: #create-api
  [사용자 지정 API를 사용하도록 앱 업데이트]: #update-app
  [앱 테스트]: #test-app
  [사용자 지정 API 자습서l]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Azure Active Directory 로그인 사용 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [그래프 REST API]: http://msdn.microsoft.com/ko-kr/library/azure/hh974478.aspx
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [사용자 가져오기]: http://msdn.microsoft.com/ko-kr/library/azure/dn151678.aspx
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [모바일 서비스의 AAD에서 역할 기반 액세스 제어]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
