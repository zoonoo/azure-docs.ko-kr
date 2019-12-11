---
title: Data Factory-.NET API 변경 로그
description: Azure Data Factory에 대 한 특정 버전의 .NET API에 대 한 주요 변경 내용, 기능 추가 내용, 버그 수정 등을 설명 합니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979005"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API 변경 로그
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

이 문서에서는 특정 버전의 Azure Data Factory SDK 변경 내용에 대 한 정보를 제공 합니다. [여기](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) Azure Data Factory에 대 한 최신 NuGet 패키지를 찾을 수 있습니다.

## <a name="version-4110"></a>버전 4.11.0
추가 기능:

* 다음 연결 된 서비스 유형이 추가 되었습니다.
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* 다음 데이터 집합 형식이 추가 되었습니다.
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* 다음 복사본 원본 유형이 추가 되었습니다.
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>버전 4.10.0
* 다음 선택적 속성이 TextFormat에 추가 되었습니다.
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* 다음 연결 된 서비스 유형이 추가 되었습니다.
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* 다음 데이터 집합 형식이 추가 되었습니다.
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* 다음 복사본 원본 유형이 추가 되었습니다.
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* AzureMLBatchExecutionActivity에 [webserviceinputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) 속성 추가
  * 여러 웹 서비스 입력을 Azure Machine Learning 실험에 전달할 수 있도록 설정

## <a name="version-491"></a>버전 4.9.1
### <a name="bug-fix"></a>버그 수정
* [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)에 대 한 사용 중단 WebApi 기반 인증

## <a name="version-490"></a>버전 4.9.0
### <a name="feature-additions"></a>기능 추가
* CopyActivity에 [Enablestaging](https://msdn.microsoft.com/library/mt767916.aspx) 및 [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) 속성을 추가 합니다. 기능에 대 한 자세한 내용은 [준비 된 복사](data-factory-copy-activity-performance.md#staged-copy) 를 참조 하세요.

### <a name="bug-fix"></a>버그 수정
* [Activitywindowsbyactivitylistparameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) 인스턴스를 사용 하는 [Activitywindowoperationextensions](https://msdn.microsoft.com/library/mt767915.aspx) 의 오버 로드를 소개 합니다.
* 가 나 Ysink에서 [Writebatchsize](https://msdn.microsoft.com/library/dn884293.aspx) 및 [writebatchtimeout](https://msdn.microsoft.com/library/dn884245.aspx) 을 옵션으로 표시 합니다.

## <a name="version-480"></a>버전 4.8.0
### <a name="feature-additions"></a>기능 추가
* 복사 성능 조정을 사용 하기 위해 복사 작업 유형에 다음과 같은 선택적 속성이 추가 되었습니다.
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>버전 4.7.0
### <a name="feature-additions"></a>기능 추가
* ORC (최적화 된 행 칼럼 형식) 형식의 파일을 복사 하기 위해 새 StorageFormat 유형 [Orcformat](https://msdn.microsoft.com/library/mt723391.aspx) 유형을 추가 했습니다.
* SqlDWSink에 [Allowpolybase](https://msdn.microsoft.com/library/mt723396.aspx) 및 PolyBaseSettings 속성을 추가 합니다.
  * PolyBase를 사용 하 여 데이터를 SQL Data Warehouse 복사 하도록 설정 합니다.

## <a name="version-461"></a>버전 4.6.1
### <a name="bug-fixes"></a>Hibajavítások
* 작업 창을 나열 하기 위한 HTTP 요청을 수정 합니다.
  * 요청 페이로드에서 리소스 그룹 이름 및 데이터 팩터리 이름을 제거 합니다.

## <a name="version-460"></a>버전 4.6.0
### <a name="feature-additions"></a>기능 추가
* [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)에 추가 된 속성은 다음과 같습니다.
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Adatkészletek](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)에 추가 된 속성은 다음과 같습니다.
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* 데이터가 JSON 형식인 데이터 집합을 정의 하는 새 [Storageformat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) 형식 [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) 형식이 추가 되었습니다.

## <a name="version-450"></a>버전 4.5.0
### <a name="feature-additions"></a>기능 추가
* [작업 창에 대 한 목록 작업을](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)추가 했습니다.
  * 엔터티 형식 (즉, 데이터 팩터리, 데이터 집합, 파이프라인 및 작업)을 기반으로 하는 필터를 사용 하 여 작업 창을 검색 하는 메서드를 추가 했습니다.
* 다음 연결 된 서비스 유형이 추가 되었습니다.
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* 다음 데이터 집합 형식이 추가 되었습니다.
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [webtabledataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* 다음 복사본 원본 유형이 추가 되었습니다.     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>버전 4.4.0
### <a name="feature-additions"></a>기능 추가
* 다음 연결 된 서비스 유형이 복사 활동에 대 한 데이터 원본 및 싱크로 추가 되었습니다.
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). 개념 정보 및 예제는 [AZURE STORAGE SAS 연결 된 서비스](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 를 참조 하세요.

## <a name="version-430"></a>버전 4.3.0
### <a name="feature-additions"></a>기능 추가
* 다음 연결 된 서비스 유형이 복사 활동에 대 한 데이터 원본으로 추가 되었습니다.
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). 개념 정보 및 예제는 [Data Factory를 사용 하 여 HDFS에서 데이터 이동](data-factory-hdfs-connector.md) 을 참조 하세요.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). 개념 정보 및 예제는 [Azure Data Factory를 사용 하 여 ODBC 데이터 저장소에서 데이터 이동](data-factory-odbc-connector.md) 을 참조 하세요.

## <a name="version-420"></a>버전 4.2.0
### <a name="feature-additions"></a>기능 추가
* 새 활동 형식 [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx)이 (가) 추가 되었습니다. 작업에 대 한 자세한 내용은 [업데이트 리소스 작업을 사용 하 여 AZURE ML 모델 업데이트](data-factory-azure-ml-batch-execution-activity.md)를 참조 하세요.
* 새 선택적 속성인 [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) 가 [AzureMLLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)에 추가 되었습니다.
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) 및 [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) 속성이 [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) 클래스에 추가 되었습니다.
* Data Factory 서비스에 대 한 클라이언트 호출의 시간 제한을 구성할 수 있습니다.

## <a name="version-410"></a>버전 4.1.0
### <a name="feature-additions"></a>기능 추가
* 다음 연결 된 서비스 유형이 추가 되었습니다.
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 다음과 같은 작업 유형이 추가 되었습니다.
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 다음 데이터 집합 형식이 추가 되었습니다.
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 복사 작업에 대 한 다음 원본 및 싱크 형식이 추가 되었습니다.
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>버전 4.0.1
### <a name="breaking-changes"></a>Kompatibilitástörő változások
다음 클래스의 이름이 변경 되었습니다. 새 이름은 4.0.0 release 이전에 클래스의 원래 이름 이었습니다.

| 4\.0.0의 이름 | 4\.0.1의 이름 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>4\.0.0-s verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
* 다음 클래스/인터페이스의 이름이 변경 되었습니다.

| 이전 이름 | Új név |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Table |[데이터 집합](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **List** 메서드는 이제 페이징된 결과를 반환 합니다. 응답이 비어 있지 않은 **NextLink** 속성을 포함 하는 경우 모든 페이지가 반환 될 때까지 클라이언트 응용 프로그램에서 다음 페이지를 계속 인출 해야 합니다.  Például:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **목록** 파이프라인 API는 전체 정보가 아니라 파이프라인의 요약만 반환 합니다. 예를 들어 파이프라인 요약의 작업에는 이름 및 유형만 포함 됩니다.

### <a name="feature-additions"></a>기능 추가
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) 클래스는 두 개의 새 속성인 **SliceIdentifierColumnName** 및 **sql cleanupscript**를 지원 하 여 idempotent copy to Azure SQL Data Warehouse를 지원 합니다. 이러한 속성에 대 한 자세한 내용은 [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) 문서를 참조 하세요.
* 이제 복사 작업의 일부로 Azure SQL Database 및 Azure SQL Data Warehouse 소스에 대 한 저장 프로시저 실행을 지원 합니다. [Sqlsource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) 및 [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) 클래스에는 다음과 같은 속성이 있습니다. **SqlReaderStoredProcedureName** 및 **StoredProcedureParameters**. 이러한 속성에 대 한 자세한 내용은 Azure.com의 [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) 및 [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) 문서를 참조 하세요.  
