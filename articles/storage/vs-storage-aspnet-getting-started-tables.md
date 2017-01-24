---
title: "Azure Table Storage 및 Visual Studio 연결 서비스 시작(ASP.NET) | Microsoft Docs"
description: "Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio ASP.NET 프로젝트에서 Azure Table Storage 사용을 시작하는 방법입니다."
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 07f827e13e5f01a373e69b90e8a5a0c72081acd0
ms.openlocfilehash: 24cfb3217dbadba3a086a8b0251efe5ab0173d1f


---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Azure Table Storage 및 Visual Studio 연결 서비스 시작
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>개요

Azure 테이블 저장소를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.

이 문서에서는 Azure Table Storage 엔터티를 프로그래밍 방식으로 관리하여 테이블 생성 및 삭제, 테이블 엔터티 작업 등의 일반적인 작업을 수행하는 방법을 설명합니다. 

> [!NOTE]
> 
> 이 문서의 코드 섹션에서는 연결 서비스를 사용하여 Azure Storage 계정에 이미 연결되어 있다고 가정합니다. 연결 서비스는 Visual Studio 솔루션 탐색기를 열고, 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가->연결 서비스** 옵션을 선택하여 구성됩니다. 여기에서 대화 상자 지침에 따라 원하는 Azure Storage 계정에 연결합니다.      

## <a name="create-a-table-in-code"></a>코드에서 테이블 만들기

다음 단계에서는 프로그래밍 방식으로 테이블을 만드는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다.

1. 다음 *using* 지시문을 추가합니다.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. (*<table-name>*을 만들려는 테이블의 이름으로 변경합니다.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 테이블이 아직 없으면 **CloudTable.CreateIfNotExists** 메서드를 호출하여 만듭니다.   
   
        table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

다음 단계에서는 프로그래밍 방식으로 테이블에 엔터티를 추가하는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다. 

1. 다음 *using* 지시문을 추가합니다.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. (*<table-name>*을 엔터티를 추가하려는 테이블의 이름으로 변경합니다.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 테이블에 엔터티를 추가하려면 **TableEntity**에서 파생된 클래스를 정의합니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 **CustomerEntity** 라는 엔터티 클래스를 정의합니다.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. 엔터티를 인스턴스화합니다.

        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.Email = "Walter@contoso.com";

7. customer 엔터티를 삽입하는 **TableOperation** 개체를 만듭니다.

        TableOperation insertOperation = TableOperation.Insert(customer1);

8. **CloudTable.Execute** 메서드를 호출하여 삽입 작업을 실행합니다. **TableResult.HttpStatusCode** 속성을 검사하여 작업의 결과를 확인할 수 있습니다. 상태 코드 2xx는 클라이언트에서 요청한 작업이 성공적으로 처리되었음을 나타냅니다. 예를 들어 새 엔터티를 성공적으로 삽입하면 작업이 성공적으로 처리되었고 서버가 어떤 콘텐츠도 반환하지 않았음을 의미하는 HTTP 상태 코드 204가 표시됩니다.

        TableResult result = table.Execute(insertOperation);

        // Inspect result.HttpStatusCode for success/failure.

## <a name="add-a-batch-of-entities-to-a-table"></a>테이블에 엔터티를 일괄로 추가

한 번에 하나의 테이블에 엔터티를 추가할 수 있을 뿐 아니라 엔터티를 일괄로 추가할 수도 있습니다. 이렇게 하면 코드와 Azure Table service 간의 왕복 수가 줄어듭니다. 다음 단계에서는 프로그래밍 방식을 사용하여 여러 엔터티를 단일 작업으로 추가하는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다.

1. 다음 *using* 지시문을 추가합니다.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. (*<table-name>*을 엔터티를 추가하려는 테이블의 이름으로 변경합니다.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 테이블에 엔터티를 추가하려면 **TableEntity**에서 파생된 클래스를 정의합니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 **CustomerEntity** 라는 엔터티 클래스를 정의합니다.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. 엔터티를 인스턴스화합니다.

        CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
        customer1.Email = "Jeff@contoso.com";
    
        CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
        customer2.Email = "Ben@contoso.com";

7. **TableBatchOperation** 개체를 가져옵니다.

        TableBatchOperation batchOperation = new TableBatchOperation();

8. 일괄 삽입 작업에 엔터티를 추가합니다.

        batchOperation.Insert(customer1);
        batchOperation.Insert(customer2);

9. **CloudTable.ExecuteBatch** 메서드를 호출하여 일괄 삽입 작업을 실행합니다. **CloudTable.ExecuteBatch** 메서드는 **TableResult** 개체 목록을 반환합니다. 목록에 있는 각 **TableResult** 개체의 **TableResult.HttpStatusCode** 속성을 검사하여 일괄 삽입 작업의 결과를 확인할 수 있습니다. 상태 코드 2xx는 클라이언트에서 요청한 작업이 성공적으로 처리되었음을 나타냅니다. 예를 들어 새 엔터티를 성공적으로 삽입하면 작업이 성공적으로 처리되었고 서버가 어떤 콘텐츠도 반환하지 않았음을 의미하는 HTTP 상태 코드 204가 표시됩니다.
    
        IList<TableResult> results = table.ExecuteBatch(batchOperation);

        // Inspect the HttpStatusCode property of each TableResult object
        // in the results list for success/failure.

## <a name="get-a-single-entity"></a>단일 엔터티 가져오기

다음 단계에서는 프로그래밍 방식으로 테이블에서 엔터티를 가져오는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다. 

> [!NOTE]
> 
> 이 섹션의 코드는 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 참조합니다. 

1. 다음 *using* 지시문을 추가합니다.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. (*<table-name>*을 엔터티를 추가하려는 테이블의 이름으로 변경합니다.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. **TableEntity**에서 파생된 엔터티 개체를 사용하는 검색 작업 개체를 만듭니다. 첫 번째 매개 변수는 *partitionKey*이고, 두 번째 매개 변수는 *rowKey*입니다. 다음 코드 조각은 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 사용하여 *partitionKey* 값 "Smith" 및 *rowKey* 값 "Ben"을 사용하여 테이블에서 **CustomerEntity** 엔터티를 쿼리합니다.  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. 검색 작업을 실행합니다.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. **TableOperation.HttpStatusCode** 속성을 검사하여 작업 결과를 확인합니다. 여기서 상태 코드 200은 클라이언트에서 요청한 작업이 성공적으로 처리되었음을 나타냅니다. (작업이 성공한 경우) 반환된 엔터티가 포함된 **TableResult.Result** 속성을 검사할 수도 있습니다.

        CustomerEntity customer = null;

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            // Process the customer entity.
            customer = retrievedResult.Result as CustomerEntity;
        }

## <a name="get-all-entities-in-a-partition"></a>파티션의 모든 엔터티 가져오기

다음 단계에서는 프로그래밍 방식으로 파티션의 모든 엔터티를 가져오는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다. 

> [!NOTE]
> 
> 이 섹션의 코드는 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 참조합니다. 

1. 다음 *using* 지시문을 추가합니다.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. (*<table-name>*을 엔터티를 추가하려는 테이블의 이름으로 변경합니다.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. **TableQuery** 개체를 인스턴스화하고 **Where** 절에 쿼리를 지정합니다. 다음 코드 조각은 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 사용하여 **PartitionKey** 값이 "Smith"인 모든 엔터티를 테이블에서 쿼리합니다.

        TableQuery<CustomerEntity> query = 
            new TableQuery<CustomerEntity>()
            .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

6. 루프 내에서 **CloudTable.ExecuteQuerySegmented** 메서드를 호출하고 이전 단계에서 인스턴스화한 쿼리 개체를 전달합니다.  **CloudTable.ExecuteQuerySegmented** 메서드는 **TableContinuationToken** 개체를 반환합니다. 이 개체는 **null**인 경우 검색할 추가 엔터티가 없음을 나타냅니다. 루프 내에서 다른 루프를 사용하여 반환된 엔터티를 반복합니다.

        TableContinuationToken token = null;
        do
        {
            TableQuerySegment<CustomerEntity>resultSegment = table.ExecuteQuerySegmented(query, token);
            token = resultSegment.ContinuationToken;

            foreach (CustomerEntity customer in resultSegment.Results)
            {
                // Process customer entity.
            }
        } while (token != null);

## <a name="delete-an-entity"></a>엔터티 삭제

다음 단계에서는 엔터티를 검색한 후 삭제하는 방법을 보여 줍니다.

1. 다음 *using* 지시문을 추가합니다.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Table service 클라이언트를 나타내는 **CloudTableClient** 개체를 가져옵니다.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 원하는 테이블 이름에 대한 참조를 나타내는 **CloudTable** 개체를 가져옵니다. (*<table-name>*을 엔터티를 추가하려는 테이블의 이름으로 변경합니다.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. **TableEntity**에서 파생된 엔터티 개체를 사용하는 검색 작업 개체를 만듭니다. 첫 번째 매개 변수는 *partitionKey*이고, 두 번째 매개 변수는 *rowKey*입니다. 다음 코드 조각은 **CustomerEntity** 클래스 및 [테이블에 엔터티를 일괄로 추가](#add-a-batch-of-entities-to-a-table) 섹션에 표시되는 데이터를 사용하여 *partitionKey* 값 "Smith" 및 *rowKey* 값 "Ben"을 사용하여 테이블에서 **CustomerEntity** 엔터티를 쿼리합니다.  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. 검색 작업을 실행합니다.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. **TableOperation.HttpStatusCode** 속성을 검사하여 작업 결과를 확인합니다. 여기서 상태 코드 200은 클라이언트에서 요청한 작업이 성공적으로 처리되었음을 나타냅니다. (작업이 성공한 경우) 반환된 엔터티가 포함된 **TableResult.Result** 속성을 검사할 수도 있습니다. 조건문 내에서 작업이 성공했는지 확인하려면 삭제 작업을 만들고(쿼리에서 반환된 엔터티 전달), 삭제 작업을 실행합니다.

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            CustomerEntity customer = retrievedResult.Result as CustomerEntity;

            // Create the delete operation.
            TableOperation deleteOperation = TableOperation.Delete(customer);

            // Execute the delete operation.
            table.Execute(deleteOperation);
        }

## <a name="next-steps"></a>다음 단계
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Dec16_HO2-->


