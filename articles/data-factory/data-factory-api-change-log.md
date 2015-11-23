<properties 
	pageTitle="Data Factory - .NET API 변경 로그 | Microsoft Azure" 
	description="Azure Data Factory에 대한 특정 .NET API 버전의 주요 변경 내용, 기능 추가 사항, 버그 수정 등을 설명합니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - .NET SDK 변경 로그 
이 문서에서는 특정 버전의 Azure Data Factory SDK 변경 내용에 대해 설명합니다. [여기](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)에서 Azure Data Factory용 최신 NuGet 패키지를 찾을 수 있습니다.

## 버전 4.2.0
릴리스 날짜: 2015-11-10

### 기능 추가 사항

- 다음과 같은 새 활동 유형을 추가되었습니다. [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx) 활동에 대한 자세한 내용은 [업데이트 리소스 활동을 사용하여 Azure ML 모델 업데이트](https://azure.microsoft.com/documentation/articles/data-factory-azure-ml-batch-execution-activity/#updating-azure-ml-models-using-the-update-resource-activity)를 참조하세요.
- 새 선택 속성 [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx)가 [AzureMLLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)에 추가되었습니다. 
- [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) 및 [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) 속성이 [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) 클래스에 추가되었습니다. 
- 데이터 팩터리 서비스에 대한 클라이언트 호출의 시간 제한 구성을 허용합니다. 


## Version 4.1.0
Release date: 2015-10-28

### 기능 추가 사항
* 다음에 링크된 서비스 유형이 추가 되었습니다. 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 다음 활동 유형이 추가 되었습니다. 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 다음과 같은 데이터 집합이 추가 되었습니다. 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 다음 복사 작업에 대한 원본 및 싱크 유형은 추가 되었습니다.
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## 버전 4.0.1
릴리스 날짜: 2015-10-13

### 주요 변경 내용
다음 클래스 이름이 변경되었습니다. 새 이름은 4.0.0 릴리스 이전의 원래 클래스 이름입니다.
 
4\.0.0의 이름 | 4\.0.1의 이름
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## 버전 4.0.0
릴리스 날짜: 2015-10-02

### 주요 변경 내용



- 다음 클래스/인터페이스의 이름이 변경되었습니다.

| 이전 이름 | 새 이름 |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| 테이블 | [데이터 집합](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- 이 릴리스의 **API 버전**은 **2015-10-01**입니다.

- **List** 메서드는 이제 페이징된 결과를 반환합니다. 응답에 비어 있지 않은 **NextLink** 속성이 포함된 경우 클라이언트 응용 프로그램은 모든 페이지가 반환될 때까지 다음 페이지를 계속 가져와야 합니다. 다음은 예제입니다.

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- **List** 파이프라인 API는 전체 세부 정보 대신 파이프라인의 요약만 반환합니다. 예를 들어 파이프라인 요약의 작업에는 이름과 형식만 포함됩니다.

### 기능 추가 사항
- [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) 클래스는 두 가지 새로운 속성인 **SliceIdentifierColumnName** 및 **SqlWriterCleanupScript**를 지원하여 Azure SQL 데이터 웨어하우스에 대한 idempotent 복사를 지원합니다. 이러한 속성에 대한 자세한 내용은 [Azure SQL 데이터 웨어하우스](data-factory-azure-sql-data-warehouse-connector.md) 문서에서 특히 [메커니즘 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) 및 [메커니즘 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) 섹션을 참조하세요.

- 이제 복사 작업의 일부로 Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스 소스에 대한 저장 프로시저 실행을 지원합니다. 이를 지원하기 위해 [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) 및 [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) 클래스에는 **SqlReaderStoredProcedureName** 및 **StoredProcedureParameters** 속성이 있습니다. 이러한 속성에 대한 자세한 내용은 Azure.com에서 [Azure SQL 데이터베이스](data-factory-azure-sql-connector.md#sqlsource) 및 [Azure SQL 데이터 웨어하우스](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) 문서를 참조하세요.

<!---HONumber=Nov15_HO3-->