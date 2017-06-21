---
title: "Azure AD Node.js 시작 | Microsoft Docs"
description: "인증을 위해 Azure AD와 통합되는 Node.js REST Web API를 빌드하는 방법."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 707bb2b7cd9796e0e05234aa08002bbc1820f871
ms.contentlocale: ko-kr
ms.lasthandoff: 03/18/2017


---
# <a name="get-started-with-web-apis-for-nodejs"></a>Node.js용 web API 시작
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* 는 Node.js에 대한 인증 미들웨어입니다. 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Microsoft Azure AD(Azure Active Directory)에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Microsoft Azure Active Directory `passport-azure-ad` 플러그 인을 추가합니다.

이렇게 하려면 다음을 수행해야 합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. Passport의 `passport-azure-ad` 플러그 인을 사용하도록 앱을 설정합니다.
3. To Do List web API를 호출하도록 클라이언트 응용 프로그램을 구성합니다.

이 자습서에 대한 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi)에서 유지 관리됩니다.

> [!NOTE]
> 이 문서에서는 Azure AD B2C를 사용하여 로그인, 등록 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다.  [Azure Active Directory와 통합하는 방법 문서](active-directory-how-to-integrate.md)를 시작하여 Azure Active Directory의 기본 사항에 대해 알아보시기 바랍니다.
>
>

Microsoft는 이 실행 예제의 모든 소스 코드를 GitHub의 MIT 라이선스에 출시했으므로 자유롭게 복제(및 분기)하고 피드백을 제공하고 요청을 끌어올 수 있습니다.

## <a name="about-nodejs-modules"></a>Node.js 모듈 정보
이 연습에서는 Node.js 모듈을 사용합니다. 모듈은 응용 프로그램의 특정 기능을 제공하는 로드 가능한 JavaScript 패키지입니다. 일반적으로 NPM 설치 디렉터리에서 Node.js NPM 명령줄 도구를 사용하여 모듈을 설치합니다. 그러나 HTTP 모듈과 같은 일부 모듈은 코어 Node.js 패키지에 포함되어 있습니다.

설치된 모듈은 Node.js 설치 디렉터리의 루트에 있는 **node_modules** 디렉터리에 저장됩니다. **node_modules** 디렉터리의 각 모듈은 종속되는 모듈이 포함된 고유한 **node_modules** 디렉터리를 유지합니다. 또한 각 필수 모듈에는 **node_modules** 디렉터리가 있습니다. 이러한 반복되는 디렉터리 구조는 종속성 체인을 나타냅니다.

이와 같은 종속성 체인 구조로 인해 응용 프로그램 공간이 커집니다. 하지만 모든 종속성이 충족되고 개발에 사용되는 모듈 버전이 프로덕션에도 사용되도록 보장합니다. 따라서 프로덕션 앱 동작을 예측하기 쉬워지고 사용자에게 영향을 줄 수 있는 버전 문제도 방지됩니다.

## <a name="step-1-register-an-azure-ad-tenant"></a>1단계: Azure AD 등록
이 샘플을 사용하려면 Azure Active Directory 테넌트가 필요합니다. 테넌트가 무엇인지 또는 어떻게 가져오는지 잘 모를 경우 [Azure AD 테넌트를 가져오는 방법](active-directory-howto-tenant.md)을 참조하세요.

## <a name="step-2-create-an-application"></a>2단계: 응용 프로그램 만들기
다음으로 Azure AD가 앱과 안전하게 통신해야 한다는 정보를 제공하는 앱을 디렉터리에 만들어야 합니다.  이 경우 클라이언트 앱과 web API가 하나의 논리 앱을 구성하기 때문에 둘 다 단일 **응용 프로그램 ID**로 표현됩니다.  앱을 만들려면 [다음 지침](active-directory-how-applications-are-added.md)에 따릅니다. 기간 업무 앱을 빌드하는 경우 [이 추가 지침이 유용할 수 있습니다](../active-directory-applications-guiding-developers-for-lob-applications.md).

응용 프로그램을 만들려면:

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 상단 메뉴에서 계정을 선택합니다. 그런 다음 **디렉터리** 목록에서 응용 프로그램을 등록할 Active Directory 테넌트를 선택합니다.

3. 왼쪽 메뉴에서 **추가 서비스**를 선택한 후 **Azure Active Directory**를 선택합니다.

4. **앱 등록**을 선택하고 **추가**를 선택합니다.

5. 프롬프트에 따라 새 **웹 응용 프로그램 및/또는 WebAPI**를 만듭니다.

      * 응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명합니다.

      * **로그온 URL** 은 앱의 기본 URL입니다.  샘플 코드의 기본 URL은 `https://localhost:8080`입니다.

6. 등록 후에는 Azure AD가 사용자 앱에 고유한 응용 프로그램 ID를 할당합니다. 이 값은 다음 섹션에서 필요하므로 응용 프로그램 페이지에서 이 값을 복사해 둡니다.

7. 응용 프로그램에 대한 **설정** -> **속성** 페이지에서 앱 ID URI를 업데이트합니다. **앱 ID URI** 는 응용 프로그램의 고유 식별자입니다. 규칙은 `https://<tenant-domain>/<app-name>`(예: `https://contoso.onmicrosoft.com/my-first-aad-app`)을 사용하는 것입니다.

8. **설정** 페이지에서 응용 프로그램의 **키**를 만들고 편한 위치에 복사해 둡니다. 곧 이 키가 필요합니다.

## <a name="step-3-download-nodejs-for-your-platform"></a>3단계: 사용자 플랫폼을 위한 Node.js 다운로드
이 샘플을 사용하려면 작동하는 Node.js 설치가 있어야 합니다.

[http://nodejs.org](http://nodejs.org)에서 Node.js를 설치합니다.

## <a name="step-4-install-mongodb-on-your-platform"></a>4단계: 플랫폼에 MongoDB 설치
이 샘플을 사용하려면 작동하는 MongoDB 설치가 있어야 합니다. MongoDB를 사용하여 REST API가 서버 인스턴스에서 지속되도록 할 것입니다.

[http://mongodb.org](http://www.mongodb.org)에서 MongoDB를 설치합니다.

> [!NOTE]
> 이 연습에서는 MongoDB의 기본 설치 및 mongodb://localhost 서버 끝점(현재 문성 작성 시점 기준)을 사용한다고 가정합니다.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>5단계: web API에 Restify 모듈 설치
Resitfy를 사용하여 REST API를 작성할 것입니다. Restify는 Express에서 파생된 작고 유연한 Node.js 응용 프로그램 프레임워크입니다. Connect 위에 REST API를 구축하기 위한 강력한 기능 집합을 포함합니다.

### <a name="install-restify"></a>Restify 설치
1. 명령줄에서 디렉터리를 **azuread** 디렉터리로 변경합니다. **azuread** 디렉터리가 없으면 만듭니다.

        `cd azuread - or- mkdir azuread; cd azuread`

2. 다음 명령을 입력합니다.

    `npm install restify`

    이 명령은 Restify를 설치합니다.

#### <a name="did-you-get-an-error"></a>오류가 발생했나요?
일부 운영 체제에서 NPM 매개 변수를 사용하면 **오류: EPERM, chmod '/usr/local/bin/..'** 오류 메시지와 관리자 계정으로 실행하라는 제안이 표시될 수 있습니다. 이런 경우 sudo 명령을 사용하여 더 높은 권한 수준으로 NPM을 실행하세요.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>DTRACE 관련 오류가 발생했나요?
Restify를 설치할 때 다음과 같은 오류가 발생할 수 있습니다.

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```
Restify는 DTrace를 사용하여 REST 호출을 추적하는 강력한 메커니즘을 제공합니다. 그러나 DTrace가 없는 운영 체제가 많습니다. 이러한 오류는 무시해도 됩니다.

이 명령의 출력은 다음 출력과 유사합니다.

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="step-6-install-passportjs-in-your-web-api"></a>6단계: web API에 Passport.js 설치
[Passport](http://passportjs.org/) 는 Node.js에 대한 인증 미들웨어입니다. 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다.

Microsoft는 Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Azure Active Directory 전략 플러그 인을 추가하겠습니다.

1. 명령줄에서 디렉터리를 **azuread** 디렉터리로 변경합니다.

2. passport.js를 설치하려면 다음 명령을 입력합니다.

    `npm install passport`

    이 명령의 출력은 다음과 유사하게 표시됩니다.

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>7단계: web API에 Passport-Azure-AD 추가
다음으로 Azure Active Directory를 Passport에 연결하는 전략 제품군인 `passport-azure-ad`를 사용하여 OAuth 전략을 추가합니다. 이 REST API 샘플에서는 이 전략을 전달자 토큰으로 사용합니다.

> [!NOTE]
> OAuth2는 알려진 모든 토큰 유형을 발급할 수 있는 프레임워크를 제공하지만 일반적으로 특정 토큰 유형만 사용됩니다. 전달자 토큰은 끝점 보호에 가장 일반적으로 사용되는 토큰입니다. 전달자 토큰은 OAuth2에서 가장 널리 발급되는 토큰 유형입니다. 많은 구현에서 발급되는 유일한 토큰 유형이 전달자 토큰이라고 가정합니다.
>
>

명령줄에서 디렉터리를 **azuread** 디렉터리로 변경합니다.

다음 명령을 입력하여 Passport.js `passport-azure-ad module`을 설치합니다.

`npm install passport-azure-ad`

이 명령의 출력은 다음 출력과 유사하게 표시됩니다.


    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>8단계: web API에 MongoDB 모듈 추가
데이터 저장소로 MongoDB를 사용하겠습니다. 따라서 모델 및 스키마를 관리에 널리 사용되는 Mongoose라고 하는 플러그 인을 설치 해야 합니다. MongoDB용 데이터베이스 드라이버(MongoDB)도 설치해야 합니다.

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>9단계: 추가 모듈 설치
다음으로 나머지 필수 모듈을 설치합니다.

1. 아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

    `cd azuread`

2. 다음 명령을 입력하여 **node_modules** 디렉터리에 다음 모듈을 설치합니다.

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>10단계: 종속성을 적용하여 server.js 만들기
server.js 파일은 web API 서버에 대한 대부분의 기능을 제공합니다. 이 파일에 대부분의 코드를 추가합니다. 프로덕션을 위해, 기능을 별도의 경로 및 컨트롤러와 같은 좀 더 작은 파일로 리팩터링하는 것이 좋습니다. 이 데모에서는 이 기능에 server.js를 사용합니다.

1. 아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

    `cd azuread`

2. 즐겨 사용하는 편집기에서 `server.js` 파일을 만들고 다음 정보를 추가합니다.

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. 파일을 저장합니다. 잠시 후에 이 파일로 다시 돌아갈 것입니다.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>11단계: Azure AD 설정을 저장하기 위한 구성 파일 만들기
이 코드 파일은 Azure Active Directory 포털의 구성 매개 변수를 Passport.js에 전달합니다. 이 연습의 첫 번째 부분에서 포털에 web API를 추가할 때 이러한 구성 값을 만들었습니다. 코드를 복사한 후에 이러한 매개 변수 값에 추가할 항목에 대해 설명할 것입니다.

1. 아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

    `cd azuread`

2. 즐겨 사용하는 편집기에서 `config.js` 파일을 만들고 다음 정보를 추가합니다.

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. 파일을 저장합니다.

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>12단계: server.js 파일에 구성 값 추가
응용 프로그램에서 방금 만든 .config 파일로부터 이러한 값을 읽어야 합니다. 이 작업을 수행하기 위해 응용 프로그램에서 .config 파일을 필수 리소스로 추가합니다. 그런 다음 config.js 문서의 변수와 일치하도록 전역 변수를 설정합니다.

1. 아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

    `cd azuread`

2. 즐겨 사용하는 편집기에서 `server.js` 파일을 열고 다음 정보를 추가합니다.

    ```Javascript
    var config = require('./config');
    ```
3. 그 후 다음 코드를 사용하여 `server.js`에 새 섹션을 추가합니다.

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. 파일을 저장합니다.

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>13단계: Moongoose를 사용하여 MongoDB 모델 및 스키마 정보 추가
이제 이러한 세 파일을 하나의 REST API 서비스에 통합하면 모든 준비 과정이 마무리됩니다.

이 연습에서는 4단계에서 설명한 대로 MongoDB를 사용하여 작업을 저장합니다.

11단계에서 만든 `config.js` 파일에서는 `tasklist` 데이터베이스를 호출했습니다. **mogoose_auth_local** 연결 URL의 마지막에 추가한 데이터베이스이기 때문입니다. MongoDB에서 이 데이터베이스를 미리 만들 필요가 없습니다. 그 대신 서버 응용 프로그램이 처음으로 실행될 때 MongoDB가 이 데이터베이스를 자동으로 만듭니다(아직 데이터베이스가 없다고 가정).

사용하려는 MongoDB 데이터베이스를 서버에 알렸으므로 일부 추가 코드를 작성하여 서버 작업에 대한 모델 및 스키마를 만들어야 합니다.

### <a name="discussion-of-the-model"></a>모델에 대한 논의
Microsoft의 스키마 모델은 간단합니다. 필요에 따라 확장할 수 있습니다.

NAME: 작업에 할당된 사람의 이름입니다. **문자열**입니다.

TASK: 작업 자체입니다. **문자열**입니다.

DATE: 작업이 만료되는 날짜입니다. **DATETIME**입니다.

COMPLETED: 작업의 완료 여부입니다. **부울**입니다.

### <a name="creating-the-schema-in-the-code"></a>코드에 스키마 만들기
1. 아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

    `cd azuread`

2. 즐겨 사용하는 편집기에서 `server.js` 파일을 열고 구성 항목 아래에 다음 정보를 추가합니다.

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
    var TaskSchema = new Schema({
        owner: String,
        task: String,
        completed: Boolean,
        date: Date
    });

    // Use the schema to register a model.
    mongoose.model('Task', TaskSchema);
    var Task = mongoose.model('Task');
    ```
코드를 보면 알 수 있듯이, 먼저 스키마를 만듭니다. 그런 다음 **경로**를 정의할 때 코드 전체에서 데이터를 저장하는 데 사용할 모델 개체를 만듭니다.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>14단계: Task REST API 서버에 대한 경로 추가
작업할 데이터베이스 모델이 준비되었으므로 REST API 서버에 사용할 경로를 추가하겠습니다.

### <a name="about-routes-in-restify"></a>Restify의 경로 정보
경로는 Express 스택을 사용할 때와 동일한 방식으로 Restify에서 작동합니다. 클라이언트 응용 프로그램이 호출할 것으로 예상하는 URI를 사용하여 경로를 정의합니다. 일반적으로 경로는 별도 파일에 정의합니다. 여기서는 경로를 server.js 파일에 저장하겠습니다. 프로덕션 사용을 위해서는 자체 파일에서 이러한 경로를 팩터링하는 것이 좋습니다.

Restify 경로의 일반적인 패턴은 다음과 같습니다.

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


가장 기본적인 수준의 패턴입니다. Resitfy(및 Express)는 응용 프로그램 유형을 정의하고, 여러 끝점에 복잡한 라우팅을 제공하는 등 훨씬 수준 높은 기능을 제공합니다. 여기서는 이러한 경로를 간단하게 유지하겠습니다.

### <a name="add-default-routes-to-our-server"></a>서버에 기본 경로 추가
이제 만들기, 검색, 업데이트 및 삭제의 기본 CRUD 경로를 추가합니다.

1. 아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

    `cd azuread`

2. 즐겨 사용하는 편집기에서 `server.js` 파일을 열고 앞에서 만든 데이터베이스 항목 아래에 다음 정보를 추가합니다.

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name.

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks.

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name.

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="add-error-handling-in-our-apis"></a>API에서 오류 처리 추가
```

///--- Errors for communicating something interesting back to the client.

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="step-15-create-your-server"></a>15단계: 서버 만들기
데이터베이스를 정의했으며 경로가 정위치에 있습니다. 마지막으로 할 일은 호출을 관리할 서버 인스턴스를 추가하는 것입니다.

Restify(및 Express)에서 REST API 서버에 대해 여러 사용자 지정 작업을 수행할 수 있지만 여기서는 가장 기본적인 설정만 사용할 것입니다.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>16단계: 서버에 경로 추가(지금은 인증 없이)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler.
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>17단계: 서버 실행(OAuth 지원을 추가하기 전에)
인증을 추가하기 전에 서버를 테스트합니다.

서버를 테스트하는 가장 쉬운 방법은 명령줄에서 curl을 사용하는 것입니다. 이 작업을 수행하기 전에 JSON처럼 출력을 구문 분석할 수 있는 유틸리티가 필요합니다.

1. 다음 JSON 도구를 설치합니다(이제부터 나오는 모든 예제에서 이 도구가 사용됨).

    `$npm install -g jsontool`

    이렇게 하면 JSON 도구가 전역적으로 설치됩니다. 작업이 완료되었으니 서버 작업을 진행하겠습니다.

2. 먼저 mongoDB 인스턴스가 실행되고 있는지 확인합니다.

    `$sudo mongod`

3. 그런 다음 해당 디렉터리로 변경하고 curl을 시작합니다.

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4. 그런 다음 다음과 같은 방식으로 작업을 추가할 수 있습니다.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    응답은 다음과 같습니다.

        ```Shell
        HTTP/1.1 201 Created
        Connection: close
        Access-Control-Allow-Origin: *
        Access-Control-Allow-Headers: X-Requested-With
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5
        Date: Tue, 04 Feb 2014 01:02:26 GMT
        Hello
        ```
    또한 다음 방식으로 Brandon에 대한 작업을 나열할 수 있습니다.

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

이 모든 작업이 끝나면 REST API 서버에 OAuth를 추가할 준비가 완료된 것입니다.

MongoDB를 사용하는 REST API 서버가 완료되었습니다.

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>18단계: REST API 서버에 인증 추가
이제 REST API가 실행되고 있으니 Azure AD를 사용하여 더욱 유용하게 만들어 보겠습니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>passport-azure-ad에 포함된 OIDCBearerStrategy 사용
지금까지 권한 부여 없이 일반적인 REST TODO 서버를 빌드했습니다. 이 지점에서 통합 작업을 시작합니다.

1. 먼저, Passport를 사용하도록 지정해야 합니다. 다른 서버 구성 바로 뒤에 이 코드를 추가합니다.

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > API를 작성할 때 항상 사용자가 스푸핑할 수 없는 토큰의 고유한 항목에 데이터를 연결하는 것이 좋습니다. 이 서버는 TODO 항목을 저장할 때 "owner" 필드에 넣은 토큰(token.oid를 통해 호출됨)의 사용자 개체 ID를 기준으로 항목을 저장합니다. 이렇게 하면 해당 사용자만 자신의 TODO 항목에 액세스할 수 있습니다. API에 "owner"가 노출되지 않으므로 외부 사용자가 인증된 경우에도 다른 사용자의 TODO 항목을 요청할 수 있습니다.                    

2. 다음으로 `passport-azure-ad`와 함께 제공되는 전달자 전략을 사용합니다. 지금은 코드만 살펴보고 나머지는 잠시 후에 설명하겠습니다. 위에서 붙여넣은 코드 뒤에 이 코드를 넣습니다.

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
    **/

    var findById = function(id, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            if (user.sub === id) {
                log.info('Found user: ', user);
                return fn(null, user);
            }
        }
        return fn(null, null);
    };


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

Passport는 모든 전략 작성자가 준수하는 유사한 패턴을 모든 전략(Twitter, Facebook 등)에 대해 사용합니다. 전략을 보면 토큰 및 완료가 매개 변수로 포함된 함수가 전달되는 것을 확인할 수 있습니다. 전략은 작업을 완료한 후에 사용자에게 돌아갑니다. 전략이 돌아오면 토큰을 다시 요청할 필요가 없도록 사용자를 저장하고 토큰을 보관합니다.

> [!IMPORTANT]
> 이전 코드는 서버에 인증하는 모든 사용자를 사용합니다. 이를 자동 등록이라고 합니다. 프로덕션 서버에서는 사용자가 결정한 등록 프로세스를 통과하지 않은 사람에게는 액세스를 허용하지 않는 것이 좋습니다. 이것은 Facebook에 등록할 수 있도록 허용하지만 추가 정보를 입력하도록 요구하는 소비자 앱에서 주로 나타나는 패턴입니다. 명령줄 프로그램이 아니라면 반환된 토큰 개체에서 메일을 추출하고 사용자에게 추가 정보를 입력하도록 요구할 수 있었을 것입니다. 테스트 서버이므로 메모리 내 데이터베이스에 추가합니다.
>
>

### <a name="protect-some-endpoints"></a>일부 끝점 보호
사용하려는 프로토콜을 통해 `passport.authenticate()` 호출을 지정하여 끝점을 보호합니다.

서버 코드에서 좀 더 흥미로운 작업을 수행하도록 경로를 편집해 보겠습니다.

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>19단계: 서버 응용 프로그램을 다시 실행하고 사용자를 거부하는지 확인
`curl`을 다시 사용하여 끝점에 대해 OAuth2 보호가 적용되는지 확인해 보겠습니다. 이 끝점에 대해 클라이언트 SDK 중 하나를 실행하기 전에 이 작업을 수행합니다. 반환되는 헤더는 현재 우리가 올바른 경로에 있는지 알려줄 만큼 충분해야 합니다.

1. 먼저 mongoDB 인스턴스가 실행되고 있는지 확인합니다.

    `$sudo mongod`

2. 그런 다음 해당 디렉터리로 변경하고 curl을 시작합니다.

      `$ cd azuread`
      `$ node server.js`

3. 기본 POST를 시도합니다.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

여기서 우리가 찾는 응답은 401입니다. 이 응답은 Passport 계층이 인증된 끝점으로 리디렉션을 시도하고 있음을 나타냅니다. 정확하게 우리가 원하는 대로 되었습니다.

## <a name="next-steps"></a>다음 단계
OAuth2 호환 클라이언트를 사용하지 않고 이 서버로 수행할 수 있는 작업은 모두 완료했습니다. 이제 추가 연습 과정을 진행해야 합니다.

Restify 및 OAuth2를 사용하여 REST API를 구현하는 방법을 배웠습니다. 또한 계속해서 서비스를 개발하고 이 예제를 바탕으로 구축하는 데 도움이 되는 코드를 충분히 제공해 드렸습니다.

ADAL 과정의 다음 단계에 관심이 있는 경우 여기서 권장하는 지원되는 ADAL 클라이언트로 계속 진행하세요.

개발자 컴퓨터에 복제하고 연습에 설명된 대로 구성하면 됩니다.

[iOS용 ADAL(영문)](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[Android용 ADAL(영문)](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

