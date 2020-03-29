---
title: 진단 로깅
titleSuffix: Azure Cognitive Services
description: 이 가이드는 Azure Cognitive 서비스에 대한 진단 로깅을 사용하도록 설정하는 단계별 지침을 제공합니다. 이러한 로그는 문제 식별 및 디버깅에 사용되는 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827909"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Azure 코그너티브 서비스에 대한 진단 로깅 사용

이 가이드는 Azure Cognitive 서비스에 대한 진단 로깅을 사용하도록 설정하는 단계별 지침을 제공합니다. 이러한 로그는 문제 식별 및 디버깅에 사용되는 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. 계속하기 전에 [Bing Web Search,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview) [음성 서비스](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)또는 [LUIS와](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)같은 하나 이상의 코그너티브 서비스에 대한 구독이 있는 Azure 계정이 있어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

진단 로깅을 사용하려면 로그 데이터를 저장할 수 있는 곳이 필요합니다. 이 자습서에서는 Azure 저장소 및 로그 분석을 사용합니다.

* [Azure 저장소](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - 정책 감사, 정적 분석 또는 백업을 위한 진단 로그를 유지합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.
* [로그 분석](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) - Azure 리소스에서 생성된 원시 로그를 분석할 수 있는 유연한 로그 검색 및 분석 도구입니다.

> [!NOTE]
> 추가 구성 옵션을 사용할 수 있습니다. 자세한 내용은 [Azure 리소스에서 로그 데이터 수집 및 사용(를 참조)을 참조하세요.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

## <a name="enable-diagnostic-log-collection"></a>진단 로그 수집 사용  

Azure 포털을 사용하여 진단 로깅을 사용하도록 설정하여 시작해 보겠습니다.

> [!NOTE]
> PowerShell 또는 Azure CLI를 사용하여 이 기능을 사용하려면 [Azure 리소스에서 로그 데이터를 수집하고 사용할](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)수 있는 수집에 제공된 지침을 사용합니다.

1. Azure Portal로 이동합니다. 그런 다음 코그너티브 서비스 리소스를 찾아 선택합니다. 예를 들어 Bing 웹 검색에 대한 구독입니다.   
2. 다음으로 왼쪽 탐색 메뉴에서 **모니터링을** 찾아 **진단 설정을**선택합니다. 이 화면에는 이 리소스에 대해 이전에 만든 모든 진단 설정이 포함되어 있습니다.
3. 사용하려는 이전에 만든 리소스가 있는 경우 지금 선택할 수 있습니다. 그렇지 않으면 **+ 진단 설정 추가를**선택합니다.
4. 설정의 이름을 입력합니다. 그런 다음 **저장소 계정에 보관을** 선택하고 **로그 애널리틱스에 보내기를**선택합니다.
5. 구성하라는 메시지가 표시되면 진단 로그를 저장하는 데 사용할 저장소 계정 및 OMS 작업 영역을 선택합니다. **참고**: 저장소 계정 이나 OMS 작업 영역이 없는 경우 프롬프트에 따라 하나를 만듭니다.
6. **감사,** **요청 응답**및 **AllMetrics를 선택합니다.** 그런 다음 진단 로그 데이터의 보존 기간을 설정합니다. 보존 정책이 0으로 설정된 경우 해당 로그 범주에 대한 이벤트가 무기한 저장됩니다.
7. **저장**을 클릭합니다.

데이터를 쿼리하고 분석하는 데 데이터를 로깅하는 데 최대 2시간이 걸릴 수 있습니다. 그래서 당신은 바로 아무것도 표시되지 않는 경우 걱정하지 마십시오.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Azure 저장소에서 진단 데이터 보기 및 내보내기

Azure Storage는 많은 양의 비정형 데이터를 저장하는 데 최적화된 강력한 개체 저장소 솔루션입니다. 이 섹션에서는 30일 기간 동안 총 트랜잭션에 대한 저장소 계정을 쿼리하고 데이터를 탁월하게 내보내는 방법을 배웁니다.

1. Azure 포털에서 마지막 섹션에서 만든 Azure 저장소 리소스를 찾습니다.
2. 왼쪽 탐색 메뉴에서 **모니터링을** 찾아 **메트릭을 선택합니다.**
3. 사용 가능한 드롭다운을 사용하여 쿼리를 구성합니다. 이 예제에서는 시간 범위를 **마지막 30일로** 설정하고 메트릭을 **트랜잭션으로**설정해 보겠습니다.
4. 쿼리가 완료되면 지난 30일 동안의 트랜잭션 시각화가 표시됩니다. 이 데이터를 내보내려면 페이지 상단에 있는 **Excel로 내보내기** 단추를 사용합니다.

[Azure 저장소에서](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)진단 데이터로 수행할 수 있는 작업에 대해 자세히 알아봅니다.

## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

다음 지침에 따라 리소스에 대한 로그 분석 데이터를 탐색합니다.

1. Azure 포털에서 왼쪽 탐색 메뉴에서 **로그 분석을** 찾아 선택합니다.
2. 진단을 사용하도록 설정할 때 만든 리소스를 찾아 선택합니다.
3. **일반**에서 로그를 찾아 **선택합니다.** 이 페이지에서 로그에 대한 쿼리를 실행할 수 있습니다.

### <a name="sample-queries"></a>샘플 쿼리

다음은 로그 데이터를 탐색하는 데 사용할 수 있는 몇 가지 기본 Kusto 쿼리입니다.

지정된 기간 동안 Azure Cognitive Services의 모든 진단 로그에 대해 이 쿼리를 실행합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

이 쿼리를 실행하여 가장 최근로 10개의 로그를 확인합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

**리소스별로**작업을 그룹화하기 위해 이 쿼리를 실행합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
이 쿼리를 실행하여 작업을 수행하는 데 걸리는 평균 시간을 찾습니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

이 쿼리를 실행하여 OperationName으로 분할된 시간별 작업 볼륨을 10초마다 바인드된 개수로 봅니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>다음 단계

* 로깅을 활성화하는 방법과 다양한 Azure 서비스에서 지원하는 메트릭 및 로그 범주를 이해하려면 Microsoft [Azure의 메트릭 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) 와 [Azure 진단 로그](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) 문서 의 개요를 모두 읽어 보십시오.
* Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.
  * [Azure Event Hubs 정의](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Event Hubs 시작](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* [Azure Storage에서 메트릭 및 진단 로그 다운로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)를 읽어보세요.
* [Azure 모니터 로그에서 로그 검색 이해 읽기.](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)
