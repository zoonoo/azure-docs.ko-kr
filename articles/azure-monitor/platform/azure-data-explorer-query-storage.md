---
title: Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 내보낸 데이터 쿼리
description: Azure 데이터 탐색기를 사용 하 여 Log Analytics 작업 영역에서 Azure storage 계정으로 내보낸 데이터를 쿼리할 수 있습니다.
ms.subservice: logs
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: ec695912dcd59b474df132cac97d9069da4c5d51
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049923"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 내보낸 데이터 쿼리
Azure Monitor에서 Azure storage 계정으로 데이터를 내보내면 저렴 한 보존 및 여러 지역에 로그를 다시 할당할 수 있습니다. Azure 데이터 탐색기를 사용 하 여 Log Analytics 작업 영역에서 내보낸 데이터를 쿼리할 수 있습니다. 구성 된 후에는 작업 영역에서 Azure storage 계정으로 전송 된 지원 테이블을 Azure 데이터 탐색기의 데이터 원본으로 사용할 수 있습니다.

프로세스 흐름은 다음과 같습니다. 

1.  Log Analytics 작업 영역에서 Azure storage 계정으로 데이터를 내보냅니다.
2.  Azure 데이터 탐색기 클러스터에 외부 테이블을 만들고 데이터 형식에 대 한 매핑을 만듭니다.
3.  Azure 데이터 탐색기에서 데이터를 쿼리 합니다.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure 데이터 탐색기 내보낸 데이터 쿼리 흐름입니다.":::



## <a name="send-data-to-azure-storage"></a>Azure storage로 데이터 전송
다음 옵션 중 하나를 사용 하 여 Azure Monitor 로그를 Azure Storage 계정으로 내보낼 수 있습니다.

- Log Analytics 작업 영역의 모든 데이터를 Azure storage 계정 또는 이벤트 허브로 내보내려면 Azure Monitor 로그의 Log Analytics 작업 영역 데이터 내보내기 기능을 사용 합니다. [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기 (미리 보기)를](logs-data-export.md) 참조 하세요.
- 논리 앱을 사용 하 여 로그 쿼리에서 예약 된 내보내기 이는 데이터 내보내기 기능과 유사 하지만 필터링 된 데이터 또는 집계 된 데이터를 Azure storage로 보낼 수 있습니다. 그러나이 방법에는 [로그 쿼리 제한이](../service-limits.md#log-analytics-workspaces)  적용 됩니다. [논리 앱을 사용 하 여 Log Analytics 작업 영역에서 Azure Storage로 데이터 보관](logs-export-logic-app.md)을 참조 하세요.
- 논리 앱을 사용 하 여 한 번 내보냅니다. [Logic Apps 및 전원 자동화에 대 한 Azure Monitor 로그 커넥터를](logicapp-flow-connector.md)참조 하세요.
- PowerShell 스크립트를 사용 하 여 로컬 컴퓨터에 한 번 내보냅니다. [AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)를 참조 하세요.

> [!TIP]
> 기존 Azure 데이터 탐색기 클러스터를 사용 하거나 필요한 구성을 사용 하 여 새 전용 클러스터를 만들 수 있습니다.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Azure blob storage에 있는 외부 테이블 만들기
[외부 테이블](/azure/data-explorer/kusto/query/schema-entities/externaltables) 을 사용 하 여 azure 데이터 탐색기 azure storage 계정에 연결 합니다. 외부 테이블은 Kusto 데이터베이스 외부에 저장 된 데이터를 참조 하는 Kusto 스키마 엔터티입니다. 테이블과 마찬가지로 외부 테이블에는 잘 정의 된 스키마가 있습니다. 테이블과 달리 데이터는 Kusto 클러스터 외부에 저장 되 고 관리 됩니다. 이전 섹션에서 내보낸 데이터는 JSON 줄에 저장 됩니다.

참조를 만들려면 내보낸 테이블의 스키마가 필요 합니다. Log Analytics에서 [getschema](/azure/data-explorer/kusto/query/getschemaoperator) 연산자를 사용 하 여 테이블의 열과 해당 데이터 형식을 포함 하는이 정보를 검색 합니다.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Azure 데이터 탐색기 내보낸 데이터 쿼리 흐름입니다.":::

이제 출력을 사용 하 여 외부 테이블을 빌드하기 위한 Kusto 쿼리를 만들 수 있습니다.
[Azure Storage 또는 Azure Data Lake에서 외부 테이블 만들기 및 변경](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)의 지침에 따라 JSON 형식으로 외부 테이블을 만든 다음 Azure 데이터 탐색기 데이터베이스에서 쿼리를 실행 합니다.

>[!NOTE]
>외부 테이블 만들기는 두 개의 프로세스에서 빌드됩니다. 첫 번째는 외부 테이블을 만들고 두 번째는 매핑을 만드는 것입니다.

다음 PowerShell 스크립트는 테이블 및 매핑에 대 한 [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) 명령을 만듭니다.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The log lanlyics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The log lanlyics WorkspaceId
    $WorkspaceName, # The log lanlyics workspace name
    $BlobURL, # The Blob URL where to save
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}

$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'

)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

다음 그림은 출력의 예를 보여 줍니다.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Azure 데이터 탐색기 내보낸 데이터 쿼리 흐름입니다.":::

[![예제 출력](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>Azure 데이터 탐색기 클라이언트 도구에서 스크립트의 출력을 복사 하 여 붙여넣고 실행 하 여 테이블과 매핑을 만듭니다.

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 내보낸 데이터 쿼리 

매핑을 구성한 후 Azure 데이터 탐색기에서 내보낸 데이터를 쿼리할 수 있습니다. 쿼리에는 다음 예제와 같은 [external_table](/azure/data-explorer/kusto/query/externaltablefunction) 함수가 필요 합니다.

```kusto
external_table("HBTest","map") | take 10000
```

[![내보낸 데이터 Log Analytics 쿼리](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 탐색기에서 쿼리를 작성](https://docs.microsoft.com/azure/data-explorer/write-queries) 하는 방법을 알아봅니다.