---
title: Azure Monitor 진단 로그에 대한 형식 변경 준비
description: Azure 진단 로그는 추가 Blob을 사용하도록 2018년 11월 1일에 전환됩니다.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ab5fba6bbbf6ade83c7699edec937ba02b222939
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370060"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>스토리지 계정에 보관된 Azure Monitor 진단 로그에 대한 형식 변경 준비

> [!WARNING]
> [리소스 진단 설정을 사용하여 Azure 리소스 진단 로그 또는 메트릭을 저장소 계정](./../../azure-monitor/platform/archive-diagnostic-logs.md)에 전송하는 경우 또는 [로그 프로필을 사용하여 활동 로그를 저장소 계정](./../../azure-monitor/platform/archive-activity-log.md)에 전송하는 경우, 2018년 11월 1일에 저장소 계정의 데이터 형식이 JSON 줄로 변경됩니다. 아래 지침에서는 새 형식을 처리하도록 도구를 업데이트하는 방법 및 영향에 대해 설명합니다. 
>
> 

## <a name="what-is-changing"></a>변경 사항

Azure Monitor는 Azure storage 계정, Event Hubs 네임 스페이스 또는 Azure Monitor에서 Log Analytics 작업 영역에 리소스 진단 데이터 및 활동 로그 데이터를 보낼 수 있는 기능을 제공 합니다. 시스템 성능 문제를 해결하기 위해 **2018년 11월 1일 오전 12시(UTC)** 에 Blob Storage로 전송되는 로그 데이터의 형식이 변경됩니다. Blob Storage에서 데이터를 읽는 도구가 있는 경우, 새 데이터 형식을 이해하도록 도구를 업데이트해야 합니다.

* 2018년 11월 1일 목요일 오전 12시(UTC)에 Blob 형식이 [JSON 줄](http://jsonlines.org/)로 변경됩니다. 즉, 각 레코드가 줄 바꿈으로 구분되고 외부 레코드 배열 및 JSON 레코드 사이의 쉼표가 없습니다.
* 모든 구독의 모든 진단 설정에 대해 Blob 형식이 동시에 변경됩니다. 11월 1일에 내보내는 첫 번째 PT1H.json 파일은 이 새로운 형식을 사용합니다. Blob 및 컨테이너 이름은 동일하게 유지됩니다.
* 현재와 11월 1일 사이에 진단 설정을 지정해도, 11월 1일까지는 현재 형식으로 데이터를 계속 내보냅니다.
* 이 변경은 모든 공용 클라우드 지역에서 동시에 적용됩니다. Azure 중국, Azure 독일 또는 Azure Government 클라우드에서는 변경이 아직 적용되지 않습니다.
* 이 변경은 다음 데이터 형식에 영향을 줍니다.
  * [Azure 리소스 진단 로그](./../../azure-monitor/platform/archive-diagnostic-logs.md)([여기서 리소스 목록 확인](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [진단 설정을 통해 내보내지는 Azure 리소스 메트릭](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [로그 프로필을 통해 내보내지는 Azure 활동 로그 데이터](./../../azure-monitor/platform/archive-activity-log.md)
* 다음 경우에는 이 변경의 영향을 받지 않습니다.
  * 네트워크 흐름 로그
  * Azure Monitor를 통해 사용할 수 없는 Azure 서비스 로그(예: Azure App Service 진단 로그, 저장소 분석 로그)
  * 다른 대상(Event Hubs, Log Analytics)으로 Azure 진단 로그 및 활동 로그 라우팅

### <a name="how-to-see-if-you-are-impacted"></a>영향을 받는지 확인하는 방법

다음과 같은 경우에만 이 변경의 영향을 받습니다.
1. 리소스 진단 설정을 사용하여 Azure Storage 계정에 로그 데이터를 전송함 및
2. 스토리지에 있는 이러한 로그의 JSON 구조를 사용하는 도구가 있음
 
Azure Storage 계정에 데이터를 보내는 리소스 진단 설정이 있는지 확인하려면 포털의 **모니터** 섹션으로 이동하고 **진단 설정**을 클릭한 다음, **진단 상태**가 **사용**으로 설정된 리소스를 식별합니다.

![Azure Monitor 진단 설정 블레이드](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

진단 상태가 사용으로 설정된 경우, 해당 리소스에 활성 진단 설정이 있습니다. 리소스를 클릭하여 저장소 계정에 데이터를 보내는 진단 설정이 있는지 확인합니다.

![저장소 계정 사용](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

이러한 리소스 진단 설정을 사용하여 저장소 계정에 데이터를 보내는 리소스가 있는 경우, 해당 저장소 계정의 데이터 형식은 이 변경의 영향을 받습니다. 이러한 저장소 계정으로 작동하는 사용자 지정 도구가 없으면 형식 변경이 영향을 주지 않습니다.

### <a name="details-of-the-format-change"></a>형식 변경 세부 정보

Azure Blob Storage에 있는 PT1H.json 파일의 현재 형식은 레코드의 JSON 배열을 사용합니다. 다음은 현재 KeyVault 로그 파일의 샘플입니다.

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

새 형식은 각 이벤트가 한 줄이고, 줄 바꿈 문자가 새 이벤트를 나타내는 [JSON 줄](http://jsonlines.org/)을 사용합니다. 변경 후에 위의 샘플은 PT1H.json 파일에서 다음과 같이 표시됩니다.

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

이 새로운 형식이 적용되면 Azure Monitor가 [추가 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)을 사용하여 로그 파일을 푸시할 수 있습니다. 이 방법이 새 이벤트 데이터를 지속적으로 추가하는 데 더 효율적입니다.

## <a name="how-to-update"></a>업데이트 방법

추가 처리를 위해 이러한 로그 파일을 수집하는 사용자 지정 도구가 있는 경우에만 업데이트를 수행하면 됩니다. 외부 로그 분석 또는 SIEM 도구를 사용하는 경우, [이벤트 허브를 사용하여 이 데이터를 수집](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)하는 것이 좋습니다. 여러 서비스의 로그를 처리하고 특정 로그의 위치에 책갈피를 설정하려면 이벤트 허브 통합이 더 편리합니다.

위에서 설명한 JSON 줄 형식과 현재 형식을 둘 다 처리하도록 사용자 지정 도구를 업데이트해야 합니다. 이렇게 하면 데이터가 새 형식으로 표시되기 시작해도 도구가 중단되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [리소스 진단 로그를 저장소 계정에 보관](./../../azure-monitor/platform/archive-diagnostic-logs.md)하는 방법을 알아봅니다.
* [활동 로그 데이터를 저장소 계정에 보관](./../../azure-monitor/platform/archive-activity-log.md)하는 방법을 알아봅니다.

