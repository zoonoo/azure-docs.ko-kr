<properties
	pageTitle="Azure 모바일 앱용 JavaScript SDK를 사용하는 방법"
	description="Azure 모바일 앱에 v를 사용하는 방법"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Azure 모바일 앱용 JavaScript 클라이언트 라이브러리를 사용하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 최신 [Azure 모바일 앱용 JavaScript SDK]를 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure 모바일 앱을 처음 접하는 경우 먼저 [Azure 모바일 앱 빠른 시작]을 완료하여 백 엔드를 만들고 테이블을 만듭니다. 이 가이드에서는 HTML/JavaScript 웹 응용 프로그램에서 모바일 백 엔드를 사용하는 데 초점을 둡니다.

##<a name="Setup"></a>설정 및 필수 조건

이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다.

`npm` 명령을 통해 Azure 모바일 앱 JavaScript SDK를 설치할 수 있습니다.

```
npm install azure-mobile-apps-client --save
```

설치되면 라이브러리는 `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.js`에 위치합니다. 웹 영역에 이 파일을 복사합니다.

```
<script src="path/to/MobileServices.Web.js"></script>
```

또한 라이브러리는 Browserify 및 Webpack과 같은 CommonJS 환경 내에서 ES2015 모듈로 사용할 수 있으며 AMD 라이브러리로 사용할 수도 있습니다. 예:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>방법: 사용자 인증

Azure 앱 서비스는 Facebook, Google, Microsoft 계정 및 Twitter와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [인증 시작] 자습서를 참조하십시오.

두 가지의 인증 흐름, 즉 서버 흐름과 클라이언트 흐름이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자별 SDK를 사용하므로 Single Sign-On과 같은 장치 특정 기능을 통해 심도 깊은 통합이 가능합니다.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>방법: 푸시 알림 등록

푸시 알림을 처리하기 위해 [phonegap-plugin-push]를 설치합니다. 명령줄에서 `cordova plugin add` 명령을 사용하거나 Visual Studio 내에서 Git 플러그 인 설치 관리자를 통해 쉽게 추가할 수 있습니다. Apache Cordova 앱에서 다음 코드는 푸시 알림을 위해 장치를 등록합니다.

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

알림 허브 SDK를 사용하여 서버에서 푸시 알림을 보냅니다. 알림 허브 또는 PNS에 대해 서비스 거부 공격을 트리거하는 데 사용될 수 있으므로 클라이언트에서 직접 푸시 알림을 보내서는 안 됩니다.

<!-- URLs. -->
[Azure 모바일 앱 빠른 시작]: app-service-mobile-cordova-get-started.md
[인증 시작]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/ko-KR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0309_2016-->