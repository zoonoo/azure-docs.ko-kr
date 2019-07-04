---
title: 진단 로깅
titleSuffix: Azure Cognitive Services
description: 이 가이드는 Azure 인식 서비스에 대 한 진단 로깅을 사용 하도록 설정 하기 위한 단계별 지침을 제공 합니다. 이러한 로그는 문제 식별 및 디버깅에 사용 되는 리소스의 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 합니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155749"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Azure Cognitive Services에 대 한 진단 로깅을 사용 하도록 설정

이 가이드는 Azure 인식 서비스에 대 한 진단 로깅을 사용 하도록 설정 하기 위한 단계별 지침을 제공 합니다. 이러한 로그는 문제 식별 및 디버깅에 사용 되는 리소스의 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 합니다. 계속 하기 전에 Azure 계정이 있어야 Cognitive 서비스가 하나 이상에 대 한 구독을 사용 하 여 같은 [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)를 [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), 또는 [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)합니다.

## <a name="prerequisites"></a>필수 조건

진단 로깅을 사용 하려면 해야 로그 데이터를 저장할 위치 합니다. 이 자습서에서는 Azure Storage 및 Log Analytics를 사용 합니다.

* [Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -정책 감사, 정적 분석 또는 백업을 위해 진단 로그를 유지 합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -에 대 한 Azure 리소스에서 생성 된 원시 로그를 분석 하는 유연한 로그 검색 및 분석 도구입니다.

> [!NOTE]
> 추가 구성 옵션을 사용할 수 있습니다. 자세한 내용은 참조 하세요 [Azure 리소스에서 로그 데이터 수집 및 소비](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)합니다.

## <a name="enable-diagnostic-log-collection"></a>진단 로그 컬렉션 활성화  

Azure portal을 사용 하 여 로깅을 진단 사용 하 여 시작 해 보겠습니다.

> [!NOTE]
> PowerShell 또는 Azure CLI를 사용 하 여이 기능을 사용 하려면에 제공 된 지침을 사용 하 여 [Azure 리소스에서 로그 데이터 수집 및 소비](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs)합니다.

1. Azure Portal로 이동합니다. 그런 다음 찾습니다 하 고 Cognitive Services 리소스를 선택 합니다. 예를 들어 Bing 웹 검색을 구독 합니다.   
2. 그런 다음 왼쪽 탐색 메뉴에서 찾은 **모니터링** 선택한 **진단 설정**합니다. 이 화면에는이 리소스에 대 한 모든 이전에 만든된 진단 설정을 포함합니다.
3. 이전에 만든된 리소스를 사용 하려는 경우 이제 선택할 수 있습니다. 선택이 고, 그렇지 **진단 설정 추가 +** 합니다.
4. 설정에 대 한 이름을 입력 합니다. 선택한 **저장소 계정에 보관** 하 고 **log Analytics에 보내기**합니다.
5. 구성 하는 메시지가 표시 되 면 저장소 계정 및 OMS 작업 영역을 사용 하 여 진단 로그를 저장 하려면 선택 합니다. **참고**: 저장소 계정 또는 OMS 작업 영역에 없는 경우 새로 만들려면 지시를 따릅니다.
6. 선택 **감사**하십시오 **RequestResponse**, 및 **AllMetrics**합니다. 진단 로그 데이터의 보존 기간을 설정 합니다. 보존 정책이 0으로 설정 되 면 해당 로그 범주에 대 한 이벤트는 무기한으로 저장 됩니다.
7. **저장**을 클릭합니다.

로깅 데이터를 쿼리 및 분석 전에 최대 2 시간이 걸릴 수 있습니다. 표시 되지 않는 경우 아무 것도 바로 하므로 걱정 하지 마세요.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Azure Storage에서 진단 데이터를 내보내고 보기

Azure Storage는 많은 양의 구조화 되지 않은 데이터 저장에 최적화 된 강력한 개체 저장소 솔루션입니다. 이 섹션에서는 excel로 데이터를 내보내고 30 일 시간 프레임을 통해 저장소 계정의 총 트랜잭션에 대 한 쿼리 하는 방법을 배웁니다.

1. Azure portal에서 마지막 섹션에서 만든 Azure Storage 리소스를 찾습니다.
2. 왼쪽 탐색 메뉴에서 찾은 **모니터링** 선택한 **메트릭**합니다.
3. 쿼리를 구성 하려면 사용 가능한 드롭다운 메뉴를 사용 합니다. 예를 들어 보겠습니다 시간 범위를 설정 **지난 30 일간** 및 메트릭 **트랜잭션**합니다.
4. 쿼리가 완료 되 면 마지막 30 일 동안 트랜잭션의 시각화를 표시 됩니다. 이 데이터를 내보내려면 다음을 사용 합니다 **Excel로 내보내기** 페이지의 맨 위에 있는 단추입니다.

수행할 수 있는 진단 데이터에 대해 자세히 알아보려면 [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)합니다.

## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

리소스에 대 한 log analytics 데이터를 탐색 하려면 다음이 지침을 따릅니다.

1. Azure portal에서 찾기 및 선택 **Log Analytics** 왼쪽 탐색 메뉴에서.
2. 찾아 진단을 사용 하도록 설정할 때 만든 리소스를 선택 합니다.
3. 아래 **일반적인**을 찾아 선택 **로그**합니다. 이 페이지에서 로그에 대 한 쿼리를 실행할 수 있습니다.

### <a name="sample-queries"></a>샘플 쿼리

로그 데이터를 탐색할 때 사용할 수 있는 몇 가지 기본 Kusto 쿼리는 다음과 같습니다.

지정된 된 기간에 대 한 Azure Cognitive Services에서 모든 진단 로그에 대해이 쿼리를 실행 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

최근 10 개 로그를 확인 하려면이 쿼리를 실행 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

이 쿼리를 실행 하 여 그룹 작업에 **리소스**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
작업을 수행 하는 데 걸리는 평균 시간 확인 하려면이 쿼리를 실행 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

모든 10에 대 한 범주화 개수로 OperationName 별 분할 하는 시간에 따른 작업 볼륨을 보려면이 쿼리를 실행 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>다음 단계

* 읽어보세요 로깅 및 다양 한 Azure 서비스에서 지원 되는 메트릭과 로그 범주를 사용 하도록 설정 하는 방법을 알아야 합니다 [메트릭의 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) Microsoft Azure에서 및 [Azure 진단 로그 개요 ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) 문서입니다.
* Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.
  * [Azure Event Hubs 정의](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Event Hubs 시작](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* [Azure Storage에서 메트릭 및 진단 로그 다운로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)를 읽어보세요.
* 읽기 [Azure Monitor 로그의 로그 검색 이해](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)합니다.
