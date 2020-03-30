---
title: 저장소 계정에 Azure 리소스 로그 보관 | 마이크로 소프트 문서
description: 저장소 계정에서 장기 보존을 위해 Azure 리소스 로그를 보관하는 방법을 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274217"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>스토리지 계정에 Azure 리소스 로그 보관
Azure 활동 로그 및 리소스 로그를 포함한 [Azure의 플랫폼 로그는](platform-logs-overview.md) Azure 리소스 및 해당 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다.  이 문서에서는 보관을 위해 데이터를 유지하기 위해 Azure 저장소 계정에 대한 플랫폼 로그 수집에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항
아직 Azure 저장소 계정이 없는 경우 [Azure 저장소 계정을 만들어야](../../storage/common/storage-account-create.md) 합니다. 저장소 계정을 리소스 전송 로그와 동일한 구독에 있을 필요는 없습니다.


> [!IMPORTANT]
> Azure Data Lake Storage Gen2 계정은 Azure Portal에서 유효한 옵션으로 나열될 수 있더라도 진단 설정의 대상으로 현재 지원되지 않습니다.


데이터에 대한 액세스를 더 잘 제어할 수 있도록 모니터링되지 않는 다른 데이터가 저장된 기존 저장소 계정을 사용해서는 안 됩니다. 하지만 활동 로그와 리소스 로그를 함께 보관하는 경우 동일한 저장소 계정을 사용하여 모든 모니터링 데이터를 중앙 위치에 유지하도록 선택할 수 있습니다.

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
Azure 리소스에 대한 진단 설정을 만들어 플랫폼 로그를 저장소 및 기타 대상으로 보냅니다. 자세한 내용은 [Azure에서 로그 및 메트릭을 수집하려면 진단 설정 만들기를](diagnostic-settings.md) 참조하십시오.


## <a name="collect-data-from-compute-resources"></a>컴퓨팅 리소스에서 데이터 수집
진단 설정은 다른 리소스와 마찬가지로 Azure 계산 리소스에 대한 리소스 로그를 수집하지만 게스트 운영 체제 또는 워크로드는 수집하지 않습니다. 이 데이터를 수집하려면 [Windows Azure 진단 에이전트를 설치합니다.](diagnostics-extension-overview.md) 


## <a name="schema-of-platform-logs-in-storage-account"></a>저장소 계정의 플랫폼 로그 스키마

진단 설정을 만든 후에는 활성화된 로그 범주 중 하나에서 이벤트가 발생하는 즉시 저장소 저장소 저장소 저장소가 만들어집니다. 컨테이너 내의 Blob은 다음 명명 규칙을 사용합니다.

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 네트워크 보안 그룹의 Blob에는 다음과 유사한 이름이 있을 수 있습니다.

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 리소스 로그 이벤트가 시간당 개별 Blob로 나뉘기 때문에 분 값(m=00)은 항상 00입니다.

PT1H.json 파일 내에서 각 이벤트는 다음 형식으로 저장됩니다. 이렇게 하면 공통 최상위 스키마를 사용하지만 [리소스 로그 스키마](diagnostic-logs-schema.md) 및 활동 [로그 스키마에](activity-log-schema.md)설명된 대로 각 Azure 서비스에 대해 고유합니다.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 플랫폼 로그는 [JSON 줄을](http://jsonlines.org/)사용하여 Blob 저장소에 기록되며, 여기서 각 이벤트는 줄이고 줄 바선은 새 이벤트를 나타냅니다. 이 형식은 2018년 11월에 구현되었습니다. 이 날짜 이전에는 저장소 계정에 [보관된 Azure Monitor 플랫폼 로그에 대한 형식 변경 준비에](resource-logs-blob-format.md)설명된 대로 blob 저장소에 기록된 레코드의 json 배열로 로그가 작성되었습니다.

## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대해 자세히 알아보기.](platform-logs-overview.md)
* [진단 설정을 만들어 Azure 에서 로그 및 메트릭을 수집합니다.](diagnostic-settings.md)
* [분석을 위해 Blob을 다운로드합니다.](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Azure 모니터를 사용 하 고 Azure Active Directory 로그를 보관](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)합니다.
