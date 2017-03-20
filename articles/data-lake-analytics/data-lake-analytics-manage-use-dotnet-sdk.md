---
title: "Azure .NET SDK를 사용하여 Azure Data Lake Analytics 관리 | Microsoft Docs"
description: "데이터 레이크 분석 작업, 데이터 원본, 사용자를 관리하는 방법에 대해 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Azure .NET SDK를 사용하여 Azure Data Lake Analytics 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure .NET SDK를 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.

## <a name="prerequisites"></a>필수 조건

* **Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual Studio 2012와 Visual C++ 설치**.
* **.NET 버전 2.5 이상용 Microsoft Azure SDK**.  [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
* **필요한 Nuget 패키지**

### <a name="install-nuget-packages"></a>Nuget 패키지 설치
   
   1. Visual Studio의 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   2. **Nuget 패키지 관리자** 탭에서 **패키지 원본**이 **nuget.org**로 설정되어 있고 **시험판 포함** 확인란이 선택되어 있는지 확인합니다.

   3. 다음 NuGet 패키지를 검색하고 설치합니다.

    - Microsoft.Rest.ClientRuntime.Azure.Authentication - 이 자습서에서는 V2.2.12 사용
    - Microsoft.Azure.Management.DataLake.Analytics - 이 자습서에서는 V2.1.0 미리 보기를 사용함
    - Microsoft.Azure.Management.DataLake.Store - 이 자습서에서는 V2.1.0 미리 보기 사용

   4. **NuGet 패키지 관리자**를 닫습니다.

## <a name="client-management-objects"></a>클라이언트 관리 개체
Azure Data Lake Analytics 및 Azure Data Lake Store API에는 대부분의 프로그래밍 작업이 수행되는 클라이언트 관리 개체 집합이 있습니다. 이러한 개체가 있는 두 개의 네임스페이스는 다음과 같습니다.
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

다음 표는 클라이언트 관리 개체와 이 문서의 해당 코드 예제에 사용된 변수를 보여 줍니다.

| 클라이언트 관리 개체                  | 코드 변수        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 관리 클라이언트 개체
* DataLakeStoreAccountManagementClient - Data Lake Store 계정을 만들고 관리하는 데 사용합니다.
* DataLakeFileSystemAccountManagementClient - 폴더와 파일을 만들고, 파일을 업로드하고 나열하며, ACL 및 자격 증명에 액세스하고, Azure Storage Blob에 대한 링크를 추가하는 것과 같은 파일 시스템 작업에 사용합니다.

Data Lake에서 Azure Storage에 대한 링크를 만들 수는 있지만 콘텐츠에 액세스할 수는 없습니다. 이렇게 하려면 Azure Storage SDK API를 사용해야 하지만 Azure Storage Blob에서 U-SQL 스크립트를 실행할 수 있습니다.

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 관리 클라이언트 개체
* DataLakeAnalyticsAccountManagementClient - Data Lake Analytics 계정을 만들고 관리하는 데 사용합니다.
* DataLakeAnalyticsCatalogManagementClient - Data Lake Analytics에서 카탈로그 항목을 탐색하는 데 사용합니다.
* DataLakeAnalyticsJobManagementClient - Data Lake Analytics에서 작업을 제출하고 관리합니다.

### <a name="example"></a>예

이 문서의 다음 부분에 설명된 기본 인증 방법을 통해 얻은 자격 증명(creds)을 사용하여 클라이언트 관리 개체를 초기화합니다. 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>인증 및 Azure Data Lake Analytics에 연결
Azure Data Lake Analytics에 로그온하는 옵션은 여러 가지가 있습니다.

### <a name="interactive-with-provided-credentials"></a>제공된 자격 증명을 사용하여 대화형으로
다음 코드 조각은 사용자가 사용자 이름 및 암호 또는 PIN 번호 등의 자격 증명을 제공하는 가장 쉬운 인증을 보여 줍니다.

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

Azure Active Directory 테넌트에서 고유한 응용 프로그램 및 서비스 주체를 만든 다음 여기에 사용된 샘플 ID가 아니라 해당 응용 프로그램의 클라이언트 ID를 사용하는 것이 좋습니다.

### <a name="non-interactive-with-a-client-secret"></a>클라이언트 암호를 사용하여 비대화형으로
다음 코드 조각은 응용 프로그램/서비스 주체에 대한 클라이언트 암호/키를 사용하여 비대화형으로 응용 프로그램을 인증하는 데 사용될 수 있습니다. 이 인증 옵션은 기존 [Azure AD "웹앱" 응용 프로그램](../azure-resource-manager/resource-group-create-service-principal-portal.md)과 함께 사용합니다.

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>서비스 주체를 사용하여 비대화형으로
세 번째 옵션으로 다음 코드 조각은 응용 프로그램/서비스 보안 주체에 대한 인증서를 사용하여 비대화형으로 응용 프로그램을 인증하는 데 사용될 수 있습니다. 이 인증 옵션은 기존 [Azure AD "웹앱" 응용 프로그램](../azure-resource-manager/resource-group-create-service-principal-portal.md)과 함께 사용합니다.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>계정 만들기
데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. 또한 Data Lake Analytics 계정에는 Data Lake Store 계정이 하나 이상 필요합니다. 자세한 내용은 [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)를 참조하세요.

### <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기
아직 만들지 않은 경우에는 반드시 Azure 리소스 그룹을 만들어 Data Lake Analytics 구성 요소를 만들어야 합니다. 인증 자격 증명, 구독 ID 및 위치가 필요합니다. 다음 코드는 리소스 그룹을 만드는 방법을 보여 줍니다.

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

자세한 내용은 [Azure 리소스 그룹 및 Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics)를 참조하세요.


### <a name="create-a-data-lake-store-account"></a>Data Lake 저장소 계정 만들기
다음 코드에서는 Data Lake Store 계정을 만드는 방법을 보여 줍니다. Create 메서드를 사용하려면 먼저 위치를 지정하여 매개 변수를 정의해야 합니다.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기
다음 코드는 비동기 메서드를 사용하여 Data Lake Analytics 계정을 만드는 방법을 보여 줍니다. CreateAsync 메서드는 해당 매개 변수 중 하나로 Data Lake Store 계정 컬렉션을 사용합니다. 이 컬렉션은 DataLakeStoreAccountInfo 개체의 인스턴스로 채워져야 합니다. 이 예제에서 이러한 DataLakeStoreAccountInfo 개체는 도우미 메서드(AdlaFromAdlsStoreAccounts)에서 가져옵니다.

모든 Data Lake Analytics 계정에 대해 필요한 분석을 수행하는 데 사용해야 하는 Data Lake Store 계정만 포함하면 됩니다. 이러한 Data Lake Store 계정 중 하나는 기본 Data Lake Store 계정이어야 합니다.

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>계정 관리

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Data Lake Store 및 Data Lake Analytics 계정 나열
다음 코드는 구독에 Data Lake Store 계정을 나열합니다. 목록 작업은 언제나 개체의 모든 속성을 제공하지는 않으며 어떤 경우에는 개체에 대해 Get 작업을 수행해야 합니다.

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>계정 가져오기
다음 코드에서는 DataLakeAnalyticsAccountManagementClient를 사용하여 Data Lake Analytics 계정을 가져옵니다. 먼저 계정이 있는지 확인합니다.

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

마찬가지로 동일한 방법으로 DataLakeStoreAccountManagementClient(adlsClient)를 사용하여 Data Lake Store 계정을 가져올 수 있습니다.

### <a name="delete-an-account"></a>계정 삭제
다음 코드에서는 Data Lake Analytics 계정이 있는 경우 해당 계정을 삭제합니다.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

또한 동일한 방법으로 DataLakeStoreAccountManagementClient를 사용하여 Data Lake Store 계정을 삭제할 수 있습니다.

### <a name="get-the-default-data-lake-store-account"></a>기본 Data Lake Store 계정 가져오기
모든 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 필요합니다. 다음 코드를 사용하여 Analytics 계정의 기본 Store 계정을 결정합니다.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>데이터 원본 관리
데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

* [Azure 데이터 레이크 저장소](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage 계정](../storage/storage-introduction.md)

Analytics 계정을 만들 때 Azure Data Lake Store 계정이 기본 Data Lake Store 계정이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. Analytics 계정을 만든 후에는 추가 Data Lake Store를 추가하고 Azure Storage(BLOB) 계정에 연결할 수 있습니다.

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Data Lake Analytics 계정에서 Azure Storage 계정에 연결
Azure Storage 계정에 대한 링크를 만들 수 있습니다.

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Data Lake Store 데이터 원본 나열
다음 코드는 지정한 Data Lake Analytics 계정에 사용되는 Data Lake Store 계정 및 Azure Storage 계정을 나열합니다.

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>폴더 및 파일 업로드 및 다운로드
Data Lake Store 파일 시스템 클라이언트 관리 개체를 사용하여 다음 메서드를 통해 Azure에서 로컬 컴퓨터로 개별 파일 또는 폴더를 업로드하고 다운로드할 수 있습니다.

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

이러한 메서드의 첫 번째 매개 변수는 원본 경로 및 대상 경로 매개 변수가 뒤에 나오는 Data Lake Store 계정 이름입니다.

다음 예제는 Data Lake Store에서 폴더를 다운로드하는 방법을 보여 줍니다.


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Data Lake Store 계정에 파일 만들기
.NET Framework IO 작업을 사용하여 Data Lake Store에 파일 콘텐츠를 만들 수 있습니다. 다음 코드에서는 100개 임의 바이트 배열 중 처음 4개 값을 .csv 파일에 씁니다.

    MemoryStream azMem = new MemoryStream();
    StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

    for (int i = 0; i < 100; i++)
    {
        byte[] gA = Guid.NewGuid().ToByteArray();
        string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
        sw.WriteLine(dataLine);
    }
    sw.Flush();
    azMem.Position = 0;

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Azure Storage 계정의 BLOB 컨테이너 나열
다음 코드에서는 지정한 Azure Storage 계정의 컨테이너를 나열합니다.

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Azure Storage 계정 경로 확인
다음 코드에서는 Azure Storage 계정(storageAccntName)이 Data Lake Analytics 계정(analyticsAccountName)에 있는지 및 컨테이너(containerName)가 Azure Storage 계정에 있는지를 확인합니다.

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>카탈로그 및 작업 관리
DataLakeAnalyticsCatalogManagementClient 개체는 각 Azure Data Lake Store에 제공된 SQL 데이터베이스를 관리하는 메서드를 제공합니다. DataLakeAnalyticsJobManagementClient는 U-SQL 스크립트를 사용하여 데이터베이스에서 실행되는 작업을 제출하고 관리하는 메서드를 제공합니다.

### <a name="list-databases-and-schemas"></a>데이터베이스 및 스키마 나열
나열할 수 있는 항목들 중에서 가장 일반적인 항목이 데이터베이스와 해당 스키마입니다. 다음 코드에서는 데이터베이스 컬렉션을 가져온 다음 각 데이터베이스에 대한 스키마를 열거합니다.

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
        }
    }

기본 master 데이터베이스에서 실행하면 이 예제의 출력은 다음과 같습니다.

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>테이블 열 나열
다음 코드에서는 Data Lake Analytics 카탈로그 관리 클라이언트를 통해 데이터베이스에 액세스하여 지정한 테이블의 열을 나열하는 방법을 보여 줍니다.

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>실패한 작업 나열
다음 코드에서는 실패한 작업에 대한 정보를 나열합니다.

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure Portal을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

