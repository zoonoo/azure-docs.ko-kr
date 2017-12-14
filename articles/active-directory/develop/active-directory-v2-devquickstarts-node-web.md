---
title: "Azure Active Directory v2.0 Node.js 웹앱 로그인 | Microsoft Docs"
description: "개인 Microsoft 계정과 회사 또는 학교 계정을 사용하여 사용자를 로그인하는 Node.js 웹앱을 작성하는 방법을 알아봅니다."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 230d8ad16dc62564f3c1149443dd59fbb9974db5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Node.js 웹앱에 로그인 추가

> [!NOTE]
> 일부 Azure Active Directory 시나리오 및 기능은 v2.0 끝점에서 작동하지 않습니다. v2.0 끝점을 사용해야 하는지 v1.0 끝점을 사용해야 하는지를 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
> 

이 자습서에서는 Passport를 사용하여 다음 작업을 수행합니다.

* 웹앱에서 Azure AD(Azure Active Directory) 및 v2.0 끝점을 사용하여 사용자를 로그인합니다.
* 사용자에 대한 정보를 표시합니다.
* 앱에서 사용자를 로그아웃합니다.

**Passport** 는 Node.js에 대한 인증 미들웨어입니다. 유연한 모듈식 Passport는 어떤 Express 기반 또는 restify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. Passport에서는 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 또는 기타 옵션을 사용하여 인증을 지원합니다. Microsoft는 Azure AD에 대한 전략을 개발했습니다. 이 문서에서는 모듈을 설치한 다음 Azure AD `passport-azure-ad` 플러그 인을 추가하는 방법을 보여 줍니다.

## <a name="download"></a>다운로드
이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)에서 유지 관리됩니다. 자습서에 따라 [.zip 파일로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

또한 이 자습서의 끝 부분에서는 완성된 응용 프로그램을 다운로드할 수 있습니다.

## <a name="1-register-an-app"></a>1: 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 새 앱을 만들거나 [이러한 세부 단계](active-directory-v2-app-registration.md)에 따라 앱을 등록합니다. 다음을 확인합니다.

* 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 자습서에 필요합니다.
* 앱에 대한 **웹** 플랫폼을 추가합니다.
* 포털에서 **리디렉션 URI** 를 복사합니다. `urn:ietf:wg:oauth:2.0:oob`의 기본 URI 값을 사용해야 합니다.

## <a name="2-add-prerequisities-to-your-directory"></a>2: 디렉터리에 필수 구성 요소 추가
아직 루트 폴더에 있지 않은 경우 명령 프롬프트에서 디렉터리를 변경하여 루트 폴더로 이동합니다. 다음 명령을 실행합니다.

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

또한 빠른 시작의 구조에서 `passport-azure-ad`를 사용합니다.

* `npm install passport-azure-ad`

그러면 `passport-azure-ad`에서 사용하는 라이브러리가 설치됩니다.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3: passport-node-js 전략을 사용하도록 앱 설정
OpenID Connect 인증 프로토콜을 사용하도록 Express 미들웨어를 설정합니다. Passport를 사용하여 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 등의 작업을 수행합니다.

1.  프로젝트의 루트에서 Config.js 파일을 엽니다. `exports.creds` 섹션에서 앱의 구성 값을 입력합니다.
  
  * `clientID`: Azure Portal에서 앱에 할당되는 **응용 프로그램 ID**입니다.
  * `returnURL`: 포털에서 입력한 **리디렉션 URI**입니다.
  * `clientSecret`: 포털에서 생성한 암호입니다.

2.  프로젝트의 루트에서 App.js 파일을 엽니다. `passport-azure-ad`와 함께 제공되는 OIDCStrategy 전략을 호출하려면 다음 호출을 추가합니다.

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  로그인 요청을 처리하려면 방금 참조한 전략을 사용합니다.

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
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

Passport는 모든 전략(Twitter, Facebook 등)에 유사한 패턴을 사용합니다. 모든 전략 작성자는 이 패턴을 준수합니다. 토큰 및 `done`을 매개 변수로 사용하는 `function()`에 전략을 전달합니다. 전략은 모든 작업을 수행한 후에 반환됩니다. 사용자를 저장하고 토큰을 보관하여 다시 요청하지 않아도 되도록 합니다.

  > [!IMPORTANT]
  > 이전 코드는 서버를 인증할 수 있는 모든 사용자를 사용합니다. 이를 자동 등록이라고 합니다. 프로덕션 서버에서는 선택한 등록 프로세스를 먼저 통과해야만 사용자 액세스를 허용하려고 합니다. 일반적으로 이 패턴은 소비자 앱에 표시됩니다. 앱에서는 Facebook에 등록할 수 있도록 허용하지만 추가 정보를 입력하도록 요청합니다. 이 자습서에서 명령줄 프로그램을 사용하지 않는 경우 반환되는 토큰 개체에서 메일을 추출할 수 있습니다. 그런 다음 추가 정보를 입력하도록 사용자에게 요청할 수 있습니다. 테스트 서버이므로 메모리 내 데이터베이스에 직접 사용자를 추가합니다.
  > 
  > 

4.  Passport의 요구에 따라 로그인한 사용자를 추적하는 데 사용할 메서드를 추가합니다. 여기에는 사용자 정보의 직렬화 및 역직렬화가 포함됩니다.

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  Express 엔진을 로드하는 코드를 추가합니다. Express가 제공하는 기본 /views 및 /routes 패턴을 사용합니다.

  ```JavaScript

  // Set up Express (section 2)

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

6.  실제 로그인 요청을 `passport-azure-ad` 엔진에 전달하는 POST 경로를 추가합니다.

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4: Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행
이제 앱이 OpenID Connect 인증 프로토콜을 사용하여 v2.0 끝점과 통신하도록 설정되었습니다. `passport-azure-ad` 전략은 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 모든 세부 정보를 처리합니다. 이제 사용자에게 로그인 및 로그아웃하는 방법을 알려 주고 로그인한 사용자에 대한 추가 정보를 수집하기만 하면 됩니다.

1.  **기본**, **로그인**, **계정** 및 **로그아웃** 메서드를 App.js 파일에 추가합니다.

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  세부 정보는 다음과 같습니다.
    
    * `/` 경로는 index.ejs 보기로 리디렉션되며, 요청에서 사용자(있는 경우)를 전달합니다.
    * `/account` 경로는 먼저 *인증되었는지 확인*합니다(다음 코드에서 구현). 그런 다음 요청에서 사용자를 전달합니다. 따라서 사용자에 대한 자세한 정보를 얻을 수 있습니다.
    * `/login` 경로는 `passport-azuread`에서 `azuread-openidconnect` 인증자를 호출합니다. 성공하지 못하면 경로는 사용자를 다시 `/login`으로 리디렉션합니다.
    * `/logout` 경로는 logout.ejs 보기(및 경로)를 호출합니다. 이 경로는 쿠키를 지운 다음 사용자를 다시 index.ejs로 반환합니다.

2.  `/account`의 앞부분에서 사용한 **EnsureAuthenticated** 메서드를 추가합니다.

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  App.js에서 서버를 만듭니다.

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5: 웹 사이트에서 사용자를 표시하는 보기 및 경로를 Express에서 만들기
사용자에 대한 정보를 표시하는 경로 및 보기를 추가합니다. 또한 경로 및 보기는 만든 `/logout` 및 `/login` 경로를 처리합니다.

1. 루트 디렉터리에서 `/routes/index.js` 경로를 만듭니다.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  루트 디렉터리에서 `/routes/user.js` 경로를 만듭니다.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` 및 `/routes/user.js`는 사용자(있는 경우)를 포함하여 요청을 보기에 전달하는 간단한 경로입니다.

3.  루트 디렉터리에서 `/views/index.ejs` 보기를 만듭니다. 이 페이지는 **로그인** 및 **로그아웃** 메서드를 호출합니다. 또한 `/views/index.ejs` 보기를 사용하여 계정 정보를 캡처합니다. 요청에서 사용자를 전달할 때 조건부 `if (!user)`를 사용할 수 있습니다. 이는 사용자가 로그인했다는 증거입니다.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  루트 디렉터리에서 `/views/account.ejs` 보기를 만듭니다. `/views/account.ejs` 보기에서는 `passport-azuread`가 사용자 요청에 넣는 추가 정보를 볼 수 있습니다.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  레이아웃을 추가합니다. 루트 디렉터리에서 `/views/layout.ejs` 보기를 만듭니다.

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  앱을 빌드하고 실행하려면 `node app.js`를 실행합니다. 그런 다음 `http://localhost:3000`으로 이동합니다.

7.  개인 Microsoft 계정이나 회사 또는 학교 계정으로 로그인합니다. 사용자의 ID가 /account 목록에 반영됩니다. 

이제 산업 표준 프로토콜을 사용하여 보호되는 웹앱을 만들었습니다. 앱에서 개인 및 회사 또는 학교 계정을 사용하여 사용자를 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계
참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [.zip 파일](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)로 제공됩니다. GitHub에서 복제할 수도 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

다음으로 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[v2.0 끝점을 사용하여 Node.js 웹 API의 보안 유지](active-directory-v2-devquickstarts-node-api.md)

다음은 몇 가지 추가 리소스입니다.

* [Azure AD v2.0 개발자 가이드](active-directory-appmodel-v2-overview.md)
* [Stack Overflow "azure-active-directory" 태그](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>당사 제품에 대한 보안 업데이트 가져오기
보안 사고가 발생할 때 알림을 받으려면 등록하는 것이 좋습니다. [Microsoft 기술 보안 알림](https://technet.microsoft.com/security/dd252948) 페이지에서 보안 권고 알림을 구독합니다.

