<properties
	pageTitle="Azure AD NodeJS 시작 | Microsoft Azure"
	description="인증을 위해 Azure AD와 통합되는 Node.js Web API를 빌드하는 방법"
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# 노드에 대한 WEB-API 시작

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

이 연습에서는 OAuth2 프로토콜을 사용하여 API 보호를 위해 Azure Active Directory와 통합되는 REST API Service를 설정하는 빠르고 쉬운 방법을 제공합니다. 다운로드에 포함된 샘플 서버는 어떤 플랫폼에서도 실행되도록 디자인되었지만 OSX와 Linux를 대상으로 합니다.

이 연습을 마치면 실행 중인 REST API 서버를 다음 기능으로 빌드할 수 있습니다.

* MongoDB를 영구 저장소로 사용하여 JSON으로 REST API 인터페이스를 실행하는 node.js 서버
* Azure Active Directory를 사용하여 전달자 토큰으로 OAuth2 API 보호 기능을 활용하는 REST API


Microsoft는 이 실행 예제의 모든 소스 코드를 GitHub의 Apache 2.0 라이선스에 출시했으므로 자유롭게 복제(및 분기)하고 피드백을 제공하고 요청을 끌어올 수 있습니다.

## Node.js 모듈 정보

이 연습에서는 Node.js 모듈을 사용합니다. 모듈은 응용 프로그램의 특정 기능을 제공하는 로드 가능한 JavaScript 패키지입니다. 일반적으로 NPM 설치 디렉터리에 Node.js NPM 명령줄 도구를 사용하여 모듈을 설치하지만 HTTP 모듈과 같은 일부 모듈이 핵심 Node.js 패키지에 포함되어 있습니다. 설치된 모듈은 Node.js 설치 디렉터리의 루트에 있는 node_modules 디렉터리에 저장됩니다. node_modules 디렉터리 내의 각 모듈은 종속되는 모든 모듈이 포함된 고유한 node_modules 디렉터리를 유지합니다. 이러한 반복되는 디렉터리 구조는 종속성 체인을 나타냅니다.

이 종속성 체인 구조 때문에 응용 프로그램 메모리 사용량이 증가하지만 모든 종속성이 충족되고 개발에 사용되는 모듈의 버전을 프로덕션 환경에서도 사용할 수 있게 됩니다. 따라서 프로덕션 앱 동작을 예측하기 쉬워지고 사용자에게 영향을 줄 수 있는 버전 문제도 방지됩니다.

## 1단계: Azure AD 테넌트 등록

이 샘플을 사용하려면 Azure Active Directory 테넌트가 필요합니다. 테넌트가 무엇인지 또는 어떻게 가져오는지 잘 모를 경우 [Azure AD 테넌트를 가져오는 방법](active-directory-howto-tenant.md)을 참조하세요.

## 2단계: 테넌트에 Web API 추가

Azure Active Directory 테넌트를 가져온 후에 이 샘플 앱을 테넌트에 추가하여 API를 보호하는 데 사용할 수 있도록 합니다.

앱에서 사용자를 인증할 수 있게 하려면 먼저 새 응용 프로그램을 테넌트에 등록해야 합니다.

- Azure 관리 포털에 로그인합니다.
- 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
- 응용 프로그램을 등록할 테넌트를 선택합니다.
- **응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 추가를 클릭합니다.
- 프롬프트에 따라 새 **웹 응용 프로그램 및/또는 WebAPI**를 만듭니다.
    - 응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**로그온 URL**은 앱의 기본 URL입니다. 기본값은 `https://localhost:8888`입니다.
    - **앱 ID URI**는 응용 프로그램의 고유 식별자입니다. 규칙은 `https://<tenant-domain>/<app-name>`(예: `https://contoso.onmicrosoft.com/my-first-aad-app`)을 사용하는 것입니다.
- 등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 구성 탭에서 복사해둡니다.

## 3단계: 사용자 플랫폼을 위한 node.js 다운로드
이 샘플을 사용하려면 작동하는 Node.js 설치가 있어야 합니다.

[http://nodejs.org](http://nodejs.org)에서 Node.js를 설치합니다.

## 4단계: 사용 중인 플랫폼에 MongoDB 설치

이 샘플을 사용하려면 작동하는 MongoDB 설치가 있어야 합니다. MongoDB를 사용하여 REST API가 서버 인스턴스 간에 지속되도록 할 것입니다.

[http://mongodb.org](http://www.mongodb.org)에서 MongoDB를 설치합니다.

**참고:** 이 연습에서는 연습 과정을 작성할 때의 MongoDB에 대한 기본 설치 및 서버 끝점인 mongodb://localhost를 사용한다고 가정합니다.


## 5단계: Web API에 Restify 모듈 설치

Resitfy를 사용하여 REST API를 작성할 것입니다. Restify는 Connect 위에 REST API를 구축하기 위한 강력한 기능 집합이 있는 Express에서 파생된 유연한 최소 Node.js 응용 프로그램 프레임워크입니다.

### Restify 설치

명령줄에서 디렉터리를 azuread 디렉터리로 변경합니다. **azuread** 디렉터리가 없으면 만듭니다.

`cd azuread - or- mkdir azuread; cd azuread`

다음 명령을 입력합니다.

`npm install restify`

이 명령은 Restify를 설치합니다.

#### 오류가 발생했나요?

일부 운영 체제에서 rpm을 사용할 때 Error: EPERM, chmod '/usr/local/bin/..' 오류 메시지와 관리자 계정으로 실행하라는 요청이 표시될 수 있습니다. 이런 경우 sudo 명령을 사용하여 더 높은 권한 수준으로 npm을 실행하세요.

#### DTrace 관련 오류가 발생했나요?

Restify를 설치할 때 다음과 같은 내용이 표시될 수 있습니다.

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


Restify는 DTrace를 사용하여 REST 호출을 추적하는 강력한 메커니즘을 제공합니다. 그러나 대부분의 운영 체제에서는 DTrace를 사용할 수 없습니다. 이러한 오류는 무시해도 됩니다.


이 명령의 출력은 다음과 유사하게 표시됩니다.


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


## 6단계: Web API에 Passport.js 설치

[Passport](http://passportjs.org/)는 Node.js에 대한 인증 미들웨어입니다. 매우 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Azure Active Directory 전략 플러그 인을 추가해 보겠습니다.

명령줄에서 디렉터리를 azuread 디렉터리로 변경합니다.

다음 명령을 입력하여 passport.js를 설치합니다.

`npm install passport`

이 명령의 출력은 다음과 유사하게 표시됩니다.

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 7단계: Web API에 Passport.js 전달자 토큰 지원 추가

다음으로 [Passport](http://passportjs.org/)용 전달자 처리기인 passport-bearer-http를 사용하여 전달자 전략을 추가합니다. 또한 node-jwt를 사용하여 JWT 토큰 처리기 지원을 추가합니다.

**참고:** OAuth2는 알려진 모든 토큰 유형이 발급될 수 있는 프레임워크를 제공하지만 특정 토큰 유형만 일반적으로 사용할 수 있습니다. 끝점 보호를 위해 이러한 토큰 유형은 전달자 토큰으로 확인되었습니다. 전달자 토큰은 OAuth2에서 가장 널리 발급되는 토큰 유형이며 많은 구현에서는 발급되는 유일한 토큰 유형이 전달자 토큰이라고 가정합니다.

명령줄에서 디렉터리를 **azuread** 디렉터리로 변경합니다.

다음 명령을 입력하여 Passport.js 모듈을 설치합니다.

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

이 명령의 출력은 다음과 유사하게 표시됩니다.

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## 8단계: Web API에 MongoDB 모듈 추가

MongoDB를 데이터 저장소로 사용할 예정입니다. 따라서 널리 사용되는 두 플러그 인을 설치해야 합니다. 하나는 모델 및 스키마를 관리하기 위한 Mongoose 플러그 인이고 다른 하나는 MongoDB용 데이터베이스 드라이버인 MongoDB 플러그 인입니다.


* `npm install mongoose`
* `npm install mongodb`

## 9단계: 추가 모듈 설치

다음에는 나머지 필수 모듈을 설치합니다.


아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`


다음 명령을 입력하여 node_modules 디렉터리에 다음 모듈을 설치합니다.

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 10단계: 종속성을 적용하여 server.js 만들기

server.js 파일이 Web API 서버의 기능 대부분을 제공하게 됩니다. 이 파일에 대부분의 코드를 추가할 것입니다. 프로덕션 목적으로, 기능을 별도 경로 및 컨트롤러와 같은 좀 더 작은 파일로 리팩터링합니다. 이 데모의 목적에 맞게 이 기능을 위해 server.js를 사용합니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 만들고 다음 정보를 추가합니다.

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
```

파일을 저장합니다. 잠시 후에 이 파일로 다시 돌아갈 것입니다.

## 11단계: Azure AD 설정을 저장하기 위한 구성 파일 만들기

이 코드 파일은 Azure Active Directory 포털의 구성 매개 변수를 Passport.js에 전달합니다. 이 연습의 첫 번째 부분에서 포털에 Web API를 추가했을 때 이러한 구성 값을 만들었습니다. 코드를 복사한 후에 이러한 매개 변수 값에 추가할 항목에 대해 설명할 것입니다.


아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `config.js` 파일을 만들고 다음 정보를 추가합니다.

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**참고:** 이러한 값은 절대 변경할 필요가 없습니다.

**참고:** 키는 자주 롤링하게 됩니다. 항상 "openid_keys" URL에서 끌어오고 있으며 앱에서 인터넷에 액세스할 수 있는지 확인하세요.


## 12단계: server.js 파일에 구성 추가

응용 프로그램에서 방금 만든 구성 파일로부터 이러한 값을 읽어야 합니다. 이를 위해 응용 프로그램에 필수 리소스로 .config 파일을 추가하고 전역 변수를 config.js 문서의 변수로 설정합니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 다음 정보를 추가합니다.

```Javascript
var config = require('./config');
```
그다음에 다음 코드를 사용하여 `server.js`에 새 섹션을 추가합니다.

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## 13단계: 메타데이터/토큰을 구문 분석에 도움이 되는 metadata.js 도우미 파일 만들기

server.js 파일에 응용 프로그램 논리만 유지하면 되므로 일부 도우미 메서드는 별도 파일에 넣을 수 있습니다. 이러한 메서드는 OpenID Connect 메타데이터를 구문 분석하는 데만 도움이 되며 핵심 시나리오와는 관련이 없습니다. 따라서 즉시 삭제하는 것이 좋습니다. 이 연습을 진행하면서 이 파일에 다른 항목을 추가할 것입니다.

***참고:*** 이 metadata.js 파일은 SAML 및 WS-Fed용 XML과 OpenID Connect용 JSON을 구문 분석합니다. 따라서 다른 샘플에서도 이 파일을 사용할 수 있습니다. 지금은 무시해도 됩니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `metadata.js` 파일을 만들고 다음 정보를 추가합니다.

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
코드에서 볼 수 있듯이 이 파일은 `config.js`에 전달한 openid URL을 가져와 구문 분석하여 `server.js` 파일에서 사용하게 되는 정보를 얻습니다. 이 코드를 조사하고 필요한 경우 확장할 수 있습니다.

### server.js에 metadata.js 파일 로드

방금 작성한 메서드를 가져올 위치를 서버에 알려야 합니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 다음 정보를 추가합니다.

```Javascript
var metadata = require('./metadata);
```
다음으로, `Configuration` 섹션의 끝에 이 호출을 추가하여 방금 작성한 파서에 `config.js`의 메타데이터 문서를 보냅니다.

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## 14단계: Moongoose를 사용하여 MongoDB 모델 및 스키마 정보 추가

이제 이러한 세 개의 파일이 하나의 REST API 서비스에 통합되었으므로 이러한 모든 준비 과정을 마무리할 때가 되었습니다.

이 연습에서는 ***4단계***에 설명된 것처럼 MongoDB를 사용하여 작업을 저장할 것입니다.

***11단계***에서 만든 `config.js` 파일에서 기억할 수 있는 것처럼 mogoose_auth_local 연결 URL의 끝에 추가했던 데이터베이스 `tasklist`를 호출했습니다. 서버 응용 프로그램을 처음 실행할 때 생성되므로(존재하지 않을 경우) MongoDB에서 이 데이터베이스를 미리 만들 필요는 없습니다.

사용하려는 MongoDB 데이터베이스를 서버에 알렸으므로 일부 추가 코드를 작성하여 서버 작업에 대한 모델 및 스키마를 만들어야 합니다.

#### 모델에 대한 논의

스키마 모델은 매우 간단하며 필요에 따라 확장할 수 있습니다.

NAME - 작업에 할당된 사람의 이름입니다. ***String***

TASK - 작업 자체입니다. ***String***

DATE - 작업이 만료되는 날짜입니다. ***DATETIME***

COMPLETED - 작업이 완료되었는지 여부입니다. ***BOOLEAN***

#### 코드에 스키마 만들기


아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 구성 항목 아래에 다음 정보를 추가합니다.

```Javascript
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
이렇게 하면 MongoDB 서버에 연결되고 스키마 개체가 다시 전달됩니다.

#### 스키마를 사용하여 코드에서 모델 만들기

위에서 작성한 코드 아래에 다음 코드를 추가합니다.

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

var TaskSchema = new Schema({
  owner: String,
  task: String,
  completed: Boolean,
  date: Date
});

// Use the schema to register a model

mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
코드에서 알 수 있듯이 스키마를 만든 다음, ***Routes***를 정의할 때 코드 전체에서 데이터를 저장하는 데 사용할 모델 개체를 만듭니다.

## 15단계: Task REST API 서버에 대한 경로 추가

작업할 데이터베이스 모델이 준비되었으므로 REST API 서버에 사용할 경로를 추가하겠습니다.

### Restify의 경로 정보

경로는 Express 스택을 사용할 때와 정확히 동일한 방식으로 Restify에서 작동합니다. 클라이언트 응용 프로그램이 호출할 것으로 예상하는 URI를 사용하여 경로를 정의합니다. 일반적으로 경로는 별도 파일에 정의합니다. 여기서는 경로를 server.js 파일에 저장할 것입니다. 프로덕션 사용을 위해서는 자체 파일에서 이러한 경로를 팩터링하는 것이 좋습니다.

Restify 경로의 일반적인 패턴:

```Javascript
function createObject(req, res, next) {

// do work on Object

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // keep the server going
}

....

server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.

```


가장 기본적인 수준의 패턴입니다. Resitfy(및 Express)는 응용 프로그램 유형 정의 및 여러 다른 끝점에서 복잡한 라우팅 수행 등의 훨씬 더 수준 높은 기능을 제공합니다. 여기서는 이러한 경로를 매우 간단하게 유지할 것입니다.

#### 서버에 기본 경로 추가

이제 만들기, 검색, 업데이트 및 삭제의 기본 CRUD 경로를 추가할 것입니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 위에서 작성한 데이터베이스 항목 아래에 다음 정보를 추가합니다.

```Javascript

/**
 *
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

        res.json(data);

        }
  });

  return next();
}
```

### 경로에 대한 일부 오류 처리 추가

발생한 문제를 클라이언트가 이해할 수 있는 방식으로 다시 전달할 수 있도록 일부 오류 처리를 추가할 수 있습니다.

위에서 작성한 코드 아래에 다음 코드를 추가합니다.

```Javascript
///--- Errors for communicating something interesting back to the client

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## 16단계: 서버 만들기

데이터베이스를 정의했으며 경로가 올바르게 지정되었으므로 마지막으로 호출을 관리하는 서버 인스턴스를 추가할 것입니다.

Restify(및 Express)는 REST API 서버에 대해 사용자가 수행할 수 있는 고급 사용자 지정 기능을 많이 제공하지만 여기서는 가장 기본적인 설정만 사용할 것입니다.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
});

        // Ensure we don't drop data on uploads
        server.pre(restify.pre.pause());

        // Clean up sloppy paths like //todo//////1//
        server.pre(restify.pre.sanitizePath());

        // Handles annoying user agents (curl)
        server.pre(restify.pre.userAgentConnection());

        // Set a per request bunyan logger (with requestid filled in)
        server.use(restify.requestLogger());

        // Allow 5 requests/second by IP, and burst to 10
        server.use(restify.throttle({
                burst: 10,
                rate: 5,
                ip: true,
        }));

        // Use the common stuff you probably want
        server.use(restify.acceptParser(server.acceptable));
        server.use(restify.dateParser());
        server.use(restify.queryParser());
        server.use(restify.gzipResponse());

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## 17단계: OAuth 지원을 추가하기 전에 서버를 실행하겠습니다.

연습의 OAuth 부분까지 계속 진행하기 전에 실수가 없는지 확인하는 것이 좋습니다.

이를 위한 가장 쉬운 방법은 명령줄에서 `curl`을 사용하는 것입니다. 이 작업을 수행하기 전에 JSON처럼 출력을 구문 분석할 수 있도록 하는 간단한 유틸리티가 필요합니다. 이를 위해 [json](https://github.com/trentm/json) 도구를 설치합니다. 이 도구는 아래의 모든 예제에서 사용됩니다.

	$npm install -g jsontool

이렇게 하면 JSON 도구가 전역적으로 설치됩니다. 이 작업은 완료했으므로 서버 작업을 진행하겠습니다.

먼저 monogoDB 인스턴스가 실행되고 있는지 확인합니다.

	$sudo mongod

그런 다음 해당 디렉터리로 변경하고 curl을 시작합니다.

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

그런 다음 다음과 같은 방식으로 작업을 추가할 수 있습니다.

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

응답은 다음과 같습니다.

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

또한 다음 방식으로 Brandon에 대한 작업을 나열할 수 있습니다.

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

이러한 모든 작업이 수행되면 REST API 서버에 OAuth를 추가할 준비가 완료된 것입니다.

## 18단계: REST API 서버에 Passport.js 코드 추가

작동하는 REST API를 만들었으므로 이제 Azure AD에서 유용하게 사용할 수 있게 만들어 보겠습니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

### 1단계: Passport 모듈 추가

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 앞서, 로드할 모듈을 명시한 위치 아래에 다음 정보를 추가합니다. 이 위치는 파일 맨 위쪽이며 `var aadutils = require('./aadutils');` 가져오기 바로 다음입니다.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2. 인증을 사용하고 있음을 서버에 알리기

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 경로를 정의한 **server.get()의 아래**, **server.listen()** 메서드의 위에 다음 정보를 추가합니다.


Restify에 해당 `authorizationParser()`를 사용할 것을 알리고 권한 부여 헤더의 내용을 확인해야 합니다.

```Javascript
        server.use(restify.authorizationParser());


```


### 3. 코드에 Passport OAuth2 모듈 추가

여기서는 config.js 파일에 추가한 특정 OAuth2 매개 변수를 사용합니다. `aadutils.js` 파일이 페더레이션 메타데이터 문서 구문 분석 작업을 수행하면 이러한 모든 값은 config.js 파일에는 비어 있더라도 자동으로 채워집니다.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### 4단계: OAuth 인증에 대한 경로 추가

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### 5단계: 경로에 IsAuthenticated() 도우미 메서드 추가

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### 6단계: 쿠키에 대한 캐싱 메커니즘 추가

```Javascript
// Passport session setup.
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
```
### 7단계: 마지막으로 일부 끝점 보호

사용하려는 프로토콜을 통해 passport.authenticate() 호출을 지정하여 끝점을 보호합니다.

서버 코드에서 경로를 편집하여 좀 더 흥미로운 작업을 수행해 보겠습니다.

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## 19단계: 서버 응용 프로그램을 다시 실행하고 사용자를 거부하는지 확인

`curl`을 다시 사용하여 끝점에 대해 OAuth2 보호가 적용되는지 확인해 보겠습니다. 이 끝점에 대해 클라이언트 SDK 중 하나를 실행하기 전에 이 작업을 수행할 것입니다. 반환되는 헤더에는 올바른 경로를 따르고 있음을 알려줄 수 있는 충분한 정보가 포함되어야 합니다.

먼저 monogoDB 인스턴스가 실행되고 있는지 확인합니다.

	$sudo mongod

그런 다음 해당 디렉터리로 변경하고 curl을 시작합니다.

	$ cd azuread
	$ node server.js

기본 GET을 시도합니다.

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


302는 원하던 것처럼 Passport 계층이 끝점을 인증하기 위해 리디렉션하려고 함을 나타내므로 여기에서 찾아야 하는 응답입니다.

## 축하합니다. OAuth2를 사용하는 REST API Service가 완료되었습니다.

OAuth2 호환 클라이언트를 사용하지 않고 이 서버로 수행할 수 있는 작업은 모두 완료했습니다. 이제 추가 연습 과정을 진행해야 합니다.

Restify 및 OAuth2를 사용하여 REST API를 구현하는 방법에 대한 정보만 원한다면 지금까지 제공된 코드만으로도 서비스를 계속 개발하고 이 예제의 빌드 방법을 계속 배울 수 있습니다.

ADAL 과정의 다음 단계에 관심이 있는 경우 여기서 권장하는 ADAL 클라이언트를 사용하여 계속 작업하세요.

간단히 개발자 컴퓨터를 복제하고 연습에 설명된 대로 구성하면 됩니다.

[iOS용 ADAL](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)(영문)

[Android용 ADAL](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)(영문)

[.NET용 ADAL](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)(영문)
 

<!---HONumber=58_postMigration-->