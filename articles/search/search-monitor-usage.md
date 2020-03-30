---
title: 운영 및 활동 모니터링
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에서 로깅 을 활성화하고 쿼리 활동 메트릭, 리소스 사용량 및 기타 시스템 데이터를 가져옵니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462329"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Azure 인지 검색의 작업 및 활동 모니터링

이 문서에서는 서비스(리소스) 수준, 워크로드 수준(쿼리 및 인덱싱)에서 모니터링을 소개하고 사용자 액세스를 모니터링하기 위한 프레임워크를 제안합니다.

전체 스펙트럼에서 Azure Monitor와 같은 기본 인프라와 기본 서비스의 조합과 통계, 개수 및 상태를 반환하는 서비스 API를 사용합니다. 기능 범위를 이해하면 피드백 루프를 구성하여 문제가 발생할 때 문제를 해결할 수 있습니다.

## <a name="use-azure-monitor"></a>Azure Monitor 사용

Azure Cognitive Search를 비롯한 많은 서비스는 경고, 메트릭 및 로깅 진단 데이터에 [Azure 모니터를](https://docs.microsoft.com/azure/azure-monitor/) 활용합니다. Azure Cognitive Search의 경우 기본 제공 모니터링 인프라는 주로 리소스 수준 모니터링(서비스 상태) 및 [쿼리 모니터링에](search-monitor-queries.md)사용됩니다.

다음 스크린샷은 포털에서 Azure Monitor 기능을 찾는 데 도움이 됩니다.

+ 기본 개요 페이지에 있는 **모니터링** 탭은 주요 메트릭을 한눈에 보여줍니다.
+ **활동 로그**, 개요 바로 아래, 리소스 수준 작업에 대한 보고서: 서비스 상태 및 API 키 요청 알림.
+ **모니터링은**목록 아래로 더 내려가면 구성 가능한 경고, 메트릭 및 진단 로그를 제공합니다. 필요할 때 만듭니다. 데이터가 수집되고 저장되면 정보를 쿼리하거나 시각화하여 통찰력을 얻을 수 있습니다.

![검색 서비스의 Azure 모니터 통합](./media/search-monitor-usage/azure-monitor-search.png
 "검색 서비스의 Azure 모니터 통합")

### <a name="precision-of-reported-numbers"></a>보고된 수치의 정밀도

포털 페이지는 몇 분마다 새로 고쳐집니다. 따라서 포털에 보고된 숫자는 대략적인 것으로, 시스템이 요청을 얼마나 잘 서비스하고 있는지 를 일반적인 방식으로 파악할 수 있습니다. 초당 쿼리(QPS)와 같은 실제 메트릭은 페이지에 표시된 수치보다 높거나 낮을 수 있습니다.

## <a name="activity-logs-and-service-health"></a>활동 로그 및 서비스 상태

[**활동 로그는**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) Azure 리소스 관리자에서 정보를 수집하고 서비스 상태 변경 에 대한 보고서를 수집합니다. 활동 로그에 서비스 상태와 관련된 중요, 오류 및 경고 조건을 모니터링할 수 있습니다.

쿼리, 인덱싱 또는 개체 만들기와 같은 서비스 내 작업의 경우 각 요청에 대한 *관리 키 받기* 및 쿼리 키 *받기와* 같은 일반적인 정보 알림이 표시되지만 특정 작업 자체는 표시되지 않습니다. 이 그레인에 대한 자세한 내용은 진단 로깅을 구성해야 합니다.

**활성 로그**는 왼쪽 탐색 창, 위쪽 창 명령 모음의 알림 또는 **진단 및 문제 해결** 페이지에서 액세스할 수 있습니다.

## <a name="monitor-storage"></a>모니터 스토리지

개요 페이지에 내장된 탭페이지는 리소스 사용량에 대한 보고서를 작성합니다. 이 정보는 구성이 필요 없는 서비스 사용을 시작하는 즉시 사용할 수 있으며 몇 분마다 페이지가 새로 고쳐집니다. 

[프로덕션 워크로드에 사용할 계층](search-sku-tier.md) 또는 [활성 복제본 및 파티션의 수를 조정할지 여부](search-capacity-planning.md)를 최종적으로 결정하는 경우 이러한 메트릭은 리소스 사용 속도 및 현재 구성의 기존 부하 처리에 대한 효율성을 보여줌으로써 이러한 결정을 내리는 데 도움이 됩니다.

저장소와 관련된 경고는 현재 사용할 수 없습니다. 저장소 소비는 Azure Monitor의 **AzureMetrics** 테이블에 집계또는 로그온되지 않습니다. 코드에서 저장소 크기를 확인하고 응답을 처리하는 리소스 관련 알림을 내처리하는 [사용자 지정 솔루션을 빌드해야](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) 합니다. 저장소 메트릭에 대한 자세한 내용은 [서비스 통계 받기를](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)참조하십시오.

포털에서 시각적으로 모니터링하는 **경우 사용** 탭에는 서비스 계층에서 부과된 현재 [제한을](search-limits-quotas-capacity.md) 기준으로 리소스 가용성이 표시됩니다. 

다음 그림에서는 각 형식의 3개 개체와 50MB의 스토리지로 제한된 체험 서비스를 보여 줍니다. 기본 또는 표준 서비스의 제한이 더 높으며, 파티션 수를 늘리면 최대 스토리지 크기도 비례하여 증가합니다.

![계층 한도에 대한 사용 상태](./media/search-monitor-usage/usage-tab.png
 "계층 한도에 대한 사용 상태")

## <a name="monitor-workloads"></a>워크로드 모니터링

기록된 이벤트에는 인덱싱 및 쿼리와 관련된 이벤트가 포함됩니다. 로그 분석의 **AzureDiagnostics** 테이블은 쿼리 및 인덱싱과 관련된 운영 데이터를 수집합니다.

기록된 데이터의 대부분은 읽기 전용 작업용입니다. 로그에 캡처되지 않은 다른 업데이트 삭제 작업의 경우 검색 서비스에서 시스템 정보를 쿼리할 수 있습니다.

| OperationName | 설명 |
|---------------|-------------|
| 서비스 통계 | 이 작업은 [서비스 통계를 얻기에](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)대한 일상적인 호출로, 직접 또는 암시적으로 호출되어 포털 개요 페이지를 로드하거나 새로 고칠 때 채울 수 있습니다. |
| 쿼리.검색 |  인덱스에 대한 쿼리 요청 은 기록된 쿼리에 대한 정보를 쿼리 [모니터링을](search-monitor-queries.md) 참조하십시오.|
| 인덱싱.인덱스  | 이 작업은 문서 [추가, 업데이트 또는 삭제에](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)대한 호출입니다. |
| 인덱스. 프로토 타입 | 데이터 가져오기 마법사에서 만든 인덱스입니다. |
| 인덱서 만들기 | 데이터 가져오기 마법사를 통해 명시적으로 또는 암시적으로 인덱서를 만듭니다. |
| 인덱서.Get | 인덱서가 실행될 때마다 인덱서의 이름을 반환합니다. |
| 인덱서.상태 | 인덱서가 실행될 때마다 인덱서의 상태를 반환합니다. |
| 데이터 소스.Get | 인덱서가 실행될 때마다 데이터 원본의 이름을 반환합니다.|
| 인덱스.Get | 인덱서가 실행될 때마다 인덱스 이름을 반환합니다. |

### <a name="kusto-queries-about-workloads"></a>워크로드에 대한 Kusto 쿼리

로깅을 사용하도록 설정한 경우 서비스에서 실행된 작업 목록과 시기를 위해 **AzureDiagnostics를** 쿼리할 수 있습니다. 활동의 상관 관계를 파악하여 성능의 변화를 조사할 수도 있습니다.

#### <a name="example-list-operations"></a>예: 작업 목록 

작업 목록과 각 작업 개수를 반환합니다.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>예: 작업 상관 관계

쿼리 요청을 인덱싱 작업과 상호 연관시키고 시간 차트에 걸쳐 데이터 포인트를 렌더링하여 작업이 일치하는지 확인합니다.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>검색 API 사용

Azure 인지 검색 REST API와 .NET SDK 모두 서비스 메트릭, 인덱스 및 인덱서 정보 및 문서 수에 대한 프로그래밍 방식으로 액세스를 제공합니다.

+ [서비스 통계 받기](/rest/api/searchservice/get-service-statistics)
+ [인덱스 통계 받기](/rest/api/searchservice/get-index-statistics)
+ [문서 수 받기](/rest/api/searchservice/count-documents)
+ [인덱서 상태 받기](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>사용자 액세스 모니터링

검색 인덱스는 더 큰 클라이언트 응용 프로그램의 구성 요소이므로 인덱스에 대한 사용자별 액세스를 제어하거나 모니터링하기 위한 기본 제공 방법론이 없습니다. 요청은 관리자 또는 쿼리 요청에 대해 클라이언트 응용 프로그램에서 온 것으로 가정합니다. 관리자 읽기-쓰기 작업에는 전체 서비스에서 개체 만들기, 업데이트, 삭제가 포함됩니다. 읽기 전용 작업은 단일 인덱스로 범위가 되는 문서 컬렉션에 대한 쿼리입니다. 

따라서 활동 로그에서 볼 수 있는 내용은 관리자 키 또는 쿼리 키를 사용하는 호출에 대한 참조입니다. 적절한 키는 클라이언트 코드에서 시작된 요청에 포함됩니다. 서비스는 ID 토큰 또는 가장을 처리할 수 있는 기능이 없습니다.

사용자별 권한 부여에 대한 비즈니스 요구 사항이 있는 경우 권장 사항은 Azure Active Directory와의 통합입니다. $filter 및 사용자 ID를 사용하여 사용자가 볼 수 없는 문서의 [검색 결과를 트리밍할](search-security-trimming-for-azure-search-with-aad.md) 수 있습니다. 

$filter 매개 변수를 포함하는 쿼리 문자열과 별도로 이 정보를 로그온할 수 있는 방법은 없습니다. 보고 쿼리 문자열에 대한 자세한 내용은 [쿼리 모니터를](search-monitor-queries.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure 모니터를 유창하면 Azure 인지 검색과 같은 리소스를 포함하여 모든 Azure 서비스를 감독하는 데 필수적입니다. Azure Monitor에 익숙하지 않은 경우 시간을 내어 리소스와 관련된 문서를 검토합니다. 자습서 외에도 다음 문서는 시작하기에 좋은 장소입니다.

> [!div class="nextstepaction"]
> [Azure 모니터를 통해 Azure 리소스 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
