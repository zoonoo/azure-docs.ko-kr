---
title: 저장소 계정에 Azure 리소스 로그 보관 | Microsoft Docs
description: 저장소 계정에 장기 보존을 위해 Azure 리소스 로그를 보관 하는 방법에 대해 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394941"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>저장소 계정에 Azure 리소스 로그 보관
Azure 활동 로그 및 리소스 로그를 포함 하 여 azure의 [플랫폼 로그](platform-logs-overview.md) 는 azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다.  이 문서에서는 보관할 데이터를 유지 하기 위해 Azure storage 계정에 플랫폼 로그를 수집 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항
아직 없는 경우 [Azure storage 계정을 만들어야](../../storage/common/storage-account-create.md) 합니다. 설정을 구성 하는 사용자가 두 구독에 대 한 적절 한 RBAC 액세스를 가진 경우 저장소 계정은 로그를 보내는 리소스와 동일한 구독을가지고 있지 않아도 됩니다.


> [!IMPORTANT]
> Azure Data Lake Storage Gen2 계정은 현재 Azure Portal에서 유효한 옵션으로 나열 될 수 있지만 진단 설정의 대상으로 지원 되지 않습니다.


데이터에 대 한 액세스를 더 효율적으로 제어할 수 있도록 저장 된 다른 데이터를 모니터링 하지 않는 기존 저장소 계정을 사용해 서는 안 됩니다. 그러나 활동 로그와 리소스 로그를 함께 보관 하는 경우 동일한 저장소 계정을 사용 하 여 모든 모니터링 데이터를 중앙 위치에 유지 하도록 선택할 수 있습니다.

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
Azure 리소스에 대 한 진단 설정을 만들어 플랫폼 로그를 저장소 및 기타 대상으로 보냅니다. 자세한 내용은 [Azure에서 로그 및 메트릭을 수집 하는 진단 설정 만들기를](diagnostic-settings.md) 참조 하세요.


## <a name="collect-data-from-compute-resources"></a>계산 리소스에서 데이터 수집
진단 설정은 다른 리소스와 마찬가지로 Azure 계산 리소스에 대 한 리소스 로그를 수집 하지만 게스트 운영 체제 또는 워크 로드는 수집 하지 않습니다. 이 데이터를 수집 하려면 [Windows Azure 진단 에이전트](diagnostics-extension-overview.md)를 설치 합니다. 


## <a name="schema-of-platform-logs-in-storage-account"></a>저장소 계정의 플랫폼 로그 스키마

진단 설정을 만들면 저장소 계정에서 사용 되는 로그 범주 중 하나에서 이벤트가 발생 하는 즉시 저장소 계정에 저장소 컨테이너가 만들어집니다. 컨테이너 내의 blob는 다음과 같은 명명 규칙을 사용 합니다.

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 네트워크 보안 그룹에 대 한 blob의 이름은 다음과 유사할 수 있습니다.

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 리소스 로그 이벤트는 시간당 개별 blob으로 분할 되므로 분 값 (m = 00)은 항상 00입니다.

PT1H.JSON 파일 내에서 각 이벤트는 다음과 같은 형식으로 저장 됩니다. 이는 [리소스 로그 스키마](diagnostic-logs-schema.md) 및 [활동 로그 스키마](activity-log-schema.md)에 설명 된 대로 공통 최상위 스키마를 사용 하지만 각 Azure 서비스에 대해 고유 합니다.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 플랫폼 로그는 [JSON 줄](http://jsonlines.org/)을 사용 하 여 blob 저장소에 기록 됩니다. 여기서 각 이벤트는 줄 이며 줄 바꿈 문자는 새 이벤트를 나타냅니다. 이 형식은 11 월 2018에 구현 되었습니다. 이 날짜 이전에는 [저장소 계정에 보관 된 Azure Monitor 플랫폼 로그에 대 한 형식 변경 준비](resource-logs-blob-format.md)에 설명 된 대로 blob 저장소에 레코드의 json 배열로 로그가 기록 되었습니다.

## <a name="next-steps"></a>다음 단계

* [자세한 내용은 리소스 로그를 참조](platform-logs-overview.md)하세요.
* [Azure에서 로그 및 메트릭을 수집 하는 진단 설정을 만듭니다](diagnostic-settings.md).
* [분석을 위해 blob을 다운로드](../../storage/blobs/storage-quickstart-blobs-dotnet.md)합니다.
* [Azure Monitor를 사용 하 여 Azure Active Directory 로그를 보관](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)합니다.
