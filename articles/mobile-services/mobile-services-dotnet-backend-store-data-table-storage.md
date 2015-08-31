<properties 
	pageTitle="SQL 데이터베이스 대신 테이블 저장소를 사용하는 서비스 빌드 | Microsoft Azure" 
	description=".NET 백 엔드 모바일 서비스에서 Azure 테이블 저장소를 사용하는 방법을 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="glenga"/>

# SQL 데이터베이스 대신 테이블 저장소를 사용하는 .NET 백 엔드 모바일 서비스 빌드

이 항목에서는 .NET 백 엔드 모바일 서비스에 대한 비관계형 데이터 저장소를 사용하는 방법을 보여 줍니다. 이 자습서에서는 기본 Azure SQL 데이터베이스 데이터 저장소 대신 Azure 테이블 저장소를 사용하도록 Azure 모바일 서비스 빠른 시작 프로젝트를 수정합니다.

이 자습서를 사용하려면 먼저 [모바일 서비스 시작] 또는 [기존 앱에 모바일 서비스 추가] 자습서를 완료해야 합니다. Azure 저장소 계정도 필요합니다.

##.NET 백 엔드 모바일 서비스에서 Azure 테이블 저장소 구성

먼저 Azure 저장소에 연결할 모바일 서비스 및 .NET 백 엔드 코드 프로젝트를 구성해야 합니다.

1. Visual Studio의 **솔루션 탐색기**에서 .NET 백 엔드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고, **스테이빌만**을 선택하고, **MobileServices**를 검색하고, **Microsoft Azure 모바일 서비스 .NET 백 엔드 Azure 저장소 확장** 패키지에서 **설치**를 클릭하고, 사용권 계약에 동의합니다.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Azure 저장소 서비스에 대한 지원이 .NET 백 엔드 모바일 서비스 프로젝트에 추가됩니다.

3. 저장소 계정을 만들지 않았다면 [저장소 계정을 만드는 방법](../storage-create-storage-account.md)을 참조하세요.

4. 관리 포털에서 **저장소**를 클릭하고 저장소 계정을 클릭한 후 **키 관리**를 클릭합니다.

5. **저장소 계정 이름**과 **액세스 키**를 적어 두세요.
 
6. 모바일 서비스에서 **구성** 탭을 클릭하고, **연결 문자열**까지 아래로 스크롤하고, `StorageConnectionString`의 **이름**과 저장소 계정 연결 문자열인 **값**을 사용하여 새 연결 문자열을 다음 형식으로 입력합니다.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. 위 문자열에서 `<ACCOUNT_NAME>` 및 `<ACCESS_KEY>` 값을 포털의 값으로 바꾸고 **저장**을 클릭합니다.

	저장소 계정 연결 문자열은 앱 설정에 암호화되어 저장됩니다. 런타임에 테이블 컨트롤러에서 이 문자열에 액세스할 수 있습니다.

8. Visual Studio의 솔루션 탐색기에서 모바일 서비스 프로젝트의 Web.config 파일을 열고 다음과 같은 새 연결 문자열을 추가합니다.

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. `<STORAGE_CONNECTION_STRING>` 자리 표시자를 6단계에서 설정한 연결 문자열로 바꿉니다.

	로컬 컴퓨터에서 실행하는 모바일 서비스는 이 연결 문자열을 사용하므로 해당 서비스를 게시하기 전에 코드를 테스트할 수 있습니다. 그러나 Azure에서 실행하는 모바일 서비스는 포털에 설정된 연결 문자열 값을 사용하고 프로젝트의 연결 문자열을 무시합니다.

## <a name="modify-service"></a>데이터 형식 및 테이블 컨트롤러 수정

TodoList 빠른 시작 프로젝트는 Entity Framework를 사용하여 SQL 데이터베이스 작업을 수행하도록 디자인되므로 테이블 저장소 작업을 수행하려면 프로젝트에서 몇 가지 항목을 업데이트해야 합니다.

1. 다음과 같이 **EntityData** 대신 **StorageData**에서 파생되도록 **TodoItem** 데이터를 수정합니다.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]**StorageData** 형식에는 *partitionId*,*rowValue* 형식의 문자열인 복합 키가 필요한 Id 속성이 있습니다.

2. **TodoItemController**에서 다음 using 문을 추가합니다.

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. **TodoItemController**의 **Initialize** 메서드를 다음으로 바꿉니다.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored 
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName, 
                tableName, Request, Services);          
        }

	저장소 계정 연결 문자열을 사용하여 요청된 컨트롤러에 대한 새 저장소 도메인 관리자가 생성됩니다.

3. 기존 **GetAllTodoItems** 메서드를 다음 코드로 바꿉니다.

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        } 

	SQL 데이터베이스와 달리 이 버전은 IQueryable<TEntity>를 반환하지 않으므로 결과가 쿼리에 바인딩될 수 있지만 추가로 쿼리로 작성되지 않습니다.

## 클라이언트 앱 업데이트

테이블 저장소를 사용하여 빠른 시작 앱이 .NET 백 엔드를 사용하도록 하려면 클라이언트 쪽에서 한 가지 항목을 변경해야 합니다. 이는 테이블 저장소 공급자에 복합 키가 필요하기 때문입니다.

1. 데이터 액세스 코드가 포함된 클라이언트 코드 파일을 열고 삽입 작업이 수행된 메서드를 찾습니다.

2. 문자열 형식 `<partitionID>,<rowValue>`에서 Id 필드를 명시적으로 설정하도록 추가되는 TodoItem 인스턴스를 업데이트합니다.

	이 예제에서는 파티션 부분이 수정되고 행 부분이 GUID에 기반을 둔 C# 앱에서 이 ID를 설정하는 방법을 보여 줍니다.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

이제 앱을 테스트할 준비가 되었습니다.

## <a name="test-application"></a>응용 프로그램 테스트

1. (선택 사항) 모바일 서비스 .NET 백 엔드 프로젝트를 다시 게시합니다. 
	
	.NET 백 엔드 프로젝트를 Azure에 게시하기 전에 로컬로 모바일 서비스를 테스트할 수도 있습니다. 로컬로 또는 Azure에서 테스트하는지 여부와 관계없이 모바일 서비스에서는 Azure 테이블 저장소를 사용합니다.

4. 모바일 서비스에 연결된 빠른 시작 클라이언트 앱을 실행합니다.

	빠른 시작 자습서를 사용하여 이전에 추가한 항목이 표시되지 않습니다. 이는 테이블 저장소가 현재 비어 있기 때문입니다.

5. 새 항목을 추가하여 데이터베이스 변경 내용을 생성합니다.
 
	데이터가 SQL 데이터베이스 대신 비관계형 저장소에 저장된다는 점을 제외하고 앱 및 모바일 서비스는 이전과 같이 동작해야 합니다.

##다음 단계

이제 간편하게 .NET 백 엔드와 함께 테이블 저장소를 사용하는 방법을 살펴보았으므로 몇 가지 다른 백 엔드 저장소 옵션을 살펴보겠습니다.

+ [모바일 서비스 .NET 백 엔드에서 MongoDB를 데이터 저장소로 사용](mobile-services-dotnet-backend-use-non-relational-data-store.md)</br>방금 완료한 자습서처럼 이 항목에서는 모바일 서비스에 대해 비관계형 데이터 저장소를 사용하는 방법을 보여 줍니다. 이 자습서에서는 데이터 저장소로 SQL 데이터베이스 대신 MongoDB를 사용하도록 모바일 서비스 빠른 시작 프로젝트를 수정합니다.
 
+ [하이브리드 연결을 사용하여 온-프레미스 SQL Server에 연결](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>하이브리드 연결을 통해 모바일 서비스를 온-프레미스 자산에 안전하게 연결할 수 있습니다. 이러한 방식으로 Azure를 사용하여 온-프레미스 데이터를 모바일 클라이언트에 액세스할 수 있게 됩니다. 지원되는 자산에는 Microsoft SQL Server, MySQL, HTTP 웹 API, 대부분의 사용자 지정 웹 서비스를 비롯하여 정적 TCP 포트에서 실행되는 모든 리소스가 포함됩니다.

+ [모바일 서비스를 사용하여 Azure 저장소에 이미지 업로드](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>이미지를 앱에서 Azure Blob 저장소에 업로드할 수 있도록 TodoList 샘플 프로젝트를 확장하는 방법을 보여 줍니다.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[모바일 서비스 시작]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[기존 앱에 모바일 서비스 추가]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Azure Management Portal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab
 

<!---HONumber=August15_HO8-->