---
title: Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기 (미리 보기)
description: Log Analytics 데이터 내보내기를 사용 하면 데이터를 수집 하는 동안 Log Analytics 작업 영역에서 Azure storage 계정 또는 Azure Event Hubs로 선택한 테이블의 데이터를 지속적으로 내보낼 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: 19d464f0148572f30ecd0c3ab1dcee7bd0315b87
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427805"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기 (미리 보기)
Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기를 사용 하면 Log Analytics 작업 영역의 선택한 테이블에서 Azure storage 계정 또는 Azure Event Hubs 수집 된 데이터를 지속적으로 내보낼 수 있습니다. 이 문서에서는이 기능 및 작업 영역에서 데이터 내보내기를 구성 하는 단계에 대 한 세부 정보를 제공 합니다.

## <a name="overview"></a>개요
Log Analytics 작업 영역에 대 한 데이터 내보내기가 구성 되 면 작업 영역에서 선택한 테이블로 전송 된 모든 새 데이터가 자동으로 저장소 계정으로 또는 거의 실시간으로 이벤트 허브로 내보내집니다.

![데이터 내보내기 개요](media/logs-data-export/data-export-overview.png)

포함 된 테이블의 모든 데이터를 필터 없이 내보냅니다. 예를 들어 *securityevent* 테이블에 대 한 데이터 내보내기 규칙을 구성 하면 *securityevent* 테이블에 전송 된 모든 데이터를 구성 시간부터 내보냅니다.


## <a name="other-export-options"></a>기타 내보내기 옵션
Log Analytics 작업 영역 데이터 내보내기는 Log Analytics 작업 영역에서 데이터를 계속 내보냅니다. 특정 시나리오에 대 한 데이터를 내보내기 위한 기타 옵션은 다음과 같습니다.

- 논리 앱을 사용 하 여 로그 쿼리에서 예약 된 내보내기 이는 데이터 내보내기 기능과 유사 하지만 필터링 된 데이터 또는 집계 된 데이터를 Azure storage로 보낼 수 있습니다. 그러나이 방법에는 [로그 쿼리 제한이](../service-limits.md#log-analytics-workspaces)  적용 됩니다. [논리 앱을 사용 하 여 Log Analytics 작업 영역에서 Azure Storage로 데이터 보관](logs-export-logic-app.md)을 참조 하세요.
- 논리 앱을 사용 하 여 한 번 내보냅니다. [Logic Apps 및 전원 자동화에 대 한 Azure Monitor 로그 커넥터를](logicapp-flow-connector.md)참조 하세요.
- PowerShell 스크립트를 사용 하 여 로컬 컴퓨터에 한 번 내보냅니다. [AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)를 참조 하세요.


## <a name="current-limitations"></a>현재 제한 사항

- 현재 CLI 또는 REST 요청을 사용 해야만 구성을 수행할 수 있습니다. Azure Portal 또는 PowerShell을 사용할 수 없습니다.
- ```--export-all-tables```CLI 및 REST의 옵션은 지원 되지 않으며 제거 됩니다. 내보내기 규칙에서 테이블 목록을 명시적으로 제공 해야 합니다.
- 지원 되는 테이블은 현재 아래의 [지원 되는 테이블](#supported-tables) 섹션에 한정 되어 있습니다. 데이터 내보내기 규칙에 지원 되지 않는 테이블이 포함 되어 있으면 작업이 성공 하지만 해당 테이블에 대 한 데이터는 내보내지 않습니다. 데이터 내보내기 규칙이 존재 하지 않는 테이블을 포함 하는 경우 오류와 함께 실패 합니다. ```Table <tableName> does not exist in the workspace.```
- Log Analytics 작업 영역은 다음을 제외 하 고 모든 지역에 있을 수 있습니다.
  - 스위스 북부
  - 스위스 서부
  - Azure 정부 지역
- 대상 저장소 계정 또는 이벤트 허브는 Log Analytics 작업 영역과 동일한 지역에 있어야 합니다.
- 내보낼 테이블 이름은 저장소 계정에는 60 자이 하 여야 하 고 이벤트 허브에는 47 자이 하 여야 합니다. 이름이 긴 테이블은 내보내지지 않습니다.

> [!NOTE]
> Log Analytics 데이터 내보내기는 현재 Azure Data Lake Storage Gen2 미리 보기 상태인 추가 blob으로 데이터를 기록 합니다. 이 저장소로 내보내기를 구성 하기 전에 지원 요청을 열어야 합니다. 이 요청에 대해 다음 세부 정보를 사용 합니다.
> - 문제 유형: 기술
> - 구독: 사용자의 구독
> - 서비스: Data Lake Storage Gen2
> - 리소스: 리소스 이름
> - 요약: Log Analytics 데이터 내보내기에서 데이터를 허용 하도록 구독 등록을 요청 합니다.
> - 문제 유형: 연결
> - 문제 하위 유형: 연결 문제

## <a name="data-completeness"></a>데이터 완전성
데이터 내보내기는 대상을 사용할 수 없는 경우 최대 30 분 동안 데이터를 계속 해 서 다시 전송 합니다. 30 분 후에도 계속 사용할 수 없는 경우에는 대상을 사용할 수 있게 될 때까지 데이터가 삭제 됩니다.

## <a name="cost"></a>비용
현재 데이터 내보내기 기능에 대 한 추가 요금은 없습니다. 데이터 내보내기에 대 한 가격은 추후 발표 되며 청구를 시작 하기 전에 제공 됩니다. 알림 기간 후에도 계속 해 서 데이터 내보내기를 사용 하도록 선택 하면 해당 하는 요금으로 요금이 청구 됩니다.

## <a name="export-destinations"></a>내보내기 대상

### <a name="storage-account"></a>스토리지 계정
데이터는 매시간 저장소 계정으로 전송 됩니다. 데이터 내보내기 구성에서는 이름이 *am* 인 저장소 계정에 테이블의 각 테이블에 대 한 컨테이너를 만든 다음 테이블 이름을 입력 합니다. 예를 들어 테이블 *securityevent* 는 *Am-securityevent* 라는 컨테이너로 전송 됩니다.

저장소 계정 blob 경로는 *WorkspaceResourceId =/subscriptions/subscription-id/resourcegroups/ \<resource-group\> /providers/microsoft.operationalinsights/workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h =/m = \<two-digit 24-hour clock hour\> 00/PT1H.json* 입니다. 추가 blob은 저장소에서 50K 쓰기로 제한 되기 때문에, 추가의 수가 높으면 내보낸 blob 수가 확장 될 수 있습니다. 이러한 경우 blob의 명명 패턴은 PT1H_입니다. 여기서 #은 증분 blob 수입니다.

저장소 계정 데이터 형식은 [JSON 줄입니다](diagnostic-logs-append-blobs.md). 즉, 각 레코드는 바깥쪽 레코드는 없고 JSON 레코드 사이에는 쉼표가 없는 줄 바꿈으로 구분 됩니다. 

[![저장소 샘플 데이터](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics 데이터 내보내기는 시간 기반 보존 정책에서 *allowProtectedAppendWrites* 설정을 사용 하도록 설정한 경우 변경할 수 없는 저장소 계정에 추가 blob을 쓸 수 있습니다. 이를 통해 추가 blob에 새 블록을 쓸 수 있으며 불변성 보호 및 규정 준수를 유지 관리할 수 있습니다. [Protected 추가 blob 쓰기 허용](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes)을 참조 하세요.

### <a name="event-hub"></a>이벤트 허브
데이터는 Azure Monitor에 도달 하는 동안 거의 실시간으로 이벤트 허브로 전송 됩니다. 이름을 *am* 으로 내보내고 테이블 이름을 사용 하 여 내보낸 각 데이터 형식에 대해 이벤트 허브가 생성 됩니다. 예를 들어 테이블 *securityevent* 는 *Am-securityevent* 라는 이벤트 허브로 전송 됩니다. 내보낸 데이터를 특정 이벤트 허브에 연결 하려는 경우 또는 이름이 47 문자 제한을 초과 하는 테이블이 있는 경우 고유한 이벤트 허브 이름을 제공 하 고 정의 된 테이블의 모든 데이터를 내보낼 수 있습니다.

고려 사항:
1. ' 기본 ' 이벤트 허브 sku는 낮은 이벤트 크기 [제한을](https://docs.microsoft.com/azure/event-hubs/event-hubs-quotas#basic-vs-standard-tiers) 지원 하 고 작업 영역의 일부 로그는이를 초과 하 여 삭제할 수 있습니다. ' 표준 ' 또는 ' 전용 ' 이벤트 허브를 내보내기 대상으로 사용 하는 것이 좋습니다.
2. 내보내는 데이터의 볼륨은 시간이 지남에 따라 증가 하 고, 더 큰 전송 속도를 처리 하 고 제한 시나리오와 데이터 대기 시간을 방지 하려면 이벤트 허브 크기를 늘려야 합니다. Event Hubs의 자동 확장 기능을 사용 하 여 처리량 단위 수를 자동으로 확장 하 고 늘리고 사용 요구를 충족 해야 합니다. 자세한 내용은 [Azure Event Hubs 처리량 단위 자동 확장](../../event-hubs/event-hubs-auto-inflate.md) 을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항
다음은 Log Analytics 데이터 내보내기를 구성 하기 전에 완료 해야 하는 필수 구성 요소입니다.

- 저장소 계정 및 이벤트 허브는 이미 만들고 Log Analytics 작업 영역과 동일한 지역에 있어야 합니다. 데이터를 다른 저장소 계정에 복제 해야 하는 경우 [Azure Storage 중복성 옵션](../../storage/common/storage-redundancy.md)중 하나를 사용할 수 있습니다.  
- 저장소 계정은 StorageV1 또는 StorageV2 여야 합니다. 클래식 저장소는 지원 되지 않습니다.  
- 선택한 네트워크에서 액세스할 수 있도록 저장소 계정을 구성한 경우 저장소 계정 설정에 예외를 추가 하 여 Azure Monitor 저장소에 쓸 수 있도록 해야 합니다.

## <a name="enable-data-export"></a>데이터 내보내기 사용
Log Analytics 데이터 내보내기를 사용 하도록 설정 하려면 다음 단계를 수행 해야 합니다. 각에 대 한 자세한 내용은 다음 섹션을 참조 하십시오.

- 리소스 공급자를 등록 합니다.
- 신뢰할 수 있는 Microsoft 서비스를 허용 합니다.
- 내보낼 테이블과 해당 대상을 정의 하는 하나 이상의 데이터 내보내기 규칙을 만듭니다.

### <a name="register-resource-provider"></a>리소스 공급자 등록
Log Analytics 데이터 내보내기를 사용 하도록 설정 하려면 다음 Azure 리소스 공급자가 구독에 등록 되어 있어야 합니다. 

- Microsoft.Insights

이 리소스 공급자는 대부분의 Azure Monitor 사용자에 대해 이미 등록 되어 있을 것입니다. 확인 하려면 Azure Portal의 **구독** 으로 이동 합니다. 구독을 선택한 후 메뉴의 **설정** 섹션에서 **리소스 공급자** 를 클릭 합니다. **Microsoft Insights** 를 찾습니다. 해당 상태가 **등록** 됨 인 경우에는 이미 등록 되어 있습니다. 그렇지 않은 **경우 등록을 클릭 하** 여 등록 합니다.

[Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md)에 설명 된 대로 사용 가능한 방법 중 하나를 사용 하 여 리소스 공급자를 등록할 수도 있습니다. 다음은 PowerShell을 사용 하는 샘플 명령입니다.

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스 허용
선택한 네트워크에서 액세스할 수 있도록 저장소 계정을 구성한 경우 Azure Monitor 계정에 쓸 수 있도록 허용 하는 예외를 추가 해야 합니다. 저장소 계정에 대 한 **방화벽 및 가상 네트워크** 에서 **신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용** 을 선택 합니다.

[![Storage 계정 방화벽 및 가상 네트워크](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>데이터 내보내기 규칙 만들기 또는 업데이트
데이터 내보내기 규칙은 테이블 집합에 대해 내보낼 데이터를 단일 대상으로 정의 합니다. 각 대상에 대 한 규칙을 만들 수 있습니다.

다음 CLI 명령을 사용 하 여 작업 영역의 테이블을 볼 수 있습니다. 이를 통해 원하는 테이블을 복사 하 고 데이터 내보내기 규칙에 포함할 수 있습니다.
```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

CLI를 사용 하 여 저장소 계정에 대 한 데이터 내보내기 규칙을 만들려면 다음 명령을 사용 합니다.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

CLI를 사용 하 여 이벤트 허브에 대 한 데이터 내보내기 규칙을 만들려면 다음 명령을 사용 합니다.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

REST API를 사용 하 여 데이터 내보내기 규칙을 만들려면 다음 요청을 사용 합니다. 요청은 전달자 토큰 권한 부여 및 콘텐츠 형식 application/json을 사용 해야 합니다.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

요청의 본문은 테이블 대상을 지정 합니다. 저장소 계정에 대 한 REST 요청에 대 한 샘플 본문은 다음과 같습니다.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

다음은 이벤트 허브에 대 한 REST 요청에 대 한 샘플 본문입니다.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

다음은 이벤트 허브 이름이 제공 되는 이벤트 허브에 대 한 REST 요청에 대 한 샘플 본문입니다. 이 경우 내보낸 모든 데이터가이 이벤트 허브로 전송 됩니다.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

## <a name="view-data-export-configuration"></a>데이터 내보내기 구성 보기
CLI를 사용 하 여 데이터 내보내기 규칙의 구성을 보려면 다음 명령을 사용 합니다.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

REST API를 사용 하 여 데이터 내보내기 규칙의 구성을 보려면 다음 요청을 사용 합니다. 요청은 전달자 토큰 권한 부여를 사용 해야 합니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="disable-an-export-rule"></a>내보내기 규칙 사용 안 함
테스트를 수행 하는 경우와 같이 특정 기간에 대 한 데이터를 유지할 필요가 없는 경우 내보내기 규칙을 사용 하지 않도록 설정 하 여 내보내기를 중지할 수 있습니다. CLI를 사용 하 여 데이터 내보내기 규칙을 사용 하지 않도록 설정 하려면 다음 명령을 사용 합니다.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

REST API를 사용 하 여 데이터 내보내기 규칙을 사용 하지 않도록 설정 하려면 다음 요청을 사용 합니다. 요청은 전달자 토큰 권한 부여를 사용 해야 합니다.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

## <a name="delete-an-export-rule"></a>내보내기 규칙 삭제
CLI를 사용 하 여 데이터 내보내기 규칙을 삭제 하려면 다음 명령을 사용 합니다.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

REST API를 사용 하 여 데이터 내보내기 규칙을 삭제 하려면 다음 요청을 사용 합니다. 요청은 전달자 토큰 권한 부여를 사용 해야 합니다.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="view-all-data-export-rules-in-a-workspace"></a>작업 영역에서 모든 데이터 내보내기 규칙 보기
CLI를 사용 하 여 작업 영역에서 모든 데이터 내보내기 규칙을 보려면 다음 명령을 사용 합니다.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

REST API를 사용 하 여 작업 영역에서 모든 데이터 내보내기 규칙을 보려면 다음 요청을 사용 합니다. 요청은 전달자 토큰 권한 부여를 사용 해야 합니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

## <a name="unsupported-tables"></a>지원 되지 않는 테이블
데이터 내보내기 규칙에 지원 되지 않는 테이블이 포함 되어 있으면 구성이 성공 하지만 해당 테이블에 대 한 데이터는 내보내지 않습니다. 테이블이 나중에 지원 되는 경우 해당 데이터는 해당 시점에 내보내집니다.

데이터 내보내기 규칙이 존재 하지 않는 테이블을 포함 하는 경우 오류와 함께 실패 합니다. ```Table <tableName> does not exist in the workspace.```


## <a name="supported-tables"></a>지원 되는 테이블
지원 되는 테이블은 현재 아래에 지정 된 것으로 제한 됩니다. 제한이 지정 되지 않은 경우 테이블의 모든 데이터를 내보냅니다. 추가 테이블에 대 한 지원이 추가 되 면이 목록이 업데이트 됩니다.


| 테이블 | 제한 사항 |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| 경고 |부분 지원. 이 테이블에 대 한 데이터 중 일부는 저장소 계정을 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| 비정상 | |
| ApiManagementGatewayLogs | |
| App중앙 오류 | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | 부분 지원. 일부 데이터는 내보내기를 지원 하지 않는 내부 서비스를 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| 이벤트 | 부분 지원. 이 테이블에 대 한 데이터 중 일부는 저장소 계정을 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionEvents | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| 하트비트 | |
| HuntingBookmark | |
| InsightsMetrics | 부분 지원. 일부 데이터는 내보내기를 지원 하지 않는 내부 서비스를 통해 수집 됩니다. 현재 내보내기에이 부분이 없습니다. |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitorDNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | 부분 지원. 수집를 통해 Office 365에서 Log Analytics로 웹 후크를 통해 데이터를 가져올 수 있습니다. 이 데이터는 현재 내보내지 않습니다. |
| 작업(Operation) | 부분 지원. 일부 데이터는 내보내기를 지원 하지 않는 내부 서비스를 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| Perf | 부분 지원. Windows 성능 데이터만 현재 지원 됩니다. Linux 성능 데이터는 현재 내보내지 않습니다. |
| ProtectionStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| 보안 기능 검색 | |
| SecurityEvent | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| 보안 권장 사항 | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| SynapseGatewayEvents | |
| SynapseRBACEvents | |
| syslog | 부분 지원. 이 테이블에 대 한 데이터 중 일부는 저장소 계정을 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| ThreatIntelligenceIndicator | |
| 업데이트 | 부분 지원. 일부 데이터는 내보내기를 지원 하지 않는 내부 서비스를 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| UpdateRunProgress | |
| UpdateSummary | |
| 사용 | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| 관심 목록 | |
| WindowsEvent | |
| WindowsFirewall | |
| WireData | 부분 지원. 일부 데이터는 내보내기를 지원 하지 않는 내부 서비스를 통해 수집 됩니다. 이 데이터는 현재 내보내지 않습니다. |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>다음 단계

- [Azure 데이터 탐색기에서 내보낸 데이터를 쿼리](azure-data-explorer-query-storage.md)합니다.
