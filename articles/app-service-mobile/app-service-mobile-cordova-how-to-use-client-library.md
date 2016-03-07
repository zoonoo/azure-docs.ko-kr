<properties
	pageTitle="Azure 모바일 앱용 Apache Cordova 플러그인 사용 방법"
	description="Azure 모바일 앱용 Apache Cordova 플러그인 사용 방법"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Azure 모바일 앱용 Apache Cordova 클라이언트 라이브러리를 사용하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 최신 [Azure 모바일 앱용 Apache Cordova 플러그 인]을 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure 모바일 앱을 처음 접하는 경우 먼저 [Azure 모바일 앱 빠른 시작]을 완료하여 백 엔드를 만들고, 테이블을 만든 다음 미리 빌드된 Apache Cordova 프로젝트를 다운로드합니다. 이 가이드에서는 클라이언트 쪽 Apache Cordova 플러그 인에 초점을 둡니다.

##<a name="Setup"></a>설정 및 필수 조건

이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다. 또한 이 가이드는 사용자 코드에 Apache Cordova 플러그 인을 추가했다고 가정합니다. 아직 추가하지 않은 경우 명령줄에서 프로젝트에 Apache Cordova 플러그 인을 추가할 수 있습니다.

```
cordova plugin add ms-azure-mobile-apps
```

[첫 번째 Apache Cordova 앱]을 만드는 방법에 대한 자세한 내용은 해당 설명서를 참조하세요.

##<a name="create-client"></a>방법: 클라이언트 만들기

`WindowsAzure.MobileServicesClient` 개체를 만들어 클라이언트 연결을 만듭니다. `appUrl`을 모바일 앱에 대한 URL로 바꿉니다.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>방법: 테이블 참조 만들기

데이터에 액세스하거나 데이터를 업데이트하려면 백 엔드 테이블에 대한 참조를 만듭니다. `tableName`을 테이블의 이름으로 바꿉니다.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>방법: 테이블 참조 쿼리

테이블 참조가 있는 경우 이를 사용하여 서버에서 데이터를 쿼리할 수 있습니다. 쿼리는 "LINQ와 유사한" 언어로 만들어집니다. 테이블에서 모든 데이터를 반환하려면 다음을 사용합니다.

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

결과에 대해 success 함수가 호출됩니다. success 함수에 `for (var i in results)`를 사용하지 마세요. 다른 쿼리 함수(예: `.includeTotalCount()`)가 사용될 경우 결과에 포함된 정보를 반복합니다.

쿼리 구문에 대한 자세한 내용은 [쿼리 개체 설명서]를 참조하세요.

### 서버에서 데이터 필터링

테이블 참조에서 `where` 절을 사용할 수 있습니다.

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

개체를 필터링하는 함수도 사용할 수 있습니다. 이 경우 `this` 변수가 필터링되는 현재 개체에 할당됩니다. 다음은 이전 예와 기능적으로 동일합니다.

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### 데이터를 통한 페이징

take() 및 skip() 메서드를 활용합니다. 예를 들어 테이블을 100개 행 레코드로 분할하려는 경우 다음과 같습니다.

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()` 메서드는 결과 개체에 totalCount 필드를 추가하는 데 사용됩니다. totalCount 필드는 페이징이 사용되지 않는 경우 반환되는 총 레코드 수로 채워집니다.

다음으로 페이지 변수 및 몇 가지 UI 단추를 사용하여 페이지 목록을 제공할 수 있습니다. 각 페이지에 대해 새 레코드를 로드하는 데 loadPage()를 사용합니다. 이미 로드된 레코드에 대한 액세스 속도를 높이려면 일종의 캐싱을 구현해야 합니다.


###<a name="sorting-data"></a>방법: 정렬된 데이터 반환

.orderBy() 또는 .orderByDescending() 쿼리 메서드를 사용합니다.

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

쿼리 개체에 대한 자세한 내용은 [쿼리 개체 설명서]를 참조하세요.

##<a name="inserting"></a>방법: 데이터 삽입

적절한 날짜로 JavaScript 개체를 만들고 table.insert()를 비동기적으로 호출합니다.

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

성공적으로 삽입되면 동기화 작업에 필요한 추가 필드와 함께 삽입된 항목이 반환됩니다. 이후 업데이트를 위해 사용자 고유의 캐시를 이 정보로 업데이트해야 합니다.

Azure 모바일 앱 Node.js 서버 SDK는 개발 목적으로 동적 스키마를 지원합니다. 동적 스키마의 경우 테이블의 스키마가 즉시 업데이트되어 삽입 또는 업데이트 작업 시 지정하는 것만으로 테이블에 열을 추가할 수 있습니다. 응용 프로그램을 프로덕션으로 전환하기 전에 동적 스키마를 해제하는 것이 좋습니다.

##<a name="modifying"></a>방법: 데이터 수정

.insert() 메서드와 마찬가지로 업데이트 개체를 만든 후 .update()를 호출해야 합니다. 업데이트 개체는 업데이트할 레코드의 ID를 포함해야 하며 레코드를 읽거나 .insert()를 호출할 때 얻을 수 있습니다.

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>방법: Blob 삭제

레코드를 삭제하려면 .del() 메서드를 호출합니다. 개체 참조에 ID를 전달합니다.

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>방법: 사용자 인증

Azure 앱 서비스는 Facebook, Google, Microsoft 계정 및 Twitter와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [인증 시작] 자습서를 참조하십시오.

Apache Cordova 앱에 인증을 사용할 때는 다음 Cordova 플러그 인을 사용할 수 있어야 합니다.

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

두 가지의 인증 흐름, 즉 서버 흐름과 클라이언트 흐름이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 장치별 SDK를 사용하므로 Single Sign-On과 같은 장치 특정 기능을 통해 심도 깊은 통합이 가능합니다.

##<a name="server-auth"></a>방법: 공급자를 사용하여 인증(서버 흐름)

모바일 서비스가 Windows 스토어 또는 HTML5 앱에서 인증 프로세스를 관리하게 하려면 앱을 ID 공급자에 등록해야 합니다. 그런 다음, Azure 앱 서비스에서 공급자로부터 제공된 응용 프로그램 ID 및 암호를 구성해야 합니다. 자세한 내용은 [앱에 인증 추가] 자습서를 참조하세요.

ID 공급자를 등록하고 나면 공급자의 이름을 사용하여 .login() 메서드를 호출합니다. 예를 들어 Facebook으로 로그인하려면 다음 코드를 사용합니다.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Facebook 이외의 ID 공급자를 사용하는 경우 위의 login 메서드에 전달된 값을 `microsoftaccount`, `facebook`, `twitter`, `google`, `aad` 중 하나로 변경합니다.

이 경우 Azure 앱 서비스는 선택한 공급자의 로그인 페이지를 표시하고 ID 공급자 로그인 후 앱 서비스 인증 토큰을 생성하여 OAuth 2.0 인증 흐름을 관리합니다. login 함수를 완료하면 사용자 ID와 앱 서비스 인증 토큰을 각각 userId 및 authenticationToken 필드에 표시하는 JSON 개체(user)가 반환됩니다. 이 토큰은 캐시했다가 만료될 때까지 다시 사용할 수 있습니다.

##<a name="client-auth"></a>방법: 공급자를 사용하여 인증(클라이언트 흐름)

앱이 독립적으로 ID 공급자에 연결한 후 반환된 토큰을 인증을 위해 앱 서비스에 제공할 수도 있습니다. 이 클라이언트 흐름을 사용하면 단일 로그인 환경을 사용자에게 제공하거나 ID 공급자로부터 더 많은 사용자 데이터를 검색할 수 있습니다.

### 소셜 인증 기본 예제

이 예제에서는 인증을 위해 Facebook 클라이언트 SDK를 사용합니다.

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
이 예제에서는 각 공급자 SDK에서 제공된 토큰이 token 변수에 저장되어 있다고 가정합니다.

### Microsoft 계정 예제

다음 예제는 Microsoft 계정을 사용하여 Windows 스토어 앱용 Single Sign-On을 지원하는 Live SDK를 사용합니다.

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

이 예제는 login 함수를 호출하여 앱 서비스에 제공된 토큰을 Live Connect에서 가져옵니다.

##<a name="auth-getinfo"></a>방법: 인증된 사용자에 대한 정보 얻기

모든 AJAX 메서드를 사용하여 현재 사용자에 대한 인증 정보를 `/.auth/me` 끝점에서 검색할 수 있습니다. 예를 들어, fetch API를 사용하려면 다음과 같이 합니다.

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

또한 jQuery 또는 AJAX API를 사용하여 정보를 가져올 수도 있습니다. 데이터는 JSON 개체로 수신됩니다.

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
[앱에 인증 추가]: app-service-mobile-cordova-get-started-users.md

[Azure 모바일 앱용 Apache Cordova 플러그 인]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[첫 번째 Apache Cordova 앱]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[쿼리 개체 설명서]: https://msdn.microsoft.com/ko-KR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->