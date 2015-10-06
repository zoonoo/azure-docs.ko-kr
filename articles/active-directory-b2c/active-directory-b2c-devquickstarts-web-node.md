<properties
	pageTitle="Azure B2C 미리 보기용 nodeJS 웹앱에 로그인 추가 | Microsoft Azure"
	description="B2C 테넌트로 사용자가 로그인하는 Node.js 웹앱을 빌드하는 방법입니다."
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# B2C 미리 보기: nodeJS 웹앱에 로그인 추가


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작하여 Azure AD B2C의 기본 사항에 대해 알아봅니다.

**Passport**는 Node.js에 대한 인증 미들웨어입니다. 매우 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Microsoft Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Microsoft Azure Active Directory `passport-azure-ad` 플러그 인을 추가하겠습니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. Passport의 azure-ad-passport 플러그 인을 사용하도록 앱을 설정합니다.
3. Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청을 실행합니다.
4. 사용자에 대한 데이터를 출력합니다.

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

전체 응용 프로그램은 이 자습서 마지막 부분에서도 제공됩니다.

> [AZURE.WARNING] 	B2C 미리 보기에서 Web-API 작업 서버와 이 서버에 연결되는 클라이언트에 대해 동일한 클라이언트 ID/응용 프로그램 ID 및 정책을 사용해야 합니다. 이것은 iOS 및 Android 자습서에 대해 적용됩니다. 이전에 이러한 빠른 시작 중 하나에서 응용 프로그램을 만든 경우, 아래와 같이 새로 만드는 대신 이 값을 사용합니다.

## 1. Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 
넘어가기 전에 [B2C 디렉터리 만들기](active-directory-b2c-get-started.md)로 이동합니다.

## 2. 응용 프로그램 만들기

이제 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 클라이언트 앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 
만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 반드시

- 응용 프로그램에서 **웹앱/Web API** 포함
- `http://localhost/TodoListService`을 **회신 URL**로 입력 - 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호**를 만들고 복사합니다. 곧 필요합니다.
- 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 또한 곧 필요합니다.

    > [AZURE.IMPORTANT]
    [Azure 포털](https://manage.windowsazure.com/)의 **응용 프로그램** 탭에 등록된 응용 프로그램은 사용할 수 없습니다.

## 3. 정책 만들기

In Azure AD B2C, every user experience is defined by a [**policy**](active-directory-b2c-reference-policies.md).  This app contains three 
identity experiences - sign-up, sign-in, and sign-in with Facebook.  You will need to create one policy of each type, as described in the 
[policy reference article](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  When creating your three policies, be sure to:

- 등록 정책에서 **표시 이름** 및 다른 몇가지 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 곧 필요합니다. 

세 가지 정책을 성공적으로 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보지
못했다면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작해야 합니다.



## 4. pre requisities를 디렉터리에 추가

명령줄에서 루트 폴더가 없는 경우 디렉터리를 루트 폴더로 변경하고 다음 명령을 실행합니다.

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- 또한 빠른 시작의 뼈대에 Preview를 위해 `passport-azure-ad`을(를) 사용했습니다.

- `npm install passport-azure-ad`


이는 passport-azure-ad가 의존하는 라이브러리를 설치합니다.

## 5. passport-node-js 전략을 사용하도록 앱을 설정합니다.
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 Express 미들웨어를 구성합니다. passport는 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

-	먼저 프로젝트의 루트에서 `config.js` 파일을 열고 `exports.creds` 섹션에 앱의 구성 값을 입력합니다.
    -	`clientID:`는 등록 포털에서 앱에 할당된 **응용 프로그램 ID**입니다.
    -	`returnURL`는 포털에서 입력한 **리디렉션 URI**입니다.
    - `tenantName:`는 contoso.onmicrosoft.com와 같이 앱의 **테넌트 이름**입니다.


- 그 다음 프로젝트의 루트에 있는 `app.js` 파일을 열고 `passport-azure-ad`과 함께 제공되는 `OIDCStrategy` 전략을 불러오기 위해 다음 호출을 추가합니다.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- 그런 후에 방금 참조한 전략을 사용하여 로그인 요청을 처리합니다.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport는 모든 전략 작성자가 준수하는 유사한 패턴을 모든 전략(Twitter, Facebook 등)에 대해 사용합니다. 전략을 보면 토큰 및 완료가 매개 변수로 포함된 function()이 전달되는 것을 확인할 수 있습니다. 전략은 모든 작업을 수행한 후 돌아옵니다. 전략이 돌아오면 사용자를 저장하고 토큰을 다시 요청할 필요가 없도록 보관하려 합니다.

> [AZURE.IMPORTANT]위 코드는 서버에 인증하는 모든 사용자를 사용합니다. 이를 자동 등록이라고 합니다. 프로덕션 서버에서는 결정한 등록 프로세스를 먼저 통과해야만 사용자 액세스를 허용하려 합니다. 일반적으로 이는 Facebook으로 등록할 수 있도록 하지만 추가 정보를 입력하도록 요구하는 소비자 앱에서 나타나는 패턴입니다. 샘플 응용 프로그램이 아니라면 반환된 토큰 개체에서 메일을 추출하고 추가 정보를 입력하도록 요구할 수 있습니다. 테스트 서버이므로 메모리 내 데이터베이스에 추가합니다.

- 다음에는 Passport에서 요구하는 대로 로그인한 사용자를 추적할 수 있는 메서드를 추가하겠습니다. 여기에는 사용자 정보의 직렬화 및 역직렬화가 포함됩니다.

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- 다음에는 Express 엔진을 로드하는 코드를 추가하겠습니다. 여기서 Express가 제공하는 기본 /views 및 /routes 패턴이 사용되는 것을 확인할 수 있습니다.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 마지막으로, 실제 로그인 요청을 `passport-azure-ad` 엔진에 전달하는 POST 경로를 추가하겠습니다.

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });
```

## 4\. Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행

이제 앱이 OpenID Connect 인증 프로토콜을 사용하여 v2.0 끝점 끝점과 통신하도록 올바르게 구성되었습니다. `passport-azure-ad`가 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 까다로운 모든 세부 과정을 처리했습니다. 이제 사용자에게 로그인 및 로그아웃하는 방법을 알려주고 로그인한 사용자에 대한 추가 정보를 수집하기만 하면 됩니다.

- 먼저 `app.js` 파일에 기본, 로그인, 계정 및 로그아웃 메서드를 추가합니다.

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	자세히 살펴보겠습니다.
    -	`/` 경로는 요청에 사용자를 전달하여(있는 경우) index.ejs 뷰로 리디렉션됩니다.
    - `/account` 경로는 먼저 ***인증되었는지 확인***하고(아래에서 구현) 사용자에 대한 추가 정보를 얻을 수 있도록 요청에 사용자를 전달합니다.
    - `/login` 경로는 `passport-azuread`에서 azuread-openidconnect 인증자를 호출하고, 실패할 경우 사용자를 다시 /login으로 리디렉션합니다.
    - `/logout`은 쿠키를 지우는 logout.ejs(및 경로)를 호출하고 사용자를 다시 index.ejs로 돌려보냅니다.


- `app.js`의 마지막 부분을 위해 위의 `/account`에서 사용된 EnsureAuthenticated 메서드를 추가하겠습니다.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- 마지막으로, `app.js`에서 실제로 서버 자체를 만들겠습니다.

```JavaScript

app.listen(3000);

```


## 5\. express에서 뷰와 경로를 만들어 정책 호출

`app.js`가 완료되었습니다. 로그인 및 등록 정책을 호출할 수 있는 경로와 뷰를 추가하고 만든 `/logout` 및 `/login` 경로를 처리하기만 하면 됩니다.

- 루트 디렉터리 아래에 `/routes/index.js` 경로를 만듭니다.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 루트 디렉터리 아래에 `/routes/user.js` 경로 만들기

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

이러한 간단한 경로는 단순히 사용자(있는 경우)를 포함하여 요청을 뷰에 전달합니다.

- 루트 디렉터리 아래에 `/views/index.ejs` 뷰를 만듭니다. 이는 로그인 및 로그아웃에 대한 정책을 호출하고 계정 정보를 얻을 수 있게 해주는 간단한 페이지입니다. 요청에 전달되는 사용자가 로그인한 사용자가 있음을 입증하므로 조건부 `if (!user)`를 사용할 수 있습니다.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- `passport-azuread`가 사용자 요청에 포함한 추가 정보를 볼 수 있도록 루트 디렉터리 아래에 `/views/account.ejs` 뷰를 만듭니다.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

마지막으로 앱을 빌드하고 실행합니다.

`node app.js`를 실행하고 `http://localhost:3000`으로 이동합니다.


전자 메일 또는 Facebook으로 앱에 등록 또는 로그인합니다. 로그아웃했다가 다른 사용자로 다시 로그인합니다.



##다음 단계

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[node.js에서 B2C 앱 모델을 사용하여 Web API 보안 유지 >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->