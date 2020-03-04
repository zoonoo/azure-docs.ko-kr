---
title: 을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기C#
description: 이 문서에서는를 사용 C#하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결을 만드는 방법에 대해 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255073"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기C#

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-grid-resource-manager.md)


Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure 데이터 탐색기는 blob 컨테이너에 기록 된 Event Hubs, IoT Hub 및 blob에서 수집 (데이터 로드)을 제공 합니다. 이 문서에서는를 사용 C#하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019가 설치 되어 있지 않으면 **무료** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드 하 여 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-csharp.md) 만들기
* [테이블 및 열 매핑](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) 만들기
* [데이터베이스 및 테이블 정책](database-table-policies-csharp.md) 설정 (선택 사항)
* [Event Grid 구독을 사용 하 여 저장소 계정을](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)만듭니다.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Event Grid 데이터 연결 추가

다음 예제에서는 프로그래밍 방식으로 Event Grid 데이터 연결을 추가 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여 Event Grid 데이터 연결을 추가 하려면 [Azure 데이터 탐색기에서 Event Grid 데이터 연결 만들기](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) 를 참조 하세요.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용 하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | *xxxxxxxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 암호입니다. |
| resourceGroupName | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| dataConnectionName | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| tableName | *StormEvents* | 대상 데이터베이스에 있는 대상 테이블의 이름입니다.|
| mappingRuleName | *StormEvents_CSV_Mapping* | 대상 테이블과 관련 된 열 매핑의 이름입니다.|
| dataFormat | *csv* | 메시지의 데이터 형식입니다.|
| eventHubResourceId | *리소스 ID* | 이벤트를 보내도록 Event Grid 구성 된 이벤트 허브의 리소스 ID입니다. |
| storageAccountResourceId | *리소스 ID* | 수집할 데이터를 보유 하는 저장소 계정의 리소스 ID입니다. |
| consumerGroup | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

## <a name="generate-sample-data"></a>샘플 데이터 생성

Azure Data Explorer와 스토리지 계정이 연결되었으면 샘플 데이터를 만들어서 Blob Storage에 업로드할 수 있습니다.

이 스크립트는 스토리지 계정에 새 컨테이너를 만들고, 해당 컨테이너에 기존 파일(Blob)을 업로드한 다음, 컨테이너의 Blob을 나열합니다.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure 데이터 탐색기는 blob 사후 수집을 삭제 하지 않습니다. Blob 삭제를 관리 하려면 [Azure blob 저장소 수명 주기](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 하 여 3 ~ 5 일간 blob을 유지 합니다.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
