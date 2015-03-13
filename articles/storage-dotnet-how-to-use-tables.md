<properties 
	pageTitle=".NET에서 테이블 저장소를 사용하는 방법 | Microsoft Azure" 
	description="Microsoft Azure 테이블 저장소를 사용하여 테이블을 작성 및 삭제하고 테이블의 엔터티를 삽입 및 쿼리하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>


# .NET에서 테이블 저장소를 사용하는 방법


이 가이드에서는 Azure Blob 서비스를 사용하여 
일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C\# 코드로 작성되었으며 Azure Storage Client Library for .NET을 사용합니다. 여기서 다루는 시나리오에는 **테이블 만들기 및 삭제**와 **테이블 엔터티 작업**이 포함됩니다. 테이블에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

> [AZURE.NOTE] 이 가이드는 Azure .NET Storage Client Library 2.x 이상을 대상으로 합니다. 권장되는 버전은 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/)을 통해 또는 [Azure SDK for .NET](/ko-kr/downloads/)의 일부로 사용할 수 있는 Storage Client Library 4.x입니다. 저장소 클라이언트 라이브러리를 구하는 방법은 아래의 [방법: 프로그래밍 방식으로 테이블 저장소 액세스][]를 참조하세요.

## 목차

-   [테이블 서비스 정의][]
-   [개념][]
-   [Azure 저장소 계정 만들기][]
-   [저장소 연결 문자열 설정][]
-   [방법: 프로그래밍 방식으로 테이블 저장소 액세스][]
-   [방법: 테이블 만들기][]
-   [방법: 테이블에 엔터티 추가][]
-   [방법: 엔터티 일괄 삽입][]
-   [방법: 파티션의 모든 엔터티 검색][]
-   [방법: 파티션의 엔터티 범위 검색][]
-   [방법: 단일 엔터티 검색][]
-   [방법: 엔터티 바꾸기][]
-   [방법: 엔터티 삽입 또는 바꾸기][]
-   [방법: 엔터티 속성 하위 집합 쿼리][]
-   [방법: 엔터티 삭제][]
-   [방법: 테이블 삭제][]
-   [다음 단계][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <h2> <a name="configure-access"> </a>방법: 프로그래밍 방식으로 테이블 저장소 액세스</h2>

<h3>어셈블리 가져오기</h3>
NuGet을 사용하여  `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져올 수 있습니다. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.  온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지 및 종속성을 설치합니다.

`Microsoft.WindowsAzure.Storage.dll`은 <a href="http://azure.microsoft.com/develop/net/#">.NET 개발자 센터</a>에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다. 이 어셈블리는  `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 디렉터리에 설치됩니다.

<h3>네임스페이스 선언</h3>
프로그래밍 방식으로 Azure 저장소에 액세스하려는 C\# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

<h3>연결 문자열 검색</h3>
**CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Azure 프로젝트 템플릿을 사용하고 있거나 Microsoft.WindowsAzure.CloudConfigurationManager 네임스페이스에 대한 참조가 있는 경우 **CloudConfigurationManager** 형식을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는 중이며 위에 표시된 대로 연결 문자열이  `web.config` 또는  `app.config`에 있는 경우 **ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다.  System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib 종속성</h3>
Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을 통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다. ODataLib 라이브러리를 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 특정 ODataLib 패키지는 [OData], [Edm] 및 [Spatial]입니다.

<h2><a name="create-table"></a>방법: 테이블 만들기</h2>

**CloudTableClient** 개체를 통해 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드에서는 **CloudTableClient** 개체를 만들고 이 개체를 사용하여 새 테이블을 만듭니다. 이 가이드의 모든 코드에서는 빌드되는 응용 프로그램이 Azure 클라우드 서비스 프로젝트이며 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용한다고 가정합니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a>방법: 테이블에 엔터티 추가</h2>

엔터티는 **TableEntity**에서 파생된 사용자 지정 클래스를 사용하여 C\# 개체에 매핑됩니다. 테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있지만 다양한 파티션 키를 사용하면 병렬 작업 확장성이 커집니다. 테이블 서비스에 저장되어야 하는 속성의 경우  `get`과  `set`을 모두 표시하는 지원되는 형식의 공용 속성이어야 합니다.
또한 엔터티 형식은  *must* 매개 변수가 없는 생성자를 표시해야 합니다.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }
		
        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

엔터티와 관련된 테이블 작업은 "방법: 테이블 만들기"에서 만든 **CloudTable** 개체를 사용하여 수행됩니다. 수행할 작업은 **TableOperation** 개체로 표시됩니다. 다음 코드 예제에서는 **CloudTable** 개체 만들기 및 **CustomerEntity** 개체 만들기를 보여 줍니다.  작업을 준비하려면 **TableOperation**을 만들어 고객 엔터티를 테이블에 삽입합니다.  마지막으로 **CloudTable.Execute**를 호출하여 작업을 실행합니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a>방법: 엔터티 일괄 삽입</h2>

하나의 쓰기 작업으로 테이블에 엔터티를 일괄 삽입할 수 있습니다. 일괄 작업에 대한 다른 참고 사항은 다음과 같습니다.

1.  동일한 단일 일괄 작업에서 업데이트, 삭제 및 삽입을 수행할 수 있습니다.
2.  단일 일괄 작업에 최대 100개의 엔터티가 포함될 수 있습니다.
3.  단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
4.  쿼리를 일괄 작업으로 수행할 수 있지만 일괄 작업의 유일한 작업이어야 합니다.

<!-- -->
다음 코드 예제에서는 두 개의 엔터티 개체를 만들고 **Insert** 메서드를 사용하여 **TableBatchOperation**에 각 개체를 추가합니다. 그런 다음 **CloudTable.Execute**를 호출하여 작업을 실행합니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a>방법: 파티션의 모든 엔터티 검색</h2>

테이블에서 파티션의 모든 엔터티를 쿼리하려면 **TableQuery** 개체를 사용합니다.
다음 코드 예제에서는  'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a>방법: 파티션의 엔터티 범위 검색</h2>

파티션의 모든 엔터티를 쿼리하지 않으려면 파티션 키 필터를 행 키 필터와 결합하여 범위를 지정할 수 있습니다. 다음 코드 예제에서는 두 개의 필터를 사용하여 행 키(이름)가 알파벳에서 'E'보다 앞에 오는 문자로 시작하는  'Smith' 파티션의 모든 엔터티를 가져온 다음 쿼리 결과를 출력합니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a>방법: 단일 엔터티 검색</h2>

단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **TableOperation**을 사용하여 고객  'Ben Smith'를 지정합니다.
이 메서드는 컬렉션이 아닌 하나의 엔터티만 반환하며, **TableResult.Result**에서 반환된 값은 **CustomerEntity**입니다.
쿼리에 파티션과 행 키를 모두 지정하는 것이 테이블 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

<h2><a name="replace-entity"></a>방법: 엔터티 바꾸기</h2>

엔터티를 업데이트하려면 테이블 서비스에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 테이블 서비스에 저장합니다. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. **Insert**를 호출하는 대신 이 코드에서는 
**Replace**를 사용합니다. 이렇게 하면 서버의 엔터티가 검색된 후 변경되어 작업이 실패하는 경우를 제외하고 서버에서 엔터티가 완전히 바뀝니다.  이러한 실패는 응용 프로그램이 검색 및 업데이트 사이에 다른 응용 프로그램 구성 요소에 의해 변경된 내용을 실수로 덮어쓰는 것을 방지합니다. 이 실패를 올바르게 처리하려면 엔터티를 다시 검색하고 변경(유효한 경우)한 다음 다른 **Replace** 작업을 수행합니다.  다음 섹션에서는 이 동작을 재정의하는 방법을 보여 줍니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="insert-or-replace-entity"></a>방법: 엔터티 삽입 또는 바꾸기</h2>

엔터티가 서버에서 검색된 이후 변경된 경우 **Replace** 작업이 실패합니다.  또한 **Replace**가 성공하려면 먼저 서버에서 엔터티를 검색해야 합니다.
그러나 서버에 엔터티가 있는지 알지 못하며 저장된 현재 값이 부적절하여 업데이트로 모두 덮어써야 하는 경우도 있습니다.  이렇게 하려면 **InsertOrReplace** 작업을 사용합니다.  이 작업은 마지막 업데이트가 수행된 시기에 관계없이 엔터티가 없는 경우 삽입하고, 엔터티가 있는 경우 바꿉니다.  다음 코드 예제에서는 Ben Smith에 대한 고객 엔터티가 계속 검색되지만 **InsertOrReplace**를 사용하여 서버에 다시 저장됩니다.  검색과 업데이트 작업 사이의 모든 엔터티 업데이트를 덮어씁니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="query-entity-properties"></a>방법: 엔터티 속성 하위 집합 쿼리</h2>

테이블 쿼리는 모든 엔터티 속성 대신 엔터티에서 몇 개의 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상할 수 있습니다. 다음 코드의 쿼리는 테이블에 있는 엔터티의 메일 주소만 반환합니다. 이 작업은 **DynamicTableEntity**의 쿼리와 **EntityResolver**를 사용하여 수행합니다. 이 [블로그 게시물][]에서 프로젝션에 대해 자세히 알아볼 수 있습니다. 로컬 저장소 에뮬레이터에서는 프로젝션이 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a>방법: 엔터티 삭제</h2>

엔터티를 검색한 후 엔터티 업데이트를 위해 표시된 것과 동일한 패턴을 사용하여 엔터티를 쉽게 삭제할 수 있습니다.  다음 코드에서는 고객 엔터티를 검색하고 삭제합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a>방법: 테이블 삭제</h2>

마지막으로, 다음 코드 예제에서는 저장소 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 기간 동안 다시 만들 수 없습니다.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

<h2><a name="next-steps"></a>다음 단계</h2>

이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

<ul>
<li>사용 가능한 API에 대한 자세한 내용은 테이블 서비스 참조 설명서를 참조하세요.
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 참조</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/windowsazure/dd179355">REST API 참조</a></li>
  </ul>
</li>
<li>Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 <a href="http://msdn.microsoft.com/library/windowsazure/gg433040.aspx">Azure에 데이터 저장 및 액세스</a>를 참조하세요.</li>
<li><a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK를 사용하여 Azure 저장소에서 작동하도록 작성하는 코드를 간소화하는 방법을 알아보세요.</li>
<li>Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하세요.
  <ul>
    <li><a href="/ko-kr/documentation/articles/storage-dotnet-how-to-use-blobs/">Blob 저장소</a>를 사용하여 구조화되지 않은 데이터를 저장합니다.</li>
    <li><a href="/ko-kr/documentation/articles/storage-dotnet-how-to-use-queues/">큐 저장소</a>를 사용하여 구조화된 데이터를 저장합니다.</li>
    <li><a href="/ko-kr/documentation/articles/sql-database-dotnet-how-to-use/">SQL 데이터베이스</a>를 사용하여 관계형 데이터를 저장합니다.</li>
  </ul>
</li>
</ul>

  [다음 단계]: #next-steps
  [테이블 서비스 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #create-account
  [Visual Studio에서 Azure 프로젝트 만들기]: #create-project
  [저장소에 액세스하도록 응용 프로그램 구성]: #configure-access
  [저장소 연결 문자열 설정]: #setup-connection-string
  [방법: 프로그래밍 방식으로 테이블 저장소 액세스]: #configure-access
  [방법: 테이블 만들기]: #create-table
  [방법: 테이블에 엔터티 추가]: #add-entity
  [방법: 엔터티 일괄 삽입]: #insert-batch
  [방법: 파티션의 모든 엔터티 검색]: #retrieve-all-entities
  [방법: 파티션의 엔터티 범위 검색]: #retrieve-range-entities
  [방법: 단일 엔터티 검색]: #retrieve-single-entity
  [방법: 엔터티 바꾸기]: #replace-entity
  [방법: 엔터티 삽입 또는 바꾸기]: #insert-or-replace-entity
  [방법: 엔터티 속성 하위 집합 쿼리]: #query-entity-properties
  [방법: 엔터티 삭제]: #delete-entity
  [방법: 테이블 삭제]: #delete-table
  [Azure SDK for .NET 다운로드 및 설치]: /ko-kr/develop/net/
  [Visual Studio에서 Azure 프로젝트 만들기]: http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [블로그 게시물]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET 클라이언트 라이브러리 참조]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure에서 데이터 저장 및 액세스]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
  [연결 문자열 구성]: http://msdn.microsoft.com/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
