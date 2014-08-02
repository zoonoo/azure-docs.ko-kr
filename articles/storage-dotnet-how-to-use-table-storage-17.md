<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service (2.0)" pageTitle="How to use table storage | Microsoft Azure" metaKeywords="Get started Azure table, Azure nosql, Azure large structured data store, Azure table, Azure table storage, Azure table .NET, Azure table storage .NET, Azure table C#, Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Table Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

테이블 저장소 서비스를 사용하는 방법
====================================

[버전 1.7](/en-us/develop/net/how-to-guides/table-services-v17/ "버전 1.7") [버전 2.0](/en-us/develop/net/how-to-guides/table-services/ "버전 2.0")

이 가이드에서는 Azure 테이블 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C\# 코드로 작성되었고 .NET API를 사용합니다. **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리** 등의 시나리오를 다룹니다. 테이블에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

-   [테이블 서비스 정의](#what-is)
-   [개념](#concepts)
-   [Azure 저장소 계정 만들기](#create-account)
-   [저장소 연결 문자열 설정](#setup-connection-string)
-   [방법: 프로그래밍 방식으로 테이블 저장소 액세스](#configure-access)
-   [방법: 테이블 만들기](#create-table)
-   [방법: 테이블에 엔터티 추가](#add-entity)
-   [방법: 엔터티 일괄 삽입](#insert-batch)
-   [방법: 파티션의 모든 엔터티 검색](#retrieve-all-entities)
-   [방법: 파티션의 엔터티 범위 검색](#retrieve-range-entities)
-   [방법: 단일 엔터티 검색](#retrieve-single-entity)
-   [방법: 엔터티 업데이트](#update-entity)
-   [방법: 엔터티 속성 하위 집합 쿼리](#query-entity-properties)
-   [방법: 엔터티 삽입 또는 바꾸기](#insert-entity)
-   [방법: 엔터티 삭제](#delete-entity)
-   [방법: 테이블 삭제](#delete-table)
-   [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

계정 만들기Azure 저장소 계정 만들기
-----------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

연결 문자열 설정저장소 연결 문자열 설정
---------------------------------------

Azure .NET 저장소 API는 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 코드에서 하드 코딩하는 대신 구성 파일에 저장소 연결 문자열을 배치할 수 있습니다.

-   Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
-   Azure 웹 사이트나 Azure 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 가이드의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를 사용하여 연결 문자열을 검색할 수 있습니다.

### 클라우드 서비스를 사용하는 경우 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리 포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나 작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-17/blob5.png)

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다.
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-17/blob6.png)

    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다.
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-17/blob7.png)

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화 상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Azure 저장소)를 대상으로 지정할지 또는 클라우드의 실제 저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 하나로 작동합니다. 앞에서 Azure에 만든 저장소 계정에 Blob 데이터를 저장하려면 이 자습서의 이전 단계에서 복사한 **기본 액세스 키** 값을 입력합니다.
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-17/blob8.png)

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 "친근한" 이름으로 변경합니다. 이 가이드의 코드 뒷부분에서 이 연결 문자열을 참조합니다.
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-17/blob9.png)

### 웹 사이트 또는 가상 컴퓨터를 사용하는 경우 연결 문자열 구성

웹 사이트나 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config`)을 사용하는 것이 좋습니다. `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

저장소 연결 문자열에 대한 자세한 내용은 [연결 문자열 구성](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx)을 참조하십시오.

이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

프로그래밍 방식으로 액세스방법: 프로그래밍 방식으로 테이블 저장소 액세스
------------------------------------------------------------------------

프로그래밍 방식으로 Azure 저장소에 액세스하려는 C\# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

**CloudStorageAccount** 유형과 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

테이블 만들기방법: 테이블 만들기
--------------------------------

**CloudTableClient** 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudTableClient** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다. 이 가이드의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

테이블에 엔터티 추가방법: 테이블에 엔터티 추가
----------------------------------------------

엔터티는 **TableServiceEntity**에서 파생된 사용자 지정 클래스를 사용하여 C\# 개체에 매핑됩니다. 테이블에 엔터티를 추가하려면 먼저 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있습니다.

    public class CustomerEntity : TableServiceEntity
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

엔터티를 포함하는 테이블 작업은 **TableServiceContext** 개체가 필요합니다. 이 개체는 클라이언트 코드에서 만들어지고 액세스되는 모든 테이블 엔터티의 클라이언트 쪽 상태를 추적합니다. 각 엔터티를 나타내는 클라이언트 쪽 개체를 유지 관리하면 저장 작업을 수행할 때 테이블 서비스에서 변경된 내용이 있는 개체만 업데이트되므로 쓰기 작업이 더 효율적으로 수행됩니다. 다음 코드는 **GetDataServiceContext** 메서드를 호출하여 **TableServiceContext** 개체를 만듭니다. 그런 다음 **CustomerEntity** 클래스의 인스턴스를 만듭니다. 또한 **serviceContext.AddObject**를 호출하여 테이블에 새 엔터티를 삽입합니다. 그러면 엔터티 개체가 **serviceContext**에 추가되지만 서비스 작업이 수행되지 않습니다. 마지막으로 이 코드는 **SaveChangesWithRetries** 메서드가 호출되면 테이블 서비스에 새 엔터티를 보냅니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Add the new customer to the people table
    serviceContext.AddObject("people", customer1);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

엔터티 일괄 삽입방법: 엔터티 일괄 삽입
--------------------------------------

하나의 쓰기 작업으로 테이블 서비스에 엔터티를 일괄 삽입할 수 있습니다. 다음 코드는 세 개의 엔터티 개체를 만들고 **AddObject** 메서드를 사용하여 서비스 컨텍스트에 각 개체를 추가합니다. 그런 다음 **SaveChangesOptions.Batch** 매개 변수를 사용하여 **SaveChangesWithRetries**를 호출합니다. **SaveChangesOptions.Batch**를 생략하면 테이블 서비스에 대해 세 번의 개별 호출이 수행됩니다. 일괄 작업에 대한 다른 참고 사항은 다음과 같습니다.

1.  일괄 업데이트, 삭제 또는 삽입을 수행할 수 있습니다.
2.  단일 일괄 작업에 최대 100개의 엔터티가 포함될 수 있습니다.
3.  단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.

<!-- -->

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a customer entity and add to the table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Create another customer entity and add to the table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Create a customer entity and add to the table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

모든 엔터티 검색방법: 파티션의 모든 엔터티 검색
-----------------------------------------------

테이블에서 파티션의 엔터티를 쿼리하려면 LINQ 쿼리를 사용할 수 있습니다. **serviceContext.CreateQuery**를 호출하여 데이터 원본에서 쿼리를 만듭니다. 다음 코드는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. LINQ 쿼리의 결과로 **AsTableServiceQuery&lt;CustomerEntity\>**를 호출하여 **CloudTableQuery** 개체 만들기를 마칩니다. 그런 다음 **foreach** 루프에서 만든 **partitionQuery** 개체를 사용하여 결과를 이용할 수 있습니다. 이 코드는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

엔터티 범위 검색방법: 파티션의 엔터티 범위 검색
-----------------------------------------------

파티션의 일부 엔터티만 쿼리하려면 일반 보드 큼(\>) 및 보다 작음(&lt;) 연산자 대신 **CompareTo** 메서드를 사용하여 범위를 지정할 수 있습니다. 이 메서드를 사용할 때 더 적절한 쿼리가 생성되기 때문입니다. 다음 코드는 두 개의 필터를 사용하여 행 키(이름)가 알파벳에서 'E'까지의 문자로 시작하는 'Smith' 파티션의 모든 엔터티를 가져옵니다. 그런 다음 쿼리 결과를 출력합니다. 이 가이드의 일괄 삽입 섹션에 있는 테이블에 추가된 엔터티를 사용할 경우 두 개의 엔터티(Ben 및 Denise Smith)만 반환되고 Jeff Smith는 반환되지 않습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key,
    // with the row key being up to the letter "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

단일 엔터티 검색방법: 단일 엔터티 검색
--------------------------------------

단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드에서는 두 개의 필터를 사용하여 고객 'Jeff Smith'를 지정합니다. **AsTableServiceQuery**를 호출하는 대신 이 코드에서는 **FirstOrDefault**를 호출합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하므로, 이 코드는 반환 값을 **CustomerEntity** 개체에 직접 할당합니다. 파티션과 행 키가 정확하게 일치하는 엔터티가 없는 경우 null 값이 반환됩니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 테이블 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

엔터티 업데이트방법: 엔터티 업데이트
------------------------------------

엔터티를 업데이트하려면 테이블 서비스에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 테이블 서비스에 저장합니다. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. 엔터티를 삽입할 때처럼 **AddObject**를 호출하는 대신 이 코드에서는 **UpdateObject**를 호출합니다. 이 응용 프로그램에서 엔터티를 검색한 이후에 다른 응용 프로그램에서 엔터티를 변경하지 않은 경우 **SaveChangesWithRetries** 메서드는 테이블 서비스를 호출하고 엔터티는 업데이트됩니다. 다른 응용 프로그램에서 엔터티를 변경한 경우에는 예외가 발생하고 엔터티를 다시 검색하고 수정한 다음 저장해야 합니다. 이 다시 시도 패턴은 분산된 저장소 시스템에서 일반적으로 발생합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Specify a new phone number
    specificEntity.PhoneNumber = "425-555-0105";

    // Update the entity
    serviceContext.UpdateObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

속성 하위 집합 쿼리방법: 엔터티 속성 하위 집합 쿼리
---------------------------------------------------

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 다음 코드의 쿼리는 테이블에 있는 엔터티의 전자 메일 주소만 반환합니다. 이 [블로그 게시글](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx)에서 프로젝션에 대해 자세히 알아볼 수 있습니다. 로컬 저장소 에뮬레이터에서는 프로젝션이 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Define a projection query that retrieves only the Email property
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // You can specify additional fields here
        };

    // Loop through the results, displaying the Email value
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

엔터티 삽입 또는 바꾸기방법: 엔터티 삽입 또는 바꾸기
----------------------------------------------------

엔터티가 테이블에 이미 있는지 모르는 상태에서 테이블에 엔터티를 추가할 수 있습니다. 이 경우 삽입 또는 바꾸기 작업을 사용하여 엔터티가 없는 경우 엔터티를 삽입하고 엔터티가 있는 경우 기존 엔터티를 바꾸도록 하는 단일 요청을 생성할 수 있습니다. 이전 예제를 기반으로 하는 다음 코드에서는 'Walter Harp'에 대한 엔터티를 삽입하거나 바꿉니다. 이 코드에서는 새 엔터티를 만든 후 **serviceContext.AttachTo** 메서드를 호출합니다. 그런 다음 **UpdateObject**를 호출하고 마지막으로 **SaveChangesOptions.ReplaceOnUpdate** 매개 변수를 사용하여 **SaveChangesWithRetries**를 호출합니다. **SaveChangesOptions.ReplaceOnUpdate** 매개 변수를 생략하면 삽입 또는 병합 작업이 수행됩니다. 로컬 저장소 에뮬레이터에서는 삽입 또는 바꾸기가 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다. 이 [블로그 게시물](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx)에서 삽입 또는 바꾸기 및 삽입 또는 병합에 대해 자세히 알아볼 수 있습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Attach this customer to the people table
    serviceContext.AttachTo("people", customer5);

    // Insert this customer if new, or replace if exists
    serviceContext.UpdateObject(customer5);

    // Submit the operation the table service, using the ReplaceOnUpdate option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

엔터티 삭제방법: 엔터티 삭제
----------------------------

엔터티를 검색한 다음 쉽게 삭제할 수 있습니다. 또한 **AttachTo** 메서드를 사용하여 서버에서 엔터티를 검색하지 않고 엔터티 추적을 시작할 수 있습니다(위의 삽입 또는 바꾸기 참조). **serviceContext**를 통해 엔터티가 추적되면 삭제할 엔터티와 함께 **DeleteObject**를 호출합니다. 그런 다음 **SaveChangesWithRetries**를 호출합니다. 다음 코드에서는 고객 엔터티를 검색하고 삭제합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Delete the entity
    serviceContext.DeleteObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

테이블 삭제방법: 테이블 삭제
----------------------------

마지막으로, 다음 코드는 저장소 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 기간 동안 다시 만들 수 없습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Delete the table it if exists
    tableClient.DeleteTableIfExist("people");

다음 단계다음 단계
------------------

이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   사용 가능한 API에 대한 자세한 내용은 Blob 서비스 참조 설명서를 참조하십시오.
    -   [.NET 클라이언트 라이브러리 참조](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST API 참조](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)를 참조하십시오.
-   Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
    -   [Blob 저장소](/en-us/develop/net/how-to-guides/blob-storage/)를 사용하여 구조화되지 않은 데이터를 저장합니다.
    -   [SQL 데이터베이스](/en-us/develop/net/how-to-guides/sql-database/)를 사용하여 관계형 데이터를 저장합니다.

