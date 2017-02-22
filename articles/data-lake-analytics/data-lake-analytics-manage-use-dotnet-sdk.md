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
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3ed1b4121e8e64b07abaeb1117f2b8a0cfd75406
ms.openlocfilehash: 7b2380e45c62684ed29fe819db7e254b968d55d0


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Azure .NET SDK를 사용하여 Azure Data Lake Analytics 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure .NET SDK를 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음 필수 조건이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

### <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기
아직 만들지 않은 경우에는 반드시 Azure 리소스 그룹을 만들어 Data Lake Analytics 구성 요소를 만들어야 합니다. 다음 코드는 리소스 그룹을 만드는 방법을 보여 줍니다.

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

자세한 내용은 [Azure 리소스 그룹 및 Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics)를 참조하세요.


## <a name="connect-to-azure-data-lake"></a>Azure Data Lake에 연결
다음 NuGet 패키지를 설치해야 합니다.

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


다음 코드 샘플의 Main 메서드에서는 Azure에 연결하고 Analytics 계정 및 Store 계정의 Data Lake 클라이언트 관리 개체를 초기화하는 방법을 보여 줍니다.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Data Lake 클라이언트 관리 개체
Azure Data Lake SDK에는 대부분의 프로그래밍 작업이 수행되고 다음 두 네임스페이스에 속한 클라이언트 관리 개체 집합이 있습니다.
* Mirosoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

다음 표에서는 이 문서 전체의 예제에서 사용하는 이러한 개체 및 해당 변수를 나열하고 있습니다.

| 클라이언트 관리 개체                  | 코드 변수         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 관리 클라이언트 개체
* DataLakeStoreAccountManagementClient - Data Lake Store를 만들고 관리하는 데 사용합니다.
* DataLakeFileSystemAccountManagementClient - 폴더와 파일을 만들고, 파일을 업로드하고 나열하며, ACL 및 자격 증명에 액세스하고, Azure Storage Blob에 대한 링크를 추가하는 것과 같은 파일 시스템 작업에 사용합니다.

Data Lake에서 Azure Storage에 대한 링크를 만들 수는 있지만 콘텐츠에 액세스할 수는 없습니다. 이렇게 하려면 Azure Storage SDK API를 사용해야 하지만 Azure Storage Blob에서 U-SQL 스크립트를 실행할 수 있습니다.

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 관리 클라이언트 개체
* DataLakeAnaylyticsAccountManagementClient - Data Lake Analytics 계정을 만들고 관리하는 데 사용합니다.
* DataLakeAnalyticsCatalogManagementClient - 목록 스키마를 포함하여 SQL 데이터베이스를 구성하는 데 사용합니다.
* DataLakeAnalyticsJobManagementClient - U-SQL 작업을 만들고 관리하는 데 사용합니다.

## <a name="create-accounts"></a>계정 만들기
데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. Azure HDInsight와 달리 작업을 실행하지 않는 경우 분석 계정에 대해 비용을 지불하지 않습니다.  작업이 실행되는 시간에 대해서만 비용을 지불합니다.  자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

또한 Data Lake Analytics 계정에는 Data Lake Store 계정이 하나 이상 필요합니다.
  
### <a name="create-a-data-lake-store-account"></a>Data Lake 저장소 계정 만들기
다음 코드에서는 Data Lake Store 계정을 만드는 방법을 보여 줍니다. Create 메서드를 사용하려면 먼저 위치를 지정하여 매개 변수를 정의해야 합니다.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기
다음 코드에서는 Data Lake Analytics 계정을 만드는 방법을 보여 줍니다. DataLakeAnalyticsAccountManagementClient 개체의 Create 메서드는 해당 매개 변수 중 하나에 대해 Data Lake Store 계정의 컬렉션을 사용합니다. 이 컬렉션은 DataLakeStoreAccountInfo 개체의 인스턴스로 채워져야 합니다. 이 예제에서 이러한 DataLakeStoreAccountInfo 개체는 도우미 메서드(AdlaFromAdlsStoreAccounts)에서 가져옵니다. 또한 구독에 있는 모든 Data Lake Store 계정이 반드시 단일 Data Lake Analytics 계정에 있어야 하는 것은 아니므로 다음 코드에서는 승인된 목록에서 이름을 확인합니다.

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>계정 관리

### <a name="list-data-lake-store-and-analytic-accounts"></a>Data Lake Store 및 분석 계정 나열
다음 코드에서는 구독에서 Data Lake Store 계정을 나열합니다. 목록 작업은 언제나 개체의 모든 속성을 제공하지는 않으며 어떤 경우에는 개체에 대해 Get 작업을 수행해야 합니다.
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>계정 가져오기
다음 코드에서는 Data Lake Analytics 계정이 있는 경우 DataLakeAnalyticsAccountManagementClient를 사용하여 해당 계정을 반환합니다. 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

마찬가지로 동일한 방법으로 DataLakeStoreAccountManagementClient(_adlsClient)를 사용하여 Data Lake Store 계정을 가져올 수 있습니다.        
### <a name="delete-an-account"></a>계정 삭제
다음 코드에서는 Data Lake Analytics 계정이 있는 경우 해당 계정을 삭제합니다. 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

또한 동일한 방법으로 DataLakeStoreAccountManagementClient를 사용하여 Data Lake Store 계정을 삭제할 수 있습니다.

### <a name="get-the-default-data-lake-store-account"></a>기본 Data Lake Store 계정 가져오기
모든 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 필요합니다. 다음 코드를 사용하여 Analytics 계정의 기본 Store 계정을 결정합니다.

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>데이터 원본 관리
데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

* [Azure 데이터 레이크 저장소](../data-lake-store/data-lake-store-overview.md)
* [Azure 저장소](../storage/storage-introduction.md)

분석 계정을 만들 때 Azure 데이터 레이크 저장소 계정이 기본 저장소 계정이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. Analytics 계정을 만든 후에는 추가 Data Lake Storage를 추가하고 Azure Storage 계정에 연결할 수 있습니다. 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Data Lake에 Azure Storage에 대한 링크 포함
Data Lake 환경에서 Azure Storage 블로그에 대한 링크를 만들 수 있습니다. 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Data Lake 데이터 원본 나열
다음 코드에서는 Data Lake Store 계정 및 지정한 Data Lake Analytics 계정의 Data Lake Storage 계정(Azure Storage용)을 나열합니다.

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Data Lake Store 계정에 파일 업로드
다음 코드에서는 DataLakeStoreFileSystemManagementClient를 사용하여 Data Lake Store 계정에 로컬 파일을 업로드합니다.

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Data Lake Store 계정에 파일 만들기
파일 업로드 외에도 분석을 위해 Data Lake Store 계정에 파일을 프로그래밍 방식으로 쉽게 만들 수 있습니다. 다음 코드에서는 100개 임의 바이트 배열 중 처음 4개 값을 .csv 파일에 씁니다.

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

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Data Lake Store 계정에서 파일 복사
다음 코드에서는 DataLakeFileSystemAccountManagementClient 개체를 사용하는 파일 시스템 작업을 보여 줍니다. Samples/Data/AmbulanceData 디렉터리의 스프레드시트(.csv) 파일을 컴퓨터의 로컬 디렉터리에 복사합니다.

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Azure Storage 컨테이너 나열
다음 코드에서는 지정한 Azure Storage 계정의 컨테이너를 나열합니다.

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Azure Storage 계정 경로 확인
다음 코드에서는 Azure Storage 계정(storageAccntName)이 Data Lake Analytics 계정(analyticsAccountName)에 있는지 및 컨테이너(containerName)가 Azure Storage 계정에 있는지를 확인합니다. 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>카탈로그 및 작업 관리
DataLakeAnalyticsCatalogManagementClient 개체는 각 Azure Data Lake Store에 제공된 SQL 데이터베이스를 관리하는 메서드를 제공합니다. DataLakeAnalyticsJobManagementClient는 U-SQL 스크립트를 사용하여 데이터베이스에서 실행되는 작업을 제출하고 관리하는 메서드를 제공합니다.

### <a name="list-databases-and-schemas"></a>데이터베이스 및 스키마 나열
나열할 수 있는 항목들 중에서 가장 일반적인 항목이 데이터베이스와 해당 스키마입니다. 다음 코드에서는 데이터베이스 컬렉션을 가져온 다음 각 데이터베이스에 대한 스키마를 열거합니다.

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
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

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>실패한 작업 나열
다음 코드에서는 실패한 작업에 대한 정보를 나열합니다.

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>U-SQL 스크립트의 Azure Storage 참조
다음 코드는 U-SQL 스크립트의 시작 부분입니다. 이 스크립트는 Data Lake Store 계정의 파일("/Samples/Data/SearchLog.tsv")에서 데이터를 읽도록 지정하고 있습니다.

    @searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

연결된 Azure Storage 계정의 Blob에서 데이터를 읽으려면 Blob에 대한 전체 URL을 다음 형식으로 사용해야 합니다.

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

예를 들어 원본 파일(SearchLog.tsv)이 "contso_33" 저장소 계정의 "samples"라는 이름의 Blob 컨테이너에 저장되어 있는 경우 FROM 문의 경로는 다음과 같습니다.

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Azure 리소스 그룹 및 Data Lake Analytics
응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. Azure Resource Manager를 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 

데이터 레이크 분석 서비스는 다음 구성 요소를 포함할 수 있습니다.

* Azure 데이터 레이크 분석 계정
* 필수 기본 Azure 데이터 레이크 저장소 계정
* 하나 이상의 Azure Data Lake Analytics 계정
* 하나 이상의 Azure Data Lake Store 계정(적어도 하나는 필요)
* 추가 연결된 Azure Data Lake Storage 계정
* 추가 Azure 저장소 계정

이러한 모든 구성을 쉽게 관리할 수 있도록 하나의 리소스 관리 그룹 아래 만들 수 있습니다.

![Azure 데이터 레이크 분석 계정 및 저장소](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

데이터 레이크 분석 계정 및 종속 저장소 계정은 동일한 Azure 데이터 센터에 있어야 합니다.
그러나 리소스 관리 그룹은 다른 데이터 센터에 있을 수 있습니다.  

## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure Portal을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Feb17_HO1-->


