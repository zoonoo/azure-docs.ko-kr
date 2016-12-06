### <a name="a-nameserver-authahow-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>방법: 공급자를 사용하여 인증(서버 흐름)
Mobile Apps가 앱에서 인증 프로세스를 관리하게 하려면 앱을 ID 공급자에 등록해야 합니다. 그런 다음, Azure 앱 서비스에서 공급자로부터 제공된 응용 프로그램 ID 및 암호를 구성해야 합니다.
자세한 내용은 [앱에 인증 추가](../articles/app-service-mobile/app-service-mobile-ios-get-started-users.md)자습서를 참조하세요.

ID 공급자를 등록하고 나면 공급자의 이름을 사용하여 .login() 메서드를 호출합니다. 예를 들어 Facebook으로 로그인하려면 다음 코드를 사용합니다.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});app-service-mobile
app-service-mobile-ios-get-started-users value passed to the login method above to one of
the following: `microsoftaccount`, `facebook`, `twitter`, `google`, or `aad`.

In this case, Azure App Service manages the OAuth 2.0 authentication flow by displaying the login page of the selected
provider and generating a App Service authentication token after successful login with the identity provider. The login
function, when complete, returns a JSON object (user) that exposes both the user ID and App Service authentication token
in the userId and authenticationToken fields, respectively. This token can be cached and re-used until it expires.

###<a name="client-auth"></a>How to: Authenticate with a Provider (Client Flow)

Your app can also independently contact the identity provider and then provide the returned token to your App Service for
authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional
user data from the identity provider.

#### Social Authentication basic example

This example uses Facebook client SDK for authentication:

```
client.login( "facebook", {"access_token": token}) .done(function (results) { alert("로그인한 계정: " + results.userId); }, function (err) { alert("오류: " + err); });

```
This example assumes that the token provided by the respective provider SDK is stored in the token variable.

#### Microsoft Account example

The following example uses the Live SDK, which supports single-sign-on for Windows Store apps by using Microsoft Account:

```
WL.login({ scope: "wl.basic"}).then(function (result) { client.login( "microsoftaccount", {"authenticationToken": result.session.authentication_token}) .done(function(results){ alert("로그인한 계정: " + results.userId); }, function(error){ alert("오류: " + err); }); });

```

This example gets a token from Live Connect, which is supplied to your App Service by calling the login function.

###<a name="auth-getinfo"></a>How to: Obtain information about the authenticated user

The authentication information for the current user can be retrieved from the `/.auth/me` endpoint using any
AJAX method.  Ensure you set the `X-ZUMO-AUTH` header to your authentication token.  The authentication token
is stored in `client.currentUser.mobileServiceAuthenticationToken`.  For example, to use the fetch API:

```
var url = client.applicationUrl + '/.auth/me'; var headers = new Headers(); headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken); fetch(url, { headers: headers }) .then(function (data) { return data.json() }).then(function (user) { // 사용자 개체에 인증된 사용자에 대한 클레임이 포함되어 있습니다. });
```

Fetch is available as an npm package or for browser download from CDNJS. You could also use
jQuery or another AJAX API to fetch the information.  Data will be received as a JSON object.



<!--HONumber=Nov16_HO3-->


