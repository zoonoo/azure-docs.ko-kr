<properties
	pageTitle="모바일 서비스에서 Azure 앱 서비스로 업그레이드 - Node.js"
	description="모바일 서비스 응용 프로그램을 앱 서비스 모바일 앱으로 쉽게 업그레이드하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="chrande"/>

# 기존 Node.js Azure 모바일 서비스를 앱 서비스로 업그레이드

앱 서비스 모바일은 Microsoft Azure를 사용하여 모바일 응용 프로그램을 빌드하는 새로운 방법입니다. 자세한 내용은 [모바일 앱 정의]를 참조하세요.

이 항목에서는 기존 Node.js 백 엔드 응용 프로그램을 Azure 모바일 서비스에서 새로운 앱 서비스 모바일 앱으로 업그레이드하는 방법을 설명합니다. 이 업그레이드를 수행하는 동안 기존 모바일 서비스 응용 프로그램이 계속 작동할 수 있습니다.

모바일 백 엔드가 Azure 앱 서비스로 업그레이드되면 모든 앱 서비스 기능에 액세스할 수 있고 모바일 서비스 가격 책정이 아닌 [앱 서비스 가격 책정]에 따라 요금이 청구됩니다.

## 마이그레이션과 업그레이드 비교

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] 업그레이드를 진행하기 전에 [마이그레이션을 수행](app-service-mobile-migrating-from-mobile-services.md)하는 것이 좋습니다. 이러한 방식으로 동일한 앱 서비스 계획에 두 버전의 응용 프로그램을 모두 추가 비용 없이 둘 수 있습니다.

### 모바일 앱 Node.js 서버 SDK에서 향상된 기능

새 [모바일 앱 SDK](https://www.npmjs.com/package/azure-mobile-apps) 업그레이드는 다음을 포함하여 다양한 향상된 기능을 제공합니다.

- [Express 프레임워크](http://expressjs.com/en/index.html)에 기반하여 새 노드 SDK는 간단하고 새 노드 버전을 유지할 수 있도록 설계됩니다. Express 미들웨어를 통해 응용 프로그램 동작을 사용자 지정할 수 있습니다.

- 모바일 서비스 SDK에 비해 성능이 크게 개선되었습니다.

- 모바일 백 엔드를 통해 웹 사이트를 호스팅할 수 있습니다. 마찬가지로 기존 expressv4 응용 프로그램에 Azure 모바일 SDK를 추가하기는 쉽습니다.

- 플랫폼 간 및 로컬 개발을 위해 작성된 모바일 앱 SDK는 Windows, Linux 및 OSX 플랫폼에서 개발되고 로컬로 실행될 수 있습니다. 배포하기 전에 [Mocha](https://mochajs.org/) 테스트를 실행하는 것 같은 일반적인 노드 개발 기술을 사용하는 것은 쉽습니다.

- [hiredis](https://www.npmjs.com/package/hiredis)와 같은 네이티브 모듈을 통해 Redis를 사용할 수 있습니다. 앱 서비스가 npm을 설치하기 때문에 배포 패키지에 이진 파일을 포함하지 않아도 됩니다.

## <a name="overview"></a>기본 업그레이드 개요

.NET 모바일 앱 SDK와 달리 모바일 서비스에서 모바일 앱으로 노드 백 엔드를 업그레이드하는 작업은 패키지 교환처럼 간단하지 않습니다. 이제 제어하는 Azure와 반대로 전체 응용 프로그램 스택을 소유합니다. 따라서 기본 Express 앱을 만들어 모바일 백 엔드를 호스팅해야 합니다. 테이블 및 API 컨트롤러의 경우 개념은 비슷하지만 테이블 개체를 내보내야 하고 함수 API는 다소 변경됩니다. 이 문서에서는 업그레이드의 기본 전략을 안내하지만 마이그레이션하기 전에 시작에 앞서 [노드 백 엔드 방법](app-service-mobile-node-backend-how-to-use-server-sdk.md)을 읽습니다.

>[AZURE.TIP] 업그레이드를 시작하기 전에 이 항목의 나머지 부분을 읽고 완전히 이해합니다. 아래 설명선에 표시된 사용하는 기능을 모두 기록해 두세요.

모바일 서비스 클라이언트 SDK는 새 모바일 앱 서버 SDK와 호환할 수 **없습니다**. 앱에 대한 서비스 연속성을 제공하기 위해 현재 게시된 클라이언트를 제공하는 사이트에 변경 내용을 게시하지 않아야 합니다. 대신 중복으로 제공한 새 모바일 앱을 만들어야 합니다. 이 응용 프로그램을 동일한 앱 서비스 계획에 두어 추가 비용이 발생하지 않도록 할 수 있습니다.

다음 두 가지 버전의 응용 프로그램이 있습니다. 하나는 동일하게 유지되고 야생에서 게시된 앱을 제공하며 다른 하나는 업그레이드하고 새 클라이언트 릴리스를 대상으로 할 수 있습니다. 진도에 맞게 코드를 이동하고 테스트할 수 있지만 수행한 버그 수정이 둘 모두에 적용되도록 해야 합니다. 야생에서 원하는 수의 클라이언트 앱이 최신 버전으로 업데이트되면 원하는 경우 원래 마이그레이션된 앱을 삭제할 수 있습니다. 동일한 앱 서비스 계획에서 모바일 앱으로 호스팅되는 경우 금전적인 추가 비용이 발생하지 않습니다.

업그레이드 프로세스에 대한 전체 개요는 다음과 같습니다.

1. 새 모바일 앱 만들기.
2. 프로젝트를 업데이트하여 새 서버 SDK 사용.
3. 새 모바일 앱에서 프로젝트 게시.
4. 새 모바일 앱을 사용하는 새 버전의 클라이언트 응용 프로그램 릴리스
5. (선택 사항) 원래 마이그레이션된 모바일 서비스 앱 삭제

삭제는 원래 마이그레이션된 모바일 서비스 앱에 트래픽이 표시되지 않을 때 발생할 수 있습니다.

## <a name="mobile-app-version"></a> 업그레이드 시작
업그레이드의 첫 번째 단계는 새 버전의 응용 프로그램을 호스트할 모바일 앱 리소스를 만드는 것입니다. 기존 모바일 서비스를 이미 마이그레이션한 경우 동일한 호스팅 계획에 이 버전을 만들려고 합니다. [Azure 포털]을 열고 마이그레이션된 응용 프로그램으로 이동합니다. 앱 서비스 계획에서 실행 중인지 확인합니다.

### 두 번째 응용 프로그램 인스턴스 만들기
다음으로 두 번째 응용 프로그램 인스턴스을 만듭니다. 앱 서비스 계획 또는 "호스팅 계획"을 선택하라는 메시지가 나타나면 마이그레이션된 응용 프로그램의 계획을 선택합니다.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

모바일 서비스와 동일한 데이터베이스 및 알림 허브를 사용하려는 경우가 많습니다. [Azure 포털]을 열고 원래 응용 프로그램 탐색하여 이러한 값을 복사한 다음 **설정** > **응용 프로그램 설정**을 클릭할 수 있습니다. **연결 문자열**에서 `MS_NotificationHubConnectionString` 및 `MS_TableConnectionString`을 복사합니다. 새 업그레이드 사이트로 이동하고 붙여 넣어 기존 값을 덮어씁니다. 앱에 필요한 다른 응용 프로그램 설정에 이 프로세스를 반복합니다. 마이그레이션된 서비스를 사용하지 않는 경우 [Azure 포털]에 있는 모바일 서비스 섹션의 **구성** 탭에서 연결 문자열 및 앱 설정을 읽을 수 있습니다.

### 노드를 통해 기본 모바일 앱 백 엔드 만들기

모든 Azure 앱 서비스 모바일 앱 Node.js 백 엔드는 ExpressJS 응용 프로그램으로 시작합니다. 다음과 같이 기본 [Express](http://expressjs.com/en/index.html) 응용 프로그램을 만들 수 있습니다.

1. 명령 또는 PowerShell 창에서 프로젝트에 새 디렉터리를 만듭니다.

        mkdir basicapp

2. npm init를 실행하여 패키지 구조를 초기화합니다.

        cd basicapp
        npm init

    npm init 명령은 프로젝트를 초기화하는 질문 집합을 요청합니다. 아래의 예제 출력을 참조하세요.

    ![Npm init 출력][0]

3. npm 리포지토리에서 Express 및 azure-mobile-apps 라이브러리를 설치합니다.

        npm install --save express azure-mobile-apps

4. app.js 파일을 만들어서 기본 모바일 서버를 구현합니다.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           var port = process.env.PORT || 3000;
           app.listen(port, function () {
               console.log('Now listening on ', port)
           });
        });

더 많은 샘플의 경우 [GitHub 리포지토리](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples)를 참조합니다.

## 서버 프로젝트 업데이트

모바일 앱은 모바일 서비스 런타임과 동일한 기능을 제공하는 새 [모바일 앱 서버 SDK]를 제공하지만 사용자는 전체 런타임을 소유합니다. 모바일 앱은 사용자에게 노드 버전이나 코드 업데이트를 강제하지 않습니다. 위의 단계를 따랐다면 사용 가능한 노드 모바일 런타임의 기본 버전이 있습니다. 이제 모바일 서비스에서 모바일 앱에 테이블 및 API 논리를 이동하기 시작하여 서버 구성, 푸시 사용, 인증 구성을 사용자 지정할 수 있습니다.

### 기본 구성

서버에는 많은 구성 설정이 있지만 다양한 기본 값을 가지고 쉽게 시작하도록 합니다. 대부분의 설정은 [Azure 포털]에서 **데이터**, **인증/권한 부여**, 및 **푸시** 설정 메뉴를 통해 설정됩니다. 로컬 개발의 경우 데이터, 인증 및 푸시를 사용하려면 로컬 개발 환경을 구성해야 할 수 있습니다.

모바일 앱 백 엔드에서 앱 설정을 통해 설정할 수 있는 환경 변수를 통해 서버 구성을 구성할 수 있습니다.

[구성 개체](http://azure.github.io/azure-mobile-apps-node/global.html#configuration)를 프로젝트의 루트에 있는 이니셜라이저에 전달하거나 [azureMobile.js라는 파일을 만들어](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-config-localdev) 모바일 앱 SDK를 추가로 사용자 지정할 수 있습니다.

### 데이터 및 테이블을 통한 작업

SDK는 메모리 내 데이터 공급자와 함께 빠르고 쉬운 시작 환경을 제공합니다. 메모리 내 공급자가 다시 시작 시 모든 데이터를 손실하고 여러 인스턴스 간에 일관된 상태를 유지하지 않기 때문에 초기에 SQL DB를 사용하여 전환해야 합니다.

모바일 서비스에서 모바일 앱에 비즈니스 논리를 이동하기 시작하려면 먼저 `./tables` 디렉터리에서 테이블의 이름('.js' 추가됨)을 사용하여 파일을 만들어야 합니다. [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js)에서 모바일 앱 테이블의 전체 예제를 볼 수 있습니다. 다음은 가장 간단한 버전입니다.

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

각 `<tablename>.<operation>.js`에 대한 논리의 일부를 이식하기 시작하려면 테이블에 함수가 필요합니다. 예제에 읽기 함수를 추가해 보겠습니다.

userid에 기반하는 항목을 필터링하는 TodoItem 테이블 및 읽기 작업이 있는 모바일 서비스에서는 다음과 같습니다.

    function(query, user, request) {
        query.where({ userId: user.userId});
        request.execute();
    }

Azure 모바일 앱 테이블 코드에 추가하는 함수는 다음과 같습니다.

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

쿼리, 사용자 및 요청을 컨텍스트로 결합합니다. 다음 필드는 컨텍스트 개체 내에서 사용할 수 있습니다.

| 필드 | 형식 | 설명 |
| :------ | :--------------------- | :---------- |
| 쿼리 | queryjs/쿼리 | 구문 분석된 OData 쿼리 |
| id | 문자열 또는 숫자 | 요청과 연결된 ID |
| 항목 | object | 삽입 또는 삭제되는 항목 |
| req | express.Request | 현재 express 요청 개체 |
| res | express.Response | 현재 express 응답 개체 |
| 데이터 | 데이터 | 구성된 데이터 공급자 |
| 테이블 | 함수 | 문자열 테이블 이름을 허용하고 테이블 액세스 개체를 반환하는 함수 |
| 사용자 | 인증/사용자 | 인증된 사용자 개체 |
| 결과 | object | 실행 작업의 결과 |
| 푸시 | NotificationHubService | 구성된 경우 알림 허브 서비스 |

자세한 내용은 [현재 API 설명서](http://azure.github.io/azure-mobile-apps-node)를 참조합니다.

### CORS

CORS는 SDK에서 [CORS 구성 설정](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration)을 통해 사용할 수 있습니다.

우려되는 부분은 CORS를 사용하는 경우 클라이언트 SDK가 제대로 작동하려면 `eTag` 및 `Location` 헤더를 허용해야 한다는 점입니다.

### 푸시 알림

Azure 알림 허브 SDK에는 모바일 서비스 이후 몇 가지 중요한 업데이트가 있었으므로 일부 알림 허브 함수 시그니처가 다를 수 있습니다. 그렇지 않은 경우 기능은 모바일 서비스의 경우와 비슷합니다. 알림 허브에 대한 앱 설정이 있는 경우 Azure 모바일 SDK는 알림 허브 인스턴스를 프로비전하고 `context.push`에서 노출합니다. 샘플은 아래에 표시된 관련 섹션을 통해 [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js)에서 찾을 수 있습니다.

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/ko-KR/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### 예약된 작업
예약된 작업은 모바일 앱에 빌드되지 않으므로 모바일 서비스 백 엔드에 있는 기존 작업을 모두 개별적으로 업그레이드해야 합니다. 한 가지 옵션은 모바일 앱 코드 사이트에 예약된 [웹 작업]을 만드는 것입니다. 작업 코드를 포함하는 API를 설정하고 예상 일정에 따라 해당 끝점에 도달하도록 [Azure 스케줄러]를 구성할 수도 있습니다.

## <a name="authentication"></a>인증 고려 사항

이제 모바일 서비스의 인증 구성 요소는 앱 서비스 인증/권한 부여 기능으로 옮겨졌습니다. [모바일 앱에 인증 추가](app-service-mobile-ios-get-started-users.md) 항목을 읽어서 사이트에 이 옵션을 사용하는 데 대해 알아볼 수 있습니다.

AAD, Facebook, Google 등의 일부 공급자의 경우 복사 응용 프로그램에서 기존 등록을 활용할 수 있어야 합니다. 단순히 ID 공급자의 포털로 이동하고 새 리디렉션 URL을 등록에 추가해야 합니다. 그런 다음 클라이언트 ID 및 암호를 통해 앱 서비스 인증/권한 부여를 구성합니다.

### 컨트롤러 작업 권한 부여 및 사용자 ID

테이블에 대한 액세스를 제한하려면 `table.access = 'authenticated';`을 사용하여 테이블 수준에 설정합니다. [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js)에서 전체 예제를 볼 수 있습니다.

[여기](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity)에 설명된 `user.getIdentity` 방법을 통해 사용자 ID 정보에 대한 액세스를 가져올 수 있습니다.

## <a name="updating-clients"></a>클라이언트 업데이트
작동하는 모바일 앱 백 엔드가 있으면 그것을 사용하는 클라이언트 응용 프로그램의 새 버전에서 작동할 수 있습니다. 또한 모바일 앱은 클라이언트 SDK의 새 버전을 포함하고 위의 서버 업그레이드와 유사합니다. 따라서 모바일 앱 버전을 설치하기 전에 모바일 서비스 SDK에 대한 모든 참조를 제거해야 합니다.

버전 간의 주요 변경 사항 중 하나는 생성자가 응용 프로그램 키를 더 이상 필요로 하지 않는다는 점입니다. 이제 모바일 앱의 URL에 간단히 전달할 수 있습니다. 예를 들어 .NET 클라이언트에서 `MobileServiceClient` 생성자는 다음과 같습니다.

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

아래 링크를 통해 새 SDK를 설치하고 새 구조를 사용하는 데 대한 내용을 읽을 수 있습니다.

- [iOS 버전 3.0.0 이상](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET(Windows/Xamarin) 버전 2.0.0 이상](app-service-mobile-dotnet-how-to-use-client-library.md)

응용 프로그램이 푸시 알림을 사용하면 일부 변경 사항이 있는 경우와 같이 각 플랫폼에 대한 특정 등록 지침을 기록합니다.

새 클라이언트 버전을 준비할 때 업그레이드된 서버 프로젝트에 사용해보세요. 작동하는지 유효성을 검사한 후에 고객에게 응용 프로그램의 새 버전을 릴리스할 수 있습니다. 결국 고객이 이러한 업데이트를 받고 나면 모바일 서비스 버전의 앱을 삭제할 수 있습니다. 이 시점에서 최신 모바일 앱 서버 SDK를 사용하여 앱 서비스 모바일 앱의 업그레이드를 완전히 완료했습니다.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[모바일 앱 정의]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ko-KR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[모바일 앱 서버 SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure 스케줄러]: /ko-KR/documentation/services/scheduler/
[웹 작업]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[앱 서비스 가격 책정]: https://azure.microsoft.com/ko-KR/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[Azure 포털]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/ko-KR/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/ko-KR/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0211_2016-->