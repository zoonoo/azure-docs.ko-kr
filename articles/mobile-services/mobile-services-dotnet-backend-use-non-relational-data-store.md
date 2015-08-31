<properties 
	pageTitle="비관계형 데이터 저장소를 사용하여 서비스 빌드 | Microsoft Azure" 
	description=".NET 기반 모바일 서비스에서 MongoDB 나 Azure 테이블 저장소와 같은 비관계형 데이터 저장소를 사용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="mahender"/>

# 저장소로 SQL 데이터베이스 대신 MongoDB를 사용하는 .NET 백 엔드 모바일 서비스 빌드

이 항목에서는 .NET 백 엔드 모바일 서비스에 대한 비관계형 데이터 저장소를 사용하는 방법을 보여 줍니다. 이 자습서에서는 기본 Azure SQL 데이터베이스 데이터 저장소 대신 MongoDB를 사용하도록 모바일 서비스 빠른 시작 프로젝트를 수정합니다.

이 자습서를 사용하려면 먼저 [모바일 서비스 시작] 또는 [기존 앱에 모바일 서비스 추가] 자습서를 완료해야 합니다. 또한 구독에 MongoLab 서비스를 추가해야 합니다.

## <a name="create-store"></a>MongoLab 비관계형 저장소 만들기

1. [Azure 관리 포털]에서 **다음**, **마켓플레이스**를 차례로 클릭합니다.

2. **MongoLab** 추가 기능을 클릭하고 마법사를 완료하여 MongoLab 계정을 등록합니다.

	MongoLab에 대한 자세한 내용은 [MongoLab 추가 기능 페이지]를 참조하세요.

2. 계정이 설정된 다음에는 **연결 정보**를 클릭하고 연결 문자열을 복사합니다.

3. 모바일 서비스에서 **구성** 탭을 클릭하고, **연결 문자열**까지 아래로 스크롤하고, `MongoConnectionString`의 **이름**과 MongoDB 연결인 **값**을 사용하여 새 연결 문자열을 입력하고, **저장**을 클릭합니다.

	![MongoDB 연결 문자열 추가](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	저장소 계정 연결 문자열은 앱 설정에 암호화되어 저장됩니다. 런타임에 테이블 컨트롤러에서 이 문자열에 액세스할 수 있습니다.

8. Visual Studio의 솔루션 탐색기에서 모바일 서비스 프로젝트의 Web.config 파일을 열고 다음과 같은 새 연결 문자열을 추가합니다.

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. `<MONGODB_CONNECTION_STRING>` 자리 표시자를 MongoDB 연결 문자열로 바꿉니다.

	로컬 컴퓨터에서 실행하는 모바일 서비스는 이 연결 문자열을 사용하므로 해당 서비스를 게시하기 전에 코드를 테스트할 수 있습니다. 그러나 Azure에서 실행하는 모바일 서비스는 포털에 설정된 연결 문자열 값을 사용하고 프로젝트의 연결 문자열을 무시합니다.

## <a name="modify-service"></a>데이터 형식 및 테이블 컨트롤러 수정

1. **WindowsAzure.MobileServices.Backend.Mongo** NuGet 패키지를 설치합니다.

2. **EntityData** 대신 **DocumentData**에서 파생되도록 **TodoItem**을 수정합니다.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. **TodoItemController**에서 **Initialize** 메서드를 다음으로 바꿉니다.

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. 위의 **Initialize** 메서드에 대한 코드에서 `<YOUR-DATABASE-NAME>`을 MongoLab 추가 기능을 프로비전할 때 선택한 이름으로 바꿉니다.

이제 앱을 테스트할 준비가 되었습니다.

## <a name="test-application"></a>응용 프로그램 테스트

1. (선택 사항) 모바일 서비스 .NET 백 엔드 프로젝트를 다시 게시합니다.

	.NET 백 엔드 프로젝트를 Azure에 게시하기 전에 로컬로 모바일 서비스를 테스트할 수도 있습니다. 로컬로 또는 Azure에서 테스트하는지 여부와 관계없이 모바일 서비스에서는 저장소로 MongoDB를 사용합니다.

4. 시작 페이지에서 이전처럼 **지금 시도** 단추를 사용하거나 모바일 앱에 연결된 클라이언트 앱을 사용하여 데이터베이스에서 항목을 쿼리합니다.
 
	퀵 스타트 자습서에서 SQL 데이터베이스에 이전에 저장된 모든 항목이 표시되지는 않습니다.

	>[AZURE.NOTE]API 도움말 페이지를 시작하기 위해 **지금 시도** 단추를 사용하는 경우 사용자의 응용 프로그램 키를 암호(빈 사용자 이름)로 제공해야 합니다.

3. 새 항목을 만듭니다.

	데이터가 SQL 데이터베이스 대신 비관계형 저장소에 저장된다는 점을 제외하고 앱 및 모바일 서비스는 이전과 같이 동작해야 합니다.

##다음 단계

이제 간편하게 .NET 백 엔드와 함께 테이블 저장소를 사용하는 방법을 살펴보았으므로 몇 가지 다른 백 엔드 저장소 옵션을 살펴보겠습니다.

+ [SQL 데이터베이스 대신 테이블 저장소를 사용하는 .NET 백 엔드 모바일 서비스 빌드](mobile-services-dotnet-backend-store-data-table-storage.md)</br>방금 완료한 자습서처럼 이 항목에서는 모바일 서비스에 대해 비관계형 데이터 저장소를 사용하는 방법을 보여 줍니다. 이 자습서에서는 데이터 저장소로 SQL 데이터베이스 대신 Azure 저장소를 사용하도록 모바일 서비스 빠른 시작 프로젝트를 수정합니다.
 
+ [하이브리드 연결을 사용하여 온-프레미스 SQL Server에 연결](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>하이브리드 연결을 통해 모바일 서비스를 온-프레미스 자산에 안전하게 연결할 수 있습니다. 이러한 방식으로 Azure를 사용하여 온-프레미스 데이터를 모바일 클라이언트에 액세스할 수 있게 됩니다. 지원되는 자산에는 Microsoft SQL Server, MySQL, HTTP 웹 API, 대부분의 사용자 지정 웹 서비스를 비롯하여 정적 TCP 포트에서 실행되는 모든 리소스가 포함됩니다.

+ [모바일 서비스를 사용하여 Azure 저장소에 이미지 업로드](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>이미지를 앱에서 Azure Blob 저장소에 업로드할 수 있도록 TodoList 샘플 프로젝트를 확장하는 방법을 보여 줍니다.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[모바일 서비스 시작]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[기존 앱에 모바일 서비스 추가]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab 추가 기능 페이지]: /gallery/store/mongolab/mongolab
 

<!---HONumber=August15_HO8-->