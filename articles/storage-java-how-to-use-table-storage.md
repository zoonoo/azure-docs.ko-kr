<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="Table Service" pageTitle="How to use table storage (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="" solutions="" manager="" editor="" />

Java에서 테이블 저장소 서비스를 사용하는 방법
=============================================

이 가이드에서는 Azure 테이블 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Java 코드로 작성되었습니다. **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리** 등의 시나리오를 다룹니다. 테이블에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

목차
----

-   [테이블 저장소 정의](#what-is)
-   [개념](#Concepts)
-   [Azure 저장소 계정 만들기](#CreateAccount)
-   [Java 응용 프로그램 만들기](#CreateApplication)
-   [테이블 저장소에 액세스하도록 응용 프로그램 구성](#ConfigureStorage)
-   [Azure 저장소 연결 문자열 설정](#ConnectionString)
-   [방법: 테이블 만들기](#CreateTable)
-   [방법: 테이블에 엔터티 추가](#AddEntity)
-   [방법: 엔터티 일괄 삽입](#InsertBatch)
-   [방법: 파티션의 모든 엔터티 검색](#RetrieveEntities)
-   [방법: 파티션의 엔터티 범위 검색](#RetrieveRange)
-   [방법: 단일 엔터티 검색](#RetriveSingle)
-   [방법: 엔터티 수정](#ModifyEntity)
-   [방법: 엔터티 속성 하위 집합 쿼리](#QueryProperties)
-   [방법: 엔터티 삽입 또는 바꾸기](#InsertOrReplace)
-   [방법: 엔터티 삭제](#DeleteEntity)
-   [방법: 테이블 삭제](#DeleteTable)
-   [다음 단계](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Azure 저장소 계정 만들기
------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Java 응용 프로그램 만들기
-------------------------

이 가이드에서는 Java 응용 프로그램 내에서 로컬로 실행할 수 있거나 Azure의 웹 역할 또는 작업자 역할 내에서 실행되는 코드에서 실행할 수 있는 저장소 기능을 사용합니다. 이 가이드의 내용은 JDK(Java 개발 키트)를 이미 다운로드 및 설치했으며 [Java용 Azure SDK](http://www.windowsazure.com/ko-kr/develop/java/)의 지침에 따라 Java용 Azure 라이브러리 및 Azure SDK를 설치했고 Azure 구독에서 Azure 저장소 계정을 만들었다고 가정합니다.

응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다. Java 프로젝트를 컴파일하고 Java용 Azure 라이브러리를 참조할 수만 있으면 됩니다.

테이블 저장소에 액세스하도록 응용 프로그램 구성
-----------------------------------------------

테이블에 액세스하기 위해 Azure 저장소 API를 사용하려는 Java 파일의 맨 위에 다음 import 문을 추가합니다.

    // Include the following imports to use table APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

Azure 저장소 연결 문자열 설정
-----------------------------

Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 끝점 및 자격 증명을 저장합니다. 클라이언트 응용 프로그램에서 실행할 경우, 저장소 계정의 이름 및 관리 포털에 나열된 저장소 계정의 기본 액세스 키를 *AccountName*과 *AccountKey* 값의 형식으로 사용하여 저장소 연결 문자열을 제공해야 합니다. 이 예제에서는 연결 문자열을 저장할 정적 필드를 어떻게 선언할 수 있는지 보여 줍니다.

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Azure의 역할 내에서 실행되는 응용 프로그램에서는 이 문자열이 서비스 구성 파일 ServiceConfiguration.cscfg에 저장될 수 있고, **RoleEnvironment.getConfigurationSettings** 메서드 호출을 통해 이 문자열에 액세스할 수 있습니다. 다음은 서비스 구성 파일에 있는 *StorageConnectionString*이라는 **Setting** 요소로부터 연결 문자열을 가져오는 예입니다.

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

아래 샘플에서는 저장소 연결 문자열을 가져오기 위해 위의 두 정의 중 하나를 사용한 것으로 가정합니다.

방법: 테이블 만들기
-------------------

**CloudTableClient** 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudTableClient** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다. 이 가이드의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create the table if it doesn't exist.
    String tableName = "people";
    tableClient.createTableIfNotExists(tableName);

방법: 테이블에 엔터티 추가
--------------------------

엔터티는 **TableEntity**를 구현하는 사용자 지정 클래스를 사용하여 Java 개체에 매핑됩니다. 사용 편의를 위해 **TableServiceEntity** 클래스는 **TableEntity**를 구현하고, 리플렉션을 사용하여 속성에 맞춰 명명된 getter 및 setter 메서드에 속성을 매핑합니다. 테이블에 엔터티를 추가하려면 먼저 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있습니다.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

엔터티를 포함하는 테이블 작업에는 **TableOperation** 개체가 필요합니다. 이 개체는 엔터티에 대해 수행될 작업을 정의하고, 이 작업은 **CloudTableClient** 개체와 함께 실행될 수 있습니다. 다음 코드에서는 **CustomerEntity** 클래스의 새 인스턴스를 저장될 일부 고객 데이터와 함께 만듭니다. 그런 다음 엔터티를 테이블에 삽입하기 위해 **TableOperation.insert**를 호출하여 **TableOperation** 개체를 만들고, 새로운 **CustomerEntity**를 이 개체와 연결합니다. 끝으로 이 코드는 **CloudTableClient**에 대해 **execute** 메서드를 호출하여 "people" 테이블 및 새로운 **TableOperation**을 지정한 후, 새 고객 엔터티를 "people" 테이블에 삽입하기 위해 저장소 서비스로 요청을 보냅니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insert(customer1);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer1);

방법: 엔터티 일괄 삽입
----------------------

하나의 쓰기 작업으로 테이블 서비스에 엔터티를 일괄 삽입할 수 있습니다. 다음 코드는 **TableBatchOperation** 개체를 만든 다음, 이 개체에 3개의 삽입 작업을 추가합니다. 각 삽입 작업을 추가하기 위해 새 엔터티 개체를 만들고 값을 설정한 후 **insert** 메서드를 **TableBatchOperation** 개체에 대해 호출하여 해당 엔터티를 새로운 삽입 작업과 연결합니다. 그런 다음, 이 코드는 **CloudTableClient**에 대해 **execute**를 호출하여 "people" 테이블 및 **TableBatchOperation** 개체를 지정한 후, 테이블 일괄 작업을 단일 요청으로 저장소 서비스로 보냅니다. 일괄 작업에 대해 유의할 사항은 다음과 같습니다.

1.  최대 100개의 삽입, 삭제, 병합, 바꾸기, 삽입 또는 병합 및 삽입 또는 바꾸기 작업을 임의로 조합하여 단일 일괄 작업으로 수행할 수 있습니다.
2.  검색 작업이 일괄 작업의 유일한 작업이면 일괄 작업에 검색 작업이 포함될 수 있습니다.
3.  단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
4.  일괄 작업은 4MB 데이터 페이로드로 제한됩니다.

<!-- -->

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Execute the batch of operations on the "people" table.
    tableClient.execute("people", batchOperation);

방법: 파티션의 모든 엔터티 검색
-------------------------------

테이블에서 파티션의 엔터티를 쿼리하려는 경우 **TableQuery**를 사용할 수 있습니다. 지정된 결과 유형을 반환하는 쿼리를 특정 테이블에 대해 만들려면 **TableQuery.from**을 호출하십시오. 다음 코드는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. **TableQuery.generateFilterCondition**은 쿼리에 필요한 필터를 만들기 위한 도우미 메서드입니다. 쿼리에 필터를 적용하려면 **TableQuery.from** 메서드에 의해 반환된 참조에 대해 **where**를 호출하십시오. **CloudTableClient** 개체에 대해 **execute**를 호출하여 쿼리가 실행되면 쿼리는 **CustomerEntity** 결과 유형이 지정된 **반복기**를 반환합니다. 그러면 반환된 **반복기**를 for each 루프에서 사용하여 결과를 이용할 수 있습니다. 이 코드는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

방법: 파티션의 엔터티 범위 검색
-------------------------------

파티션의 모든 엔터티를 쿼리하지 않으려면 비교 연산자를 필터에서 사용하여 범위를 지정할 수 있습니다. 다음 코드는 두 개의 필터를 결합하여 행 키(이름)가 알파벳에서 'E'까지의 문자로 시작하는 'Smith' 파티션의 모든 엔터티를 가져옵니다. 그런 다음 쿼리 결과를 출력합니다. 이 가이드의 일괄 삽입 섹션에 있는 테이블에 추가된 엔터티를 사용할 경우 두 개의 엔터티(Ben 및 Denise Smith)만 반환되고 Jeff Smith는 반환되지 않습니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

방법: 단일 엔터티 검색
----------------------

단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드는 **TableQuery**를 만들고 필터를 사용하는 대신, **TableOperation.retrieve**를 파티션 키 및 행 키 매개 변수와 함께 호출하여 고객 'Jeff Smith'를 지정합니다. 코드가 실행되면 검색 작업은 컬렉션 대신 엔터티 1개만 반환합니다. **getResultAsType** 메서드는 결과를 할당 대상, 즉 **CustomerEntity** 개체의 형식으로 캐스트합니다. 이 형식이 쿼리에 지정된 형식과 호환되지 않으면 예외가 발생합니다. 파티션과 행 키가 정확하게 일치하는 엔터티가 없는 경우 null 값이 반환됩니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 테이블 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();

방법: 엔터티 수정
-----------------

엔터티를 수정하려면 테이블 서비스에서 엔터티를 검색하고 엔터티 개체를 변경한 후, 바꾸기 또는 병합 작업으로 변경 사항을 테이블 서비스에 다시 저장하십시오. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. 삽입하기 위해 사용했던 **TableOperation.insert**를 호출하는 대신, 이 코드는 **TableOperation.replace**를 호출합니다. 이 응용 프로그램에서 엔터티를 검색한 이후에 다른 응용 프로그램에서 엔터티를 변경하지 않은 경우 **CloudTableClient.execute** 메서드가 테이블 서비스를 호출하고 엔터티는 교체됩니다. 다른 응용 프로그램에서 엔터티를 변경한 경우에는 예외가 발생하고 엔터티를 다시 검색하고 수정한 다음 저장해야 합니다. 이 낙관적 동시성 다시 시도 패턴은 분산된 저장소 시스템에서 일반적으로 발생합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();
        
    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    tableClient.execute("people", replaceEntity);

방법: 엔터티 속성 하위 집합 쿼리
--------------------------------

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상할 수 있습니다. 다음 코드의 쿼리는 **select** 메서드를 사용하여 테이블에 있는 엔터티의 전자 메일 주소만 반환합니다. 이 결과는 서버에서 반환된 엔터티에 대해 형식 변환을 수행하는 **EntityResolver**를 통해 **String** 컬렉션에 프로젝트됩니다. 이 [블로그 게시글](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx)에서 프로젝션에 대해 자세히 알아볼 수 있습니다. 로컬 저장소 에뮬레이터에서는 프로젝션이 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery = 
        TableQuery.from("people", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

방법: 엔터티 삽입 또는 바꾸기
-----------------------------

엔터티가 테이블에 이미 있는지 모르는 상태에서 테이블에 엔터티를 추가할 수 있습니다. 이 경우 삽입 또는 바꾸기 작업을 사용하여 엔터티가 없는 경우 엔터티를 삽입하고 엔터티가 있는 경우 기존 엔터티를 바꾸도록 하는 단일 요청을 생성할 수 있습니다. 이전 예제를 기반으로 하는 다음 코드에서는 'Walter Harp'에 대한 엔터티를 삽입하거나 바꿉니다. 이 코드에서는 새 엔터티를 만든 후 **TableOperation.insertOrReplace** 메서드를 호출합니다. 그런 다음, 이 코드는 테이블 및 테이블 삽입 또는 바꾸기 작업을 매개 변수로 하여 **CloudTableClient**에 대해 **execute**를 호출합니다. 엔터티의 일부만 업데이트하려면 **TableOperation.insertOrMerge** 메서드를 대신 사용할 수 있습니다. 로컬 저장소 에뮬레이터에서는 삽입 또는 바꾸기가 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다. 이 [블로그 게시물](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx)에서 삽입 또는 바꾸기 및 삽입 또는 병합에 대해 자세히 알아볼 수 있습니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer5);

방법: 엔터티 삭제
-----------------

엔터티를 검색한 다음 쉽게 삭제할 수 있습니다. 엔터티를 검색한 후, 삭제할 엔터티와 함께 **TableOperation.delete**를 호출하십시오. 그런 다음 **CloudTableClient**에 대해 **execute**를 호출하십시오. 다음 코드에서는 고객 엔터티를 검색하고 삭제합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
            tableClient.execute("people", retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    tableClient.execute("people", deleteSmithJeff);

방법: 테이블 삭제
-----------------

마지막으로, 다음 코드는 저장소 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 기간(일반적으로 40초 미만) 동안 다시 만들 수 없습니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Delete the table and all its data if it exists.
    tableClient.deleteTableIfExists("people");

다음 단계
---------

이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   MSDN 참조: [Azure에 데이터 저장 및 액세스]
-   [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하십시오.

