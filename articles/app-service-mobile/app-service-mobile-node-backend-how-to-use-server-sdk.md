<properties
	pageTitle="모바일 앱용 노드 백 엔드 서버 SDK를 사용하는 방법 | Azure 앱 서비스"
	description="Azure 앱 서비스 모바일 앱용 노드 백 엔드 서버 SDK를 사용하는 방법에 대해 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianha"
	manager=""
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="adrianha"/>

# Azure 모바일 앱 노드 SDK를 사용하는 방법

이 문서에서는 Azure 앱 서비스 모바일 앱에서 노드 백 엔드로 작업하는 방법을 보여주는 자세한 정보와 예를 제공합니다.

	> [AZURE.NOTE] This SDK is in PREVIEW.  As a result, we do not recommend that you use this SDK in production.  The examples
	in this document use v2.0.0-beta1 of azure-mobile-apps.

## <a name="Introduction"></a>소개

Azure 앱 서비스 모바일 앱은 모바일에 최적화된 데이터 액세스 Web API를 웹 응용 프로그램에 추가하는 기능을 제공합니다. Azure 앱 서비스 모바일 앱 SDK는 ASP.NET 및 NodeJS 웹 응용 프로그램에 대해 제공되고 다음 작업을 제공합니다.

- 데이터 액세스를 위한 테이블 작업(읽기, 삽입, 업데이트, 삭제)
- API 작업 사용자 지정

두 작업은 모두 Facebook, Twitter, Google Microsoft 등과 같은 소셜 ID 공급자 뿐만 아니라 엔터프라이즈 ID에 대한 Azure Active Directory를 포함하는 Azure 앱 서비스에서 허용된 모든 ID 공급자에 걸쳐 인증을 제공합니다.

[GitHub의 샘플 디렉터리]에서 각 사용 사례에 대한 샘플을 찾을 수 있습니다.

### <a name="howto-cmdline-basicapp"></a>명령줄을 사용하는 기본 노드 백 엔드 만들기

모든 Azure 앱 서비스 모바일 앱 노드 백 엔드는 ExpressJS 응용 프로그램으로 시작합니다. ExpressJS는 노드에 사용할 수 있는 가장 인기 있는 웹 서비스 프레임워크입니다. 다음과 같이 기본 ExpressJS 노드 응용 프로그램을 만들 수 있습니다.

1. 명령 또는 PowerShell 창에서 프로젝트에 새 디렉터리를 만듭니다.

	```
	mkdir basicapp
	```

2. npm init를 실행하여 패키지 구조를 초기화합니다.

	```
	cd basicapp
	npm init
	```

	npm init 명령은 프로젝트를 초기화하는 질문 집합을 요청합니다. 아래의 예제 출력을 참조하세요.

	![Npm init 출력][0]

3. npm 리포지토리에서 Express 및 azure-mobile-apps 라이브러리를 설치합니다.

	```
	npm install --save express azure-mobile-apps
	```

4. app.js 파일을 만들어서 기본 모바일 서버를 구현합니다.

	```
	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Define a TodoItem table
	mobile.tables.add('TodoItem');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);
	```

이 응용 프로그램은 동적 스키마를 사용하여 기본 SQL 데이터 저장소에 대한 인증되지 않은 액세스를 제공하는 단일 끝점인 /tables/TodoItem으로 간단하고 모바일에 최적화된 WebAPI를 만듭니다. 다음의 클라이언트 라이브러리 빠른 시작에 적합합니다.

- [iOS 클라이언트 빠른 시작]
- [Xamarin.iOS 클라이언트 빠른 시작]
- [Xamarin.Android 클라이언트 빠른 시작]
- [Xamarin.Forms 클라이언트 빠른 시작]
- [Windows Phone 클라이언트 빠른 시작]
- [HTML/JavaScript 클라이언트 빠른 시작]

[GitHub의 기본 앱 샘플]에서 이 기본 응용 프로그램에 대한 코드를 찾을 수 있습니다.

### <a name="howto-vs2015-basicapp"></a>Visual Studio 2015를 사용하여 노드 백 엔드 만들기

Visual Studio 2015는 IDE 내에서 노드 응용 프로그램 개발하도록 확장이 필요합니다. 시작하려면 [Visual Studio용 Node.js Tools 1.1]를 다운로드 및 설치합니다. Visual Studio용 Node.js Tools가 설치되면 Express 4.x 응용 프로그램을 만듭니다.

1. **새 프로젝트** 대화를 엽니다.(**파일** > **새로 만들기** > **프로젝트...**에서)

2. **템플릿** > **JavaScript** > **Node.js**를 확장합니다.

3. **기본 Azure Node.js Express 4 응용 프로그램**을 선택합니다.

4. 프로젝트 이름을 입력합니다. *확인*을 클릭합니다.

	![Visual Studio 2015 새 프로젝트][1]

5. 마우스 오른쪽 단추로 **npm** 노드를 클릭하고 **새 npm 패키지 설치...**를 선택합니다.

6. 첫 번째 노드 응용 프로그램을 만드는 데 npm 카탈로그를 새로 고쳐야 합니다 - **새로 고침**을 클릭합니다.

7. 검색 상자에 _azure-mobile-apps_을 입력합니다. **azure-mobile-apps 2.0.0** 패키지를 클릭한 다음 **패키지 설치**를 클릭합니다.

	![새 npm 패키지 설치][2]

8. **닫기**를 클릭합니다.

9. _app.js_ 파일을 열고 Azure 모바일 앱 SDK에 대한 지원을 추가합니다.

  a. 6줄에 다음 코드를 추가합니다.

    ```
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

  b. 약 27줄에 다음 코드를 추가합니다.

	```
	app.use('/users', users);

	// Azure Mobile Apps Initialization
	var mobile = azureMobileApps();
	mobile.tables.add('TodoItem');
	app.use('mobile');
	```

  c. 파일을 저장합니다.

10. 응용 프로그램을 로컬로 실행하거나(API가 http://localhost:3000에서 제공됨) Azure에 게시합니다.

### <a name="howto-publish-to-azure"></a>Azure에 노드 백 엔드 게시

Microsoft Azure는 Azure 앱 서비스 모바일 앱 노드 백 엔드를 Azure 서비스에 게시하기 위한 여러 메커니즘을 제공합니다. 여기에는 원본 제어에 따라 Visual Studio, 명령줄 도구 및 연속 배포 옵션에 통합된 배포 도구의 활용을 포함합니다. 이 항목에 대한 자세한 내용은 [Azure 앱 서비스 배포 가이드]를 참조합니다.

Azure 앱 서비스에는 배포하기 전에 검토해야 하는 노드 응용 프로그램에 대한 구체적인 조언이 있습니다.

- [노드 버전 지정]하는 방법
- [노드 모듈 사용]하는 방법

## <a name="TableOperations"></a>테이블 작업

azure-mobile-apps 노드 서버 SDK는 SQL Azure에 저장된 데이터 테이블을 WebAPI로 노출하는 메커니즘을 제공합니다. 다섯 가지 작업이 제공됩니다.

| 작업 | 설명 |
| --------- | ----------- |
| GET /tables/\_tablename\_ | 테이블의 레코드 모두 가져오기 |
| GET /tables/\_tablename\_/:id | 테이블의 특정 레코드 가져오기 |
| POST /tables/\_tablename\_ | 테이블에 새 레코드 만들기 |
| PUT /tables/\_tablename\_/:id | 테이블의 기존 레코드 업데이트 |
| DELETE /tables/\_tablename\_/:id | 테이블의 레코드 삭제 |

이 WebAPI은 [OData]을 지원하고 테이블 스키마를 확장하여 [오프라인 데이터 동기화]를 지원합니다.

### <a name="howto-dynamicschema"></a>동적 스키마를 사용하여 테이블 정의

테이블을 사용하기 전에 정의되어야 합니다. 테이블은 정적 스키마(개발자가 스키마 내에서 열을 정의하는 위치) 또는 동적으로(SDK가 들어오는 요청에 따라 스키마를 제어하는 위치) 정의될 수 있습니다. 또한 개발자는 정의에 Javascript 코드를 추가하여 WebAPI의 특정 측면을 제어할 수 있습니다.

모범 사례로 테이블 디렉터리의 Javascript 파일에 각 테이블을 정의한 다음 테이블을 가져오는 tables.import() 메서드를 사용해야 합니다. basic-app을 확장하면 app.js 파일은 조정됩니다.

```
var express = require('express'),
	azureMobileApps = require('azure-mobile-apps');

var app = express(),
	mobile = azureMobileApps();

// Define the database schema that is exposed
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined
mobile.tables.initialize().then(function () {
	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);
});
```

./tables/TodoItem.js에서 테이블을 정의합니다.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here

module.exports = table;
```

테이블은 기본적으로 동적 스키마를 사용합니다. 동적 스키마를 전역적으로 해제하려면 앱 설정 **MS\_DynamicSchema**를 Azure 포털 내에서 false로 설정합니다.

[GitHub의 할 일 샘플]에서 전체 예제를 찾을 수 있습니다.

### <a name="howto-staticschema"></a>정적 스키마를 사용하여 테이블 정의

열을 명시적으로 정의하여 WebAPI를 통해 노출할 수 있습니다. azure-mobile-apps 노드 SDK는 오프라인 데이터 동기화에 필요한 모든 추가 열을 제공하는 목록에 자동으로 추가합니다. 예를 들어 빠른 시작 클라이언트 응용 프로그램은 텍스트(문자열) 및 완료(부울)이라는 두 열이 있는 테이블이 필요합니다. 다음과 같이 테이블 정의 JavaScript 파일에서 정의될 수 있습니다.(테이블 디렉터리에 위치)

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

module.exports = table;
```

또한 정적으로 테이블을 정의하는 경우 tables.initialize() 메서드를 호출하여 시작 시 데이터베이스 스키마를 만들어야 합니다. Tables.initialize() 메서드는 [Promise]를 반환합니다 - 데이터베이스가 초기화되기 전에 웹 서비스가 요청을 처리하지 않도록 하는 데 사용됩니다.

### <a name="howto-sqlexpress-setup"></a>로컬 컴퓨터에서 개발 데이터 저장소로 SQL Express 사용

Azure 모바일 앱 노드 SDK는 즉시 데이터를 처리하기 위해 세 가지 옵션을 제공합니다.

- **메모리** 드라이버를 사용하여 비 영구적 예제 저장소를 제공합니다
- **sql** 드라이버를 사용하여 개발을 위한 SQL Express 데이터 저장소를 제공합니다.
- **sql** 드라이버를 사용하여 프로덕션을 위한 SQL Azure 데이터 저장소를 제공합니다.

Azure 모바일 앱 노드 SDK는 [mssql 노드 패키지]를 사용하여 SQL Express 및 SQL Azure에 대한 연결을 설정하고 사용합니다. 이 패키지는 SQL Express 인스턴스에서 TCP 연결을 사용해야 합니다.

   >[AZURE.NOTE]메모리 드라이버는 테스트하기 위한 완전한 기능 집합을 제공하지 않습니다. 백 엔드 로컬로 테스트하려는 경우 SQL Express 데이터 저장소 및 SQL 드라이버를 사용하는 것이 좋습니다.

1. [Microsoft SQL Server 2014 Express]를 다운로드하고 설치합니다. Tools Edition을 사용하여 SQL Server 2014 Express를 설치하도록 합니다. 64비트를 지원하도록 명시적으로 요구하지 않는 한 32비트 버전이 실행할 때 메모리를 적게 사용합니다.

2. SQL Server 2014 구성 관리자를 실행합니다.

  a. 왼쪽 위 트리 메뉴에서 **SQL Server 네트워크 구성** 노드를 확장합니다.b. **SQLEXPRESS에 대한 프로토콜**을 클릭합니다. c. 마우스 오른쪽 단추로 **TCP/IP**를 클릭하고 **사용**을 선택합니다. 팝업 대화 상자에서 **확인**을 클릭합니다.d. 마우스 오른쪽 단추로 **TCP/IP**를 클릭하고 **속성**을 선택합니다.e. **IP 주소** 탭을 클릭합니다.f. **IPAll** 노드를 찾습니다. **TCP 포트** 필드에 **1433**을 입력합니다.

  ![TCP/IP에 대한 SQL Express 구성][3]

  g. **확인**을 클릭합니다. 팝업 대화 상자에서 **확인**을 클릭합니다.h. 왼쪽 트리 메뉴에서 **SQL Server 서비스**를 클릭합니다. i. 마우스 오른쪽 단추로 **SQL Server(SQLEXPRESS)**를 클릭하고 **다시 시작**을 선택합니다. j. SQL Server 2014 구성 관리자를 닫습니다.

3. SQL Server 2014 Management Studio 실행을 만들고 로컬 SQL Express 인스턴스에 연결합니다.

  a. 개체 탐색기에서 마우스 오른쪽 단추로 인스턴스를 클릭하고 **속성**을 선택합니다.b. **보안** 페이지를 선택합니다. c. **SQL Server 및 Windows 인증 모드**를 선택하도록 합니다.d. **확인**을 클릭합니다.

  ![SQL Express 인증 구성][4]

  e. 개체 탐색기에서 **보안** > **로그인**을 확장합니다. f. 마우스 오른쪽 단추로 **로그인**을 클릭하고 **새 로그인...**을 선택합니다. g. 로그인 이름을 입력합니다. **SQL Server 인증**을 선택합니다. 암호를 입력한 다음 동일한 암호를 **암호 확인**에 입력합니다. 암호는 Windows 복잡성 요구 사항을 충족해야 합니다.h. **확인**을 클릭합니다.

  ![SQL Express에 새 사용자 추가][5]

  i. 새 로그인을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. j. **서버 역할** 페이지를 선택합니다.k. **dbcreator** 서버 역할 옆에 확인란을 체크합니다.l. **확인**을 클릭합니다.m. SQL Server 2015 Management Studio를 닫습니다.

사용자 이름 및 선택한 암호를 기록하도록 합니다. 특정 데이터베이스 요구 사항에 따라 추가 서버 역할 또는 사용 권한을 할당해야 합니다.

노드 응용 프로그램은 **SQLCONNSTR\_MS\_TableConnectionString** 환경 변수를 읽어서 이 데이터베이스에 대한 연결 문자열을 읽습니다. 환경 내에서 이를 설정할 수 있습니다. 예를 들어 이 환경 변수를 설정하려면 PowerShell을 사용할 수 있습니다.

```
$env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"
```

TCP/IP 연결을 통해 데이터베이스에 액세스하고 연결에 대한 사용자 이름 및 암호를 제공해야 합니다.

### <a name="howto-config-localdev"></a>로컬 개발에 대한 프로젝트 구성

Azure 모바일 앱은 로컬 파일 시스템에서 _azureMobile.js_라는 JavaScript 파일을 읽습니다. 이 파일을 사용하여 프로덕션에서 Azure 모바일 앱 SDK를 구성하지 않아야 합니다 - 대신 [Azure 포털] 내에서 앱 설정을 사용합니다. _azureMobile.js_ 파일은 구성 개체를 내보내야 합니다. 가장 일반적인 설정은 다음과 같습니다.

- 데이터베이스 설정
- 진단 로깅 설정
- 대체 CORS 설정

위에 지정된 데이터베이스 설정을 구현하는 예제 _azureMobile.js_ 파일은 다음과 같습니다.

```
module.exports = {
	cors: {
		origins: [ 'localhost' ]
	},
	data: {
		provider: 'sql',
		server: '127.0.0.1',
		database: 'mytestdatabase',
		user: 'azuremobile',
		password: 'T3stPa55word'
	},
	logging: {
		level: 'verbose'
	}
};
```

_.gitignore_ 파일에 _azureMobile.js_를 추가하여(또는 기타 원본 코드 제어 무시 파일) 암호가 클라우드에 저장하지 않도록 합니다. [Azure 포털] 내의 앱 설정에서 프로덕션 설정을 항상 구성합니다.

### <a name="howto-use-sqlazure"></a>SQL Azure를 프로덕션 데이터 저장소로 사용

<!-- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

SQL Azure를 데이터 저장소로 사용하면 모든 Azure 앱 서비스 응용 프로그램 형식에 걸쳐 동일합니다. 아직 수행하지 않은 경우 다음 단계에 따라 새 모바일 앱 백 엔드를 만드세요.

1. [Azure 포털]에 로그인합니다.

2. 창의 왼쪽 위에서 **+새로 만들기** 단추 > **웹 + 모바일** > **모바일 앱**을 클릭한 다음 모바일 앱 백 엔드에 대한 이름을 입력합니다.

3. **리소스 그룹** 상자에 앱과 동일한 이름을 입력합니다.

4. 기본 앱 서비스 계획이 선택됩니다. 앱 서비스 계획을 변경하려는 경우 앱 서비스 계획 > **+ 새로 만들기**를 클릭하여 변경합니다. 새 앱 서비스 계획의 이름을 입력하고 적절한 위치를 선택합니다. 가격 책정 계층을 클릭하고 서비스에 대한 적절한 가격 책정 계층을 선택합니다. **무료** 및 **공유** 등의 더 많은 가격 책정 옵션을 보려면 **모두 보기**를 선택합니다. 가격 책정 계층을 선택한 다음 **선택** 단추를 클릭합니다. **앱 서비스 계획** 블레이드로 돌아가서 **확인**을 클릭합니다.

5. **만들기**를 클릭합니다. 그러면 나중에 서버 프로젝트를 배포할 모바일 앱 백 엔드가 만들어집니다. 모바일 앱 백 엔드를 프로비저닝하는 데 몇 분 정도 걸릴 수 있습니다. 모바일 앱 백 엔드가 프로비저닝되면 포털에서 모바일 앱 백 엔드에 대한 **설정** 블레이드가 열립니다.

모바일 앱 백 엔드를 만들면 모바일 앱 백 엔드에 기존 SQL Azure 데이터베이스를 연결하거나 새 SQL Azure 데이터베이스를 만들도록 선택할 수 있습니다. 이 방법에서 새 SQL 데이터베이스를 만듭니다.

    > [AZURE.NOTE] If you already have a database in the same location as the new mobile app backend, you can instead choose **Use an existing database** and then select that database. The use of a database in a different location is not recommended because of additional bandwidth costs and higher latencies.

6. 새 모바일 앱 백 엔드에서 **설정** > **모바일 앱** > **데이터** > **+추가**를 클릭합니다.

7. **데이터 연결 추가** 블레이드에서 **SQL 데이터베이스 - 필요한 설정 구성** > **새 데이터베이스를 만들기**를 클릭합니다. **이름** 필드에 새 데이터베이스 이름을 입력합니다.

8. **서버**를 클릭합니다. **새 서버** 블레이드에서 **서버 이름** 필드에 고유한 서버 이름을 입력하고 적절한 **서버 관리자 로그인** 및 **암호**를 입력합니다. **Azure 서비스가 서버에 액세스하도록 허용**이 선택되었는지 확인합니다. **확인**을 클릭합니다.

	![SQL Azure 데이터베이스 만들기][6]

9. **새 데이터베이스** 블레이드에서 **확인**을 클릭합니다.

10. **데이터 연결 추가** 블레이드로 돌아가서 **연결 문자열**을 선택하고 데이터베이스를 만들 때 입력한 로그인 및 암호를 입력합니다. 기존 데이터베이스를 사용하는 경우 해당 데이터베이스에 대한 로그인 자격 증명을 입력합니다. 입력한 다음 **확인**을 클릭합니다.

11. **데이터 연결 추가** 블레이드로 다시 돌아가서 **확인**을 클릭하여 데이터베이스를 만듭니다.

<!-- END OF ALTERNATE INCLUDE -->

데이터베이스를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 영역을 사용하여 배포의 진행률을 모니터링합니다. 데이터베이스가 성공적으로 배포될 때까지 진행하지 마세요. 성공적으로 배포되면 모바일 백 엔드 앱 설정에서 SQL Azure 데이터베이스 인스턴스에 대한 연결 문자열을 만듭니다. **설정** > **응용 프로그램 설정** > **연결 문자열**에서 이 앱 설정을 볼 수 있습니다.

### <a name="howto-tables-auth"></a>테이블에 대한 액세스 인증 요구

테이블 끝점을 사용하여 앱 서비스 인증을 사용하려는 경우 [Azure 포털]에서 우선 앱 서비스 인증을 구성해야 합니다. Azure 앱 서비스에서 인증을 구성하는 데 대한 자세한 내용은 사용하려는 ID 공급자를 위한 구성 가이드를 검토합니다.

- [Azure Active Directory 인증을 구성하는 방법]
- [Facebook 인증을 구성하는 방법]
- [Google 인증을 구성하는 방법]
- [Microsoft 인증을 구성하는 방법]
- [Twitter 인증을 구성하는 방법]

각 테이블에는 테이블에 대한 액세스를 제어하는 데 사용할 수 있는 액세스 속성이 있습니다. 다음 샘플에서는 필요한 인증을 사용하여 정적으로 정의된 테이블을 보여줍니다.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

액세스 속성은 두 가지 값을 사용할 수 있습니다.

  - *인증됨*은 클라이언트 응용 프로그램이 요청을 사용하여 유효한 인증 토큰을 송신해야 함을 나타냅니다
  - *사용 안 함*은 이 테이블이 현재 사용되지 않음을 나타냅니다.

액세스 속성을 정의하지 않으면 인증되지 않은 액세스가 허용됩니다.

### <a name="howto-tables-disabled"></a>특정 테이블 작업에 대한 액세스 사용 안 함

테이블에 나타나는 것 외에도 액세스 속성은 개별 작업을 제어하는 데 사용될 수 있습니다. 네 가지 작업이 있습니다.

  - *읽기*는 테이블의 RESTful 가져오기 작업입니다.
  - *삽입*은 테이블의 RESTful 게시 작업입니다.
  - *업데이트*는 테이블의 RESTful 패치 작업입니다.
  - *삭제*는 테이블의 RESTful 삭제 작업입니다.

예를 들어 읽기 전용 인증되지 않은 테이블을 제공하려 할 수도 있습니다. 다음 테이블 정의에서 제공될 수 있습니다.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-Only table - only allow READ operations
table.read.access = undefined;
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>테이블 작업에 사용되는 쿼리 조정

테이블 작업에 대한 일반적인 요구 사항은 데이터의 제한된 보기를 제공하는 것입니다. 예를 들어 사용자가 고유한 레코드를 읽거나 업데이트만할 수 있는 등 인증된 사용자 ID로 태그가 지정된 테이블을 제공할 수 있습니다. 다음 테이블 정의는 다음의 기능을 제공합니다.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table
table.columns = {
	"userId": "string",
	"text": "string",
	"complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved
table.read(function (context) {
	context.query.where({ userId: context.user.id });
	return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user
table.insert(function (context) {
	context.item.userId = context.user.id;
	return context.execute();
}

module.exports = table;
```

일반적으로 쿼리를 실행하는 작업에는 where 절을 사용하여 조정할 수 있는 쿼리 속성이 있습니다. 쿼리 속성은 데이터 백 엔드가 처리할 수 있는 무언가에 OData 쿼리를 변환하는 데 사용되는 [QueryJS] 개체입니다. 간단한 같은 경우(위에서 처럼) 맵을 사용할 수 있습니다. 비교적 쉽게 특정 SQL 절을 추가합니다.

```
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>테이블에 일시 삭제 구성

일시 삭제는 레코드를 실제로 삭제하지 않습니다. 대신 삭제된 열을 true로 설정하여 데이터베이스 내에서 삭제된 것으로 표시합니다. 모바일 클라이언트 SDK가 IncludeDeleted()를 사용하지 않는 경우 Azure 모바일 앱 SDK는 일시 삭제된 레코드를 결과에서 자동으로 삭제합니다. 일시 삭제에 대한 테이블을 구성하려면 테이블 정의 파일에서 softDelete 속성을 설정합니다. 예는 다음과 같을 수 있습니다.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

// Turn on Soft Delete
table.softDelete = true;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

클라이언트 응용 프로그램에서 웹WebJob 또는 사용자 지정 메커니즘을 통해 레코드를 제거하기 위한 메커니즘을 설정해야 합니다.

### <a name="howto-tables-seeding"></a>데이터를 사용하여 데이터베이스 시드

새 응용 프로그램을 만들 때 데이터가 있는 테이블을 시드할 수 있습니다. 다음과 같이 테이블 정의 JavaScript 파일 내에서 수행될 수 있습니다.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};
table.seed = [
	{ text: 'Example 1', complete: false },
	{ text: 'Example 2', complete: true }
];

// Turn off dynamic schema
table.dynamicSchema = false;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

데이터의 시드는 Azure 모바일 앱 SDK에서 테이블을 만들 때에만 수행될 수 있음에 유의해야 합니다. 테이블이 데이터베이스 내에 이미 있으면 데이터는 테이블에 삽입되지 않습니다. 동적 스키마를 설정한 경우 스키마는 시드된 데이터에서 유추됩니다.

서비스가 실행되기 시작하면 initialize() 메서드를 명시적으로 호출하여 테이블을 만드는 것이 좋습니다.

## <a name="CustomAPI"></a>사용자 지정 API

/tables 끝점을 통한 데이터 액세스 API 외에도 Azure 모바일 앱은 사용자 지정 API 범위를 제공할 수 있습니다. 사용자 지정 API는 유사한 방식으로 테이블 정의에 정의되고 인증을 비롯한 동일한 시설에 액세스할 수 있습니다.

사용자 지정 API을 사용하여 앱 서비스 인증을 사용하려는 경우 [Azure 포털]에서 우선 앱 서비스 인증을 구성해야 합니다. Azure 앱 서비스에서 인증을 구성하는 데 대한 자세한 내용은 사용하려는 ID 공급자를 위한 구성 가이드를 검토합니다.

- [Azure Active Directory 인증을 구성하는 방법]
- [Facebook 인증을 구성하는 방법]
- [Google 인증을 구성하는 방법]
- [Microsoft 인증을 구성하는 방법]
- [Twitter 인증을 구성하는 방법]

### <a name="howto-customapi-basic"></a>단순 사용자 지정 API 정의

사용자 지정 API는 거의 동일한 방식으로 테이블 API로 정의됩니다.

1. **API** 디렉터리 만들기
2. **API** 디렉터리에서 API 정의 JavaScript 파일을 만듭니다.
3. 가져오기 메서드를 사용하여 **API** 디렉터리를 가져옵니다.

다음은 이전에 사용된 기본 앱 샘플에 따른 프로토타입 API 정의입니다.

```
var express = require('express'),
	azureMobileApps = require('azure-mobile-apps');

var app = express(),
	mobile = azureMobileApps();

// Import the Custom API
mobile.api.import('./api');

// Add the mobile API so it is accessible as a Web API
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

_Date.now()_ 메서드를 사용하여 서버 날짜를 반환하는 간단한 API를 살펴보겠습니다. 다음은 api/date.js 파일입니다.

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};

module.exports = api;
```

각 매개 변수는 가져오기, 게시, 패치 또는 삭제와 같은 RESTful 표준 동사의 하나입니다. 메서드는 필요한 출력을 전송하는 표준 [ExpressJS 미들웨어] 함수입니다.

### <a name="howto-customapi-auth"></a>사용자 지정 API에 대한 액세스 인증 요구

Azure 모바일 앱 SDK는 테이블 끝점 및 사용자 지정 API에 대해 동일한 방식으로 인증을 구현합니다. 이전 섹션에서 개발된 API에 인증을 추가하려면 **액세스** 속성을 추가합니다.

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

또한 특정 작업에 인증을 지정할 수 있습니다.

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

테이블 끝점에 사용되는 동일한 토큰은 인증을 요구하는 사용자 지정 API에 사용되어야 합니다.

## <a name="Debugging"></a>디버그 및 문제 해결

Azure 앱 서비스는 노드 응용 프로그램에 대한 여러 디버깅 및 문제 해결 기술을 제공합니다. 이러한 기술을 모두 사용할 수 있습니다.

- [Azure 앱 서비스 모니터링]
- [Azure 앱 서비스에 진단 로그 사용]
- [Visual Studio에서 Azure 앱 서비스 문제 해결]

### <a name="howto-diagnostic-logs"></a>Azure 모바일 앱 진단 로그에 쓰기

노드 응용 프로그램은 넓은 범위의 진단 로그 도구에 액세스합니다. 내부적으로 Azure 모바일 앱 노드 SDK는 진단 로깅에 [윈스턴]을 사용합니다. [Azure 포털]에서 디버그 모드를 사용하거나 **MS\_DebugMode** 앱 설정을 true로 설정하여 자동으로 활성화합니다. 생성된 로그는 [Azure 포털]의 진단 로그에 표시됩니다.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[iOS 클라이언트 빠른 시작]: app-service-mobile-ios-get-started.md
[Xamarin.iOS 클라이언트 빠른 시작]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android 클라이언트 빠른 시작]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms 클라이언트 빠른 시작]: app-service-mobile-xamarin-forms-get-started.md
[Windows Phone 클라이언트 빠른 시작]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/JavaScript 클라이언트 빠른 시작]: app-service-html-get-started.md
[오프라인 데이터 동기화]: app-service-mobile-offline-data-sync.md
[Azure Active Directory 인증을 구성하는 방법]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook 인증을 구성하는 방법]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google 인증을 구성하는 방법]: app-service-mobile-how-to-configure-google-authentication.md
[Microsoft 인증을 구성하는 방법]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter 인증을 구성하는 방법]: app-service-mobile-how-to-configure-twitter-authentication.md
[Azure 앱 서비스 배포 가이드]: ../app-service-web/web-site-deploy.md
[Azure 앱 서비스 모니터링]: ../app-service-web/web-sites-monitor.md
[Azure 앱 서비스에 진단 로그 사용]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Visual Studio에서 Azure 앱 서비스 문제 해결]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[노드 버전 지정]: ../nodejs-specify-node-version-azure-apps.md
[노드 모듈 사용]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/

[Azure 포털]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/ko-KR/docs/Web/JavaScript/Reference/Global_Objects/Promise
[GitHub의 기본 앱 샘플]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[GitHub의 할 일 샘플]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[GitHub의 샘플 디렉터리]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Visual Studio용 Node.js Tools 1.1]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql 노드 패키지]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/ko-KR/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS 미들웨어]: http://expressjs.com/guide/using-middleware.html
[윈스턴]: https://github.com/winstonjs/winston

<!---HONumber=Nov15_HO4-->