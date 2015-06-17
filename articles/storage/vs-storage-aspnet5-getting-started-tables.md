<properties 
	pageTitle="Azure 저장소 시작" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [시작하기](vs-storage-aspnet5-getting-started-tables.md)
> - [변경된 내용](vs-storage-aspnet5-what-happened.md)

## Azure 저장소 시작(ASP.NET vNext 프로젝트)

> [AZURE.SELECTOR]
> - [Blob](vs-storage-aspnet5-getting-started-blobs.md)
> - [큐](vs-storage-aspnet5-getting-started-queues.md)
> - [테이블](vs-storage-aspnet5-getting-started-tables.md)

Azure 테이블 저장소 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.  자세한 내용은 [.NET에서 테이블 저장소를 사용하는 방법](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET")(영문)을 참조하세요.

ASP.NET 5 프로젝트에서 테이블에 프로그래밍 방식으로 액세스하려면 다음 항목(아직 없는 경우)을 추가해야 합니다.

1. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.ConfigurationModel;
		using System.Threading.Tasks;

2. 다음 코드를 사용하여 구성 설정을 가져옵니다.

		Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### 저장소 연결 문자열 가져오기
테이블로 작업을 수행하려면 먼저 테이블을 저장할 저장소 계정의 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET vNext 프로젝트를 사용하는 경우 다음 코드에 나온 대로 구성 개체의 get 메서드를 호출하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져올 수 있습니다.

**참고:** ASP.NET 5에서 Azure 저장소로 나가는 호출을 수행하는 API는 비동기입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](http://msdn.microsoft.com/library/hh191443.aspx)을 참조하세요. 다음 코드는 비동기 프로그래밍 방법을 사용한다고 가정합니다.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### 테이블 만들기
**CloudTableClient** 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudTableClient** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다. 코드가 "people"이라는 이름의 테이블을 참조하려고 합니다. 이 이름의 테이블을 찾을 수 없으면 새로 만듭니다.

**참고:** 이 가이드의 모든 코드는 응용 프로그램이 Azure 클라우드 서비스 프로젝트에서 빌드된다고 가정하며 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. 이 모든 코드를 다음 섹션에 나오는 코드 앞에 사용하세요.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

##### 테이블에 엔터티 추가
테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 **CustomerEntity**라는 엔터티 클래스를 정의합니다.

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

엔터티와 관련된 테이블 작업은 이전에 "테이블 만들기"에서 만든 **CloudTable** 개체를 사용하여 수행합니다. **TableOperation** 개체를 수행할 작업을 나타냅니다. 다음 코드 예제에서는 **CloudTable** 개체와 **CustomerEntity** 개체를 만드는 방법을 보여 줍니다. 작업을 준비하기 위해 고객 엔터티를 테이블에 삽입하는 **TableOperation**이 만들어집니다. 마지막으로 CloudTable.ExecuteAsync를 호출하여 작업이 실행됩니다.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

##### 엔터티 일괄 삽입
하나의 쓰기 작업으로 테이블에 여러 엔터티를 삽입할 수 있습니다. 다음 코드 예제에서는 두 개의 엔터티 개체("Jeff Smith" 및 "Ben Smith")를 만들고 Insert 메서드를 사용하여 **TableBatchOperation** 개체에 이 두 개체를 추가한 다음 CloudTable.ExecuteBatchAsync를 호출하여 작업을 시작합니다.

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
	await table.ExecuteBatchAsync(batchOperation);

##### 파티션의 모든 엔터티 가져오기
테이블에서 파티션의 모든 엔터티를 쿼리하려면 **TableQuery** 개체를 사용합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


##### 단일 엔터티 가져오기
단일 특정 엔터티를 가져오는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **TableOperation** 개체를 사용하여 'Ben Smith'라는 고객을 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며, TableResult.Result에서 반환된 값은 **CustomerEntity** 개체입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 **테이블** 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##### 엔터티 삭제
엔터티를 찾은 후 삭제할 수 있습니다. 다음 코드에서는 "Ben Smith"라는 고객 엔터티를 찾아 보고 찾으면 삭제합니다.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Azure 저장소에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/storage/)
또한 [서버 탐색기로 저장소 리소스 탐색](http://msdn.microsoft.com/library/azure/ff683677.aspx)(영문) 및 [ASP.NET 5](http://www.asp.net/vnext)(영문)를 참조하세요.

<!--HONumber=42-->
 