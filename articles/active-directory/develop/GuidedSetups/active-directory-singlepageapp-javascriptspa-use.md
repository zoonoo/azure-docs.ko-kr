
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL(Microsoft 인증 라이브러리)를 사용하여 Microsoft Graph API에 대한 토큰 가져오기

`index.html`에서 `<body>` 태그 내에 다음 코드를 추가합니다.

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>추가 정보
#### <a name="sign-in-the-user"></a>사용자 로그인
사용자가 ‘Call Microsoft Graph API’(Microsoft Graph API 호출) 단추를 처음으로 클릭하면 사용자를 로그인하기 위해 `loginPopup`이 실행됩니다. 이 메서드를 호출하면 사용자에게 로그인하라는 브라우저 창이 표시됩니다. 로그인에 성공하면 사용자 정보가 MSAL에 의해 캐시되고 토큰이 반환됩니다. 이 토큰은 *ID 토큰*이라고 하며 사용자 표시 이름과 같은 사용자에 대한 기본 정보를 포함합니다. 이 토큰에서 제공하는 데이터를 어떤 용도로든 사용할 계획이면 백 엔드 서버에서 이 토큰의 유효성을 검사하여 토큰이 응용 프로그램의 유효한 사용자에게 발급되었음을 보장하는지 확인해야 합니다.

이 가이드에서 생성하는 SPA는 ID 토큰을 직접 사용하지 않고 대신 `acquireTokenSilent` 및/또는 `acquireTokenPopup`을 호출하여 Microsoft Graph API를 쿼리하는 데 사용하는 *액세스 토큰*을 가져옵니다. ID 토큰의 유효성을 검사하는 예제가 필요한 경우 GitHub에서 [이](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 예제") 응용 프로그램 예제를 살펴보세요. 이 예제는 토큰 유효성 검사를 위해 ASP.NET Web API를 사용합니다.

#### <a name="getting-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기
`acquireTokenPopup(scope)` 메서드를 호출하면 사용자에게 로그인하라는 브라우저 창이 표시됩니다. 사용자가 처음으로 보호되는 리소스에 액세스해야 하거나 토큰 획득을 위한 자동 작업에 실패한 경우(예: 사용자의 암호 만료) 일반적으로 사용자는 응용 프로그램에서 대화식으로 로그인해야 합니다.

#### <a name="getting-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기
` acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginPopup`이 처음으로 실행된 후 일반적으로 `acquireTokenSilent` 메서드를 사용하여 후속 호출 시 보호되는 리소스에 액세스하는 데 사용되는 토큰을 가져옵니다. 즉, 토큰을 요청하거나 갱신하기 위한 호출이 자동으로 수행됩니다.
결국 `acquireTokenSilent`가 실패하게 되는데, 사용자가 다른 장치에서 암호를 변경했다거나 하는 것이 이유입니다. 응용 프로그램에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1.  즉시 `acquireTokenPopup`을 호출하여 사용자에게 로그인하라는 메시지를 표시합니다. 이 패턴은 응용 프로그램에 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 응용 프로그램에서 일반적으로 사용됩니다. 이 설정 안내에서 생성하는 예제는 이 패턴을 사용합니다.
2. 또한 응용 프로그램에서는 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 보여줍니다. 따라서 사용자가 로그인할 적절한 시간을 선택하거나 이후에 응용 프로그램이 `acquireTokenSilent`를 다시 시작할 수 있습니다. 이는 사용자가 중단 없이 응용 프로그램의 기능을 사용할 수 있는 경우(예: 응용 프로그램에 사용 가능한 오프라인 콘텐츠가 있는 경우) 일반적으로 사용됩니다. 이 경우 사용자가 보호되는 리소스에 액세스하거나 오래된 정보를 새로 고치기 위해 로그인할 시점을 결정하거나 응용 프로그램이 일시적으로 사용할 수 없게 된 후 네트워크가 복원된 경우 `acquireTokenSilent`를 다시 시도하도록 결정할 수 있습니다.

`acquireTokenSilent`가 실패하는 또 다른 시나리오는 사용자가 호출에서 요청한 범위에 아직 동의하지 않은 경우입니다. 예를 들어 이 가이드에서 생성한 응용 프로그램을 사용자가 처음으로 실행할 경우 *user.read* 범위가 매개 변수로 제공됩니다. 로그인한 사용자가 프로필을 읽는 것에 동의하지 않았으므로 `acquireTokenSilent`는 일반적으로 문제를 해결하는 방법으로 `acquireTokenPopup`을 호출해야 함을 나타내는 `interaction_required`가 포함된 예외를 생성합니다. 이 경우 필요한 범위에 대한 동의 화면을 표시하는 팝업 창이 생성됩니다.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API 호출

다음 코드를 index.html의 `<body>` 태그 내에 추가합니다.

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 가이드에서 생성한 응용 프로그램 예제에서는 `callWebApiWithToken()` 메서드를 사용하여 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행한 다음 콘텐츠를 호출자에게 반환합니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 가이드에서 생성한 응용 프로그램 예제의 경우 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 끝점입니다.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>사용자를 로그아웃하는 메서드 추가

다음 코드를 index.html의 `<body>` 태그 내에 추가합니다.

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
