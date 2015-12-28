<properties
	pageTitle="테이블 저장소 및 Visual Studio 연결 서비스 시작(ASP.NET) | Microsoft Azure"
	description="Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio ASP.NET 프로젝트에서 Azure 테이블 저장소 사용을 시작하는 방법입니다."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="tarcher"/>

# 테이블 저장소 및 Visual Studio 연결 서비스 시작(ASP.NET)

## 개요
이 문서에서는 Visual Studio의 **연결된 서비스 추가** 대화 상자를 사용하여 Azure 저장소 계정을 ASP.NET 프로젝트에서 생성하거나 참조한 후 Azure 테이블 저장소를 Visual Studio에서 사용하는 방법을 설명합니다. 이 문서에서는 테이블 만들기 및 삭제, 테이블 엔터티 작업 등 Azure 테이블에서 일반적인 작업을 수행하는 방법을 보여 줍니다. 샘플은 C# 코드로 작성되었으며 [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)을 사용합니다. Azure 테이블 저장소를 사용하는 방법에 대한 일반적인 정보는 [.NET에서 테이블 저장소를 사용하는 방법](storage-dotnet-how-to-use-tables.md)을 참조하세요.

Azure 테이블 저장소를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.


## 코드에서 테이블 액세스하기

1. C# 파일 맨 위의 네임스페이스 선언에 이러한 **using** 문이 포함되어 있는지 확인합니다.

		 using Microsoft.Azure;
		 using Microsoft.WindowsAzure.Storage;
		 using Microsoft.WindowsAzure.Storage.Auth;
		 using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **참고:** 다음 샘플의 코드 앞에 위의 코드를 모두 사용합니다.

3. 저장소 계정의 테이블 개체를 참조하려면 **CloudTableClient** 개체를 가져옵니다.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. 특정 테이블과 엔터티를 참조하려면 **CloudTable** 참조 개체를 가져옵니다.

    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## 코드에서 테이블 만들기

Azure 테이블을 만들려면 이전 코드에 **CreateIfNotExistsAsync()**에 대한 호출을 추가합니다.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();


## 엔터티 일괄 삽입

하나의 쓰기 작업으로 테이블에 여러 엔터티를 삽입할 수 있습니다. 다음 코드 예제에서는 두 개의 엔터티 개체("Jeff Smith" 및 "Ben Smith")를 만들고 Insert 메서드를 사용하여 **TableBatchOperation** 개체에 이 두 개체를 추가한 다음 **CloudTable.ExecuteBatchAsync**를 호출하여 작업을 시작합니다.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

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
	await peopleTable.ExecuteBatchAsync(batchOperation);

## 파티션의 모든 엔터티 가져오기
테이블에서 파티션의 모든 엔터티를 쿼리하려면 **TableQuery** 개체를 사용합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## 단일 엔터티 가져오기
단일 특정 엔터티를 가져오는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **TableOperation** 개체를 사용하여 'Ben Smith'라는 고객을 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며, **TableResult.Result**에서 반환된 값은 **CustomerEntity** 개체입니다. 쿼리에 파티션 키와 행 키를 모두 지정하는 것이 테이블 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

        // Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

        // Create a retrieve operation that takes a customer entity.
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## 엔터티 삭제
엔터티를 찾은 후 삭제할 수 있습니다. 다음 코드에서는 "Ben Smith"라는 고객 엔터티를 찾아 보고 찾으면 삭제합니다.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Couldn't delete the entity.");

## 다음 단계

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

<!---HONumber=AcomDC_1217_2015-->