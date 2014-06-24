<properties  linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage  from .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use the Table Storage Service" authors="tamram" />
# .NET에서 테이블 저장소 서비스를 사용하는 방법

이 가이드에서는 Azure 테이블 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C# 코드로
작성되었으며 Azure Storage Client Library for .NET을 사용합니다. **테이블 만들기 및 삭제**,
**테이블 엔터티 작업** 등의 시나리오를 다룹니다. 테이블에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을
참조하십시오.
## 목차

* [테이블 서비스 정의](#what-is)
* [개념](#concepts)
* [Azure 저장소 계정 만들기](#create-account)
* [저장소 연결 문자열 설정](#setup-connection-string)
* [방법: 프로그래밍 방식으로 테이블 저장소 액세스](#configure-access)
* [방법: 테이블 만들기](#create-table)
* [방법: 테이블에 엔터티 추가](#add-entity)
* [방법: 엔터티 일괄 삽입](#insert-batch)
* [방법: 파티션의 모든 엔터티 검색](#retrieve-all-entities)
* [방법: 파티션의 엔터티 범위 검색](#retrieve-range-entities)
* [방법: 단일 엔터티 검색](#retrieve-single-entity)
* [방법: 엔터티 바꾸기](#replace-entity)
* [방법: 엔터티 삽입 또는 바꾸기](#insert-or-replace-entity)
* [방법: 엔터티 속성 하위 집합 쿼리](#query-entity-properties)
* [방법: 엔터티 삭제](#delete-entity)
* [방법: 테이블 삭제](#delete-table)
* [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">계정 만들기</span>Azure 저장소 계정 만들기</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">연결 문자열 설정</span>저장소 연결 문자열 설정</h2>


Azure Storage Client Library for .NET은 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기
위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 코드에서 하드 코딩하는 대신 구성 파일에 저장소 연결 문자열을 배치할 수
있습니다.

* Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을
  사용하여 연결 문자열을 저장하는 것이 좋습니다.
* Azure 웹 사이트 또는 Azure 가상 컴퓨터를 사용하거나 Azure 외부에서 실행하려는 .NET 응용 프로그램을 빌드하는
  경우 .NET 구성 시스템(예: `web.config` 또는 `app.config` 파일)을 사용하여 연결 문자열을 저장하는
  것이 좋습니다.

두 경우 모두, 이 가이드의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를
사용하여 연결 문자열을 검색할 수 있습니다.
### 클라우드 서비스를 사용할 때 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리
포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나
    작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.  
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-20/blob5.png)

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다.  
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-20/blob6.png)
    
    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다.  
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-20/blob7.png)

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화
    상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Windows Azure 저장소)를 대상으로 지정할지 또는 클라우드의 실제
    저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 하나로 작동합니다. 앞에서 Azure에 만든
    저장소 계정에 Blob 데이터를 저장하려면 이 자습서의 이전 단계에서 복사한 **기본 액세스 키** 값을 입력합니다.   
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-20/blob8.png)

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 "친근한" 이름으로
    변경합니다. 나중에 이 가이드의 코드에서 이 연결 문자열을 참조합니다.  
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-20/blob9.png)
### .NET 구성을 사용하여 연결 문자열 구성

Azure 클라우드 서비스가 아닌 응용 프로그램을 작성하는 경우(이전 섹션 참조) .NET 구성 시스템(예:
`web.config` 또는 `app.config`)을 사용하는 것이 좋습니다. 여기에는 Azure 웹 사이트 또는 Azure
가상 컴퓨터와 Azure 외부에서 실행되도록 설계된 응용 프로그램이 포함됩니다. 다음과 같이 `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

    	<configuration>
  			<appSettings>
    			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  			</appSettings>
		</configuration>

 저장소 연결 문자열에 대한 자세한 내용은 [연결 문자열 구성][1]을 참조하십시오.

 이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

 <h2> <a name="configure-access"> </a><span  class="short-header">프로그래밍 방식으로 액세스</span>방법: 프로그래밍 방식으로 테이블 저장소 액세스</h2>


 <h3>어셈블리 가져오기</h3>


 NuGet을 사용하여 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져올 수 있습니다. **솔루션
탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서
"WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

 `Microsoft.WindowsAzure.Storage.dll`은 [.NET 개발자 센터][2](영문)에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다.
어셈블리는 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` 디렉터리에 설치됩니다.

 <h3>네임스페이스 선언</h3>


 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

 <h3>연결 문자열 검색</h3>


 **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Windows Azure
프로젝트 템플릿을 사용 중이며 Microsoft.WindowsAzure.CloudConfigurationManager
네임스페이스에 대한 참조가 있는 경우 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스
구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는
중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우
**ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다.
System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 <h3>ODataLib 종속성</h3>


 Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을
통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다. ODataLib 라이브러리를 직접 다운로드하거나
NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 특정 ODataLib 패키지는 [OData][3], [Edm][4] 및
[Spatial][5]입니다.

 <h2><a name="create-table"></a><span  class="short-header">테이블 만들기</span>방법: 테이블 만들기</h2>


 **CloudTableClient** 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는
**CloudTableClient** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다. 이 가이드의 모든 코드에서는
빌드되는 응용 프로그램이 Azure 클라우드 서비스 프로젝트이며 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결
문자열을 사용한다고 가정합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // 테이블이 없는 경우 새로 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

 <h2><a name="add-entity"></a><span  class="short-header">테이블에 엔터티 추가</span>방법: 테이블에 엔터티 추가</h2>


 엔터티는 **TableEntity**에서 파생된 사용자 지정 클래스를 사용하여 C# 개체에 매핑됩니다. 테이블에 엔터티를
추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는
엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진
엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있지만 다양한 파티션 키를 사용하면 병렬 작업 확장성이 커집니다.
테이블 서비스에 저장해야 하는 속성의 경우 `get` 및 `set`를 통해 표시되는 지원되는 유형의 공용 속성이어야 합니다. 또한
엔터티 형식은 *반드시* 매개 변수가 없는 생성자를 표시해야 합니다.

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

 엔터티와 관련된 테이블 작업은 "방법: 테이블 만들기"에서 만든 **CloudTable** 개체를 사용하여 수행됩니다. 수행할
작업은 **TableOperation** 개체로 표시됩니다. 다음 코드 예제에서는 **CloudTable** 개체 생성과
**CustomerEntity** 개체를 보여 줍니다. 작업을 준비하기 위해 고객 엔터티를 테이블에 삽입하는
**TableOperation**이 만들어집니다. 마지막으로, **CloudTable.Execute**를 호출하여 작업이
실행됩니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 새 고객 엔터티를 만듭니다.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";
    
    // 고객 엔터티를 삽입하는 TableOperation을 만듭니다.
    TableOperation insertOperation = TableOperation.Insert(customer1);
    
    // 삽입 작업을 실행합니다.
    table.Execute(insertOperation);

 <h2><a name="insert-batch"></a><span  class="short-header">엔터티 일괄 삽입</span>방법: 엔터티 일괄 삽입</h2>


 하나의 쓰기 작업으로 테이블에 엔터티를 일괄 삽입할 수 있습니다. 일괄 작업에 대한 다른 참고 사항은 다음과 같습니다.

 1.  동일한 단일 일괄 작업에서 업데이트, 삭제 및 삽입을 수행할 수 있습니다.
2.  단일 일괄 작업에 최대 100개의 엔터티가 포함될 수 있습니다.
3.  단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
4.  쿼리를 일괄 작업으로 수행할 수 있지만 일괄 작업의 유일한 작업이어야 합니다.

 <!-- -->
 다음 코드 예제에서는 두 개의 엔터티 개체를 만들고 **Insert** 메서드를 사용하여
**TableBatchOperation**에 각 개체를 추가합니다. 그런 다음 **CloudTable.Execute**가 호출되어
작업을 실행합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 일괄 작업을 만듭니다.
    TableBatchOperation batchOperation = new TableBatchOperation();
    
    // 고객 엔터티를 만들고 테이블에 추가합니다.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";
            
    // 다른 고객 엔터티를 만들고 테이블에 추가합니다.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
            
    // 일괄 삽입 작업에 두 개의 고객 엔터티를 모두 추가합니다.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    
    // 일괄 작업을 실행합니다.
    table.ExecuteBatch(batchOperation);

 <h2><a name="retrieve-all-entities"></a><span  class="short-header">모든 엔터티 검색</span>방법: 파티션의 모든 엔터티 검색</h2>


 테이블에서 파티션의 모든 엔터티를 쿼리하려면 **TableQuery** 개체를 사용합니다. 다음 코드 예제에서는
\'Smith\'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에
출력합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // PartitionKey="Smith"인 모든 고객 엔터티에 대한 쿼리 작업을 생성합니다.
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    
    // 각 고객에 대한 필드를 출력합니다.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

 <h2><a name="retrieve-range-entities"></a><span  class="short-header">엔터티 범위 검색</span>방법: 파티션의 엔터티 범위 검색</h2>


 파티션의 모든 엔터티를 쿼리하지 않으려면 파티션 키 필터를 행 키 필터와 결합하여 범위를 지정할 수 있습니다. 다음 코드
예제에서는 두 개의 필터를 사용하여 행 키(이름)가 알파벳에서 \'E\'보다 앞에 오는 문자로 시작하는 \'Smith\' 파티션의
모든 엔터티를 가져온 다음 쿼리 결과를 출력합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 테이블 쿼리를 만듭니다.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));
    
    // 결과를 반복하고 엔터티 정보를 표시합니다.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

 <h2><a name="retrieve-single-entity"></a><span  class="short-header">단일 엔터티 검색</span>방법: 단일 엔터티 검색</h2>


 단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **TableOperation**을 사용하여 고객
\'Ben Smith\'를 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며,
**TableResult.Result**에서 반환된 값은 **CustomerEntity**입니다. 쿼리에 파티션과 행 키를 모두
지정하는 것이 테이블 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 고객 엔터티를 가져오는 검색 작업을 만듭니다.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // 검색 작업을 실행합니다.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // 결과의 전화 번호를 출력합니다.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

 <h2><a name="replace-entity"></a><span  class="short-header">엔터티 바꾸기</span>방법: 엔터티 바꾸기</h2>


 엔터티를 업데이트하려면 테이블 서비스에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 테이블 서비스에 저장합니다. 다음
코드에서는 기존 고객의 전화 번호를 변경합니다. **Insert**를 호출하는 대신 이 코드에서는 **Replace**를
사용합니다. 이렇게 하면 서버의 엔터티가 검색된 후 변경되어 작업이 실패하는 경우를 제외하고 서버에서 엔터티가 완전히 바뀝니다.
이러한 실패는 응용 프로그램이 검색 및 업데이트 사이에 다른 응용 프로그램 구성 요소에 의해 변경된 내용을 실수로 덮어쓰는 것을
방지합니다. 이 실패를 올바르게 처리하려면 엔터티를 다시 검색하고 변경한 다음(유효한 경우) 다른 **Replace** 작업을
수행합니다. 다음 섹션에서는 이 동작을 재정의하는 방법을 보여 줍니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 고객 엔터티를 가져오는 검색 작업을 만듭니다.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // 작업을 실행합니다.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // 결과를 CustomerEntity 개체에 할당합니다.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
    
    if (updateEntity != null)
    {
       // 전화 번호를 변경합니다.
       updateEntity.PhoneNumber = "425-555-0105";
    
       // InsertOrReplace TableOperation을 만듭니다.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);
    
       // 작업을 실행합니다.
       table.Execute(updateOperation);
    
       Console.WriteLine("Entity updated.");
    }
    
    else
       Console.WriteLine("Entity could not be retrieved.");

 <h2><a name="insert-or-replace-entity"></a><span  class="short-header">엔터티 삽입 또는 바꾸기</span>방법: 엔터티 삽입 또는 바꾸기</h2>


 엔터티가 서버에서 검색된 이후 변경된 경우 **Replace** 작업이 실패합니다. 또한 **Replace**가 성공하려면
서버에서 엔터티를 검색해야 합니다. 그러나 서버에 엔터티가 있는지 알지 못하며 저장된 현재 값이 부적절하여 업데이트로 모두
덮어써야 하는 경우도 있습니다. 이렇게 하려면 **InsertOrReplace** 작업을 사용합니다. 이 작업은 마지막 업데이트가
수행된 시기에 관계없이 엔터티가 없는 경우 삽입하고, 엔터티가 있는 경우 바꿉니다. 다음 코드 예제에서는 Ben Smith에 대한
고객 엔터티가 여전히 검색되지만 **InsertOrReplace**를 사용하여 서버에 다시 저장됩니다. 검색 및 업데이트 작업
사이의 모든 엔터티 업데이트를 덮어씁니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable 개체를 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 고객 엔터티를 가져오는 검색 작업을 만듭니다.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // 작업을 실행합니다.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // 결과를 CustomerEntity 개체에 할당합니다.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
    
    if (updateEntity != null)
    {
       // 전화 번호를 변경합니다.
       updateEntity.PhoneNumber = "425-555-1234";
    
       // InsertOrReplace TableOperation을 만듭니다.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);
    
       // 작업을 실행합니다.
       table.Execute(insertOrReplaceOperation);
    
       Console.WriteLine("Entity was updated.");
    }
    
    else
       Console.WriteLine("Entity could not be retrieved.");

 <h2><a name="query-entity-properties"></a><span  class="short-header">속성 하위 집합 쿼리</span>방법: 엔터티 속성 하위 집합 쿼리</h2>


 테이블 쿼리는 모든 엔터티 속성 대신 엔터티에서 몇 개의 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을
줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 다음 코드의 쿼리는 테이블에 있는 엔터티의 전자 메일 주소만
반환합니다. 이 작업을 위해 **DynamicTableEntity**의 쿼리와 **EntityResolver**를 사용합니다. 이
[블로그 게시글][6]에서 프로젝션에 대해 자세히 알아볼 수 있습니다. 로컬 저장소 에뮬레이터에서는 프로젝션이 지원되지 않으므로
이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable을 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 쿼리를 정의하고 Email 속성만 선택합니다.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });
    
    // 검색 후 엔터티 작업을 수행할 엔터티 확인 프로그램을 정의합니다.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;
    
    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

 <h2><a name="delete-entity"></a><span  class="short-header">엔터티 삭제</span>방법: 엔터티 삭제</h2>


 엔터티를 검색한 후 엔터티 업데이트를 위해 표시된 것과 동일한 패턴을 사용하여 엔터티를 쉽게 삭제할 수 있습니다. 다음 코드에서는
고객 엔터티를 검색하고 삭제합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable을 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 고객 엔터티를 가져오는 검색 작업을 만듭니다.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // 작업을 실행합니다.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // 결과를 CustomerEntity에 할당합니다.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
    
    // Delete TableOperation을 만듭니다.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    
       // 작업을 실행합니다.
       table.Execute(deleteOperation);
    
       Console.WriteLine("Entity deleted.");
    }
    
    else
       Console.WriteLine("Could not retrieve the entity.");

 <h2><a name="delete-table"></a><span  class="short-header">테이블 삭제</span>방법: 테이블 삭제</h2>


 마지막으로, 다음 코드 예제에서는 저장소 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 기간 동안 다시 만들 수
없습니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 테이블 클라이언트를 만듭니다.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // "people" 테이블을 나타내는 CloudTable을 만듭니다.
    CloudTable table = tableClient.GetTableReference("people");
    
    // 테이블이 있는 경우 삭제합니다.
    table.DeleteIfExists();

 <h2><a name="next-steps"></a><span  class="short-header">다음 단계</span>다음 단계</h2>


 이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

 *  사용 가능한 API에 대한 자세한 내용은 Blob 서비스 참조 설명서를 참조하십시오.
  *  [Storage Client Library for .NET 참조][7]
  *  [REST API 참조][8]
  

*  Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스][9]를 참조하십시오.
*  Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
  *  [Blob 저장소](/en-us/develop/net/how-to-guides/blob-storage/)를 사용하여 구조화되지 않은 데이터를 저장합니다.
  *  [SQL 데이터베이스](/en-us/develop/net/how-to-guides/sql-database/)를 사용하여 관계형 데이터를 저장합니다.
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
