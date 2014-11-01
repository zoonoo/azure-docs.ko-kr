##### 테이블 만들기

**CloudTableClient** 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudTableClient** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다. 코드가 “people”이라는 이름의 테이블을 참조하려고 합니다. 이 이름의 테이블을 찾을 수 없으면 새로 만듭니다.

**참고:** 이 가이드의 모든 코드에서는 빌드되는 응용 프로그램이 Azure 클라우드 서비스 프로젝트이며 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용한다고 가정합니다.

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

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

엔터티와 관련된 테이블 작업은 이전에 "테이블 만들기"에서 만든 **CloudTable** 개체를 사용하여 수행합니다. **TableOperation** 개체를 수행할 작업을 나타냅니다. 다음 코드 예제에서는 **CloudTable** 개체와 **CustomerEntity** 개체를 만드는 방법을 보여 줍니다. 작업을 준비하기 위해 고객 엔터티를 테이블에 삽입하는 **TableOperation**이 만들어집니다. 마지막으로 CloudTable.Execute를 호출하여 작업이 실행됩니다.

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

##### 엔터티 일괄 삽입

하나의 쓰기 작업으로 테이블에 여러 엔터티를 삽입할 수 있습니다. 다음 코드 예제에서는 두 개의 엔터티 개체("Jeff Smith" 및 "Ben Smith")를 만들고 Insert 메서드를 사용하여 **TableBatchOperation** 개체에 이 두 개체를 추가한 다음 CloudTable.Execute를 호출하여 작업을 시작합니다.

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

##### 파티션의 모든 엔터티 가져오기

테이블에서 파티션의 모든 엔터티를 쿼리하려면 **TableQuery** 개체를 사용합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

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

##### 단일 엔터티 가져오기

단일 특정 엔터티를 가져오는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **TableOperation** 개체를 사용하여 'Ben Smith'라는 고객을 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며, TableResult.Result에서 반환된 값은 **CustomerEntity** 개체입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 **테이블** 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

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
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

[Azure 저장소에 대한 자세한 정보][Azure 저장소에 대한 자세한 정보]
[서버 탐색기에서 저장소 리소스 탐색][서버 탐색기에서 저장소 리소스 탐색]도 참조하세요.

  [Azure 저장소에 대한 자세한 정보]: http://azure.microsoft.com/documentation/services/storage/
  [서버 탐색기에서 저장소 리소스 탐색]: http://msdn.microsoft.com/ko-kr/library/azure/ff683677.aspx
