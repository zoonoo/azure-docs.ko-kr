---
title: Azure Stream Analytics 출력 문제 해결
description: 이 문서에서는 Azure Stream Analytics 작업에서 출력 연결의 문제를 해결하는 기술에 대해 설명합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: bee40bc30a0ffbdacf8cc7bf88d1512c4fc43fa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147942"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics 출력 문제 해결

이 문서에서는 Azure Stream Analytics 출력 연결과 관련 된 일반적인 문제 및 출력 문제를 해결 하는 방법을 설명 합니다. 많은 문제 해결 단계를 수행 하려면 리소스 및 기타 진단 로그를 Stream Analytics 작업에 사용 하도록 설정 해야 합니다. 리소스 로그를 사용 하도록 설정 하지 않은 경우 [리소스 로그를 사용 하 여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)을 참조 하세요.

## <a name="the-job-doesnt-produce-output"></a>작업에서 출력을 생성 하지 않습니다.

1. 각 출력에 대해 **테스트 연결** 단추를 사용하여 출력에 대한 연결을 확인합니다.
1. **모니터** 탭에서 [모니터링 메트릭](stream-analytics-monitoring.md) 을 확인 합니다. 값이 집계 되므로 메트릭은 몇 분 정도 지연 됩니다.

   * **입력 이벤트** 값이 0 보다 크면 작업에서 입력 데이터를 읽을 수 있습니다. **입력 이벤트** 값이 0 보다 크지 않은 경우 작업의 입력에 문제가 있습니다. 자세한 내용은 [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md) 을 참조 하세요.
   * **데이터 변환 오류** 값이 0 보다 크고 증가 하는 경우 데이터 변환 오류에 대 한 자세한 내용은 [데이터 오류 Azure Stream Analytics](data-errors.md) 를 참조 하세요.
   * **런타임 오류** 값이 0 보다 크면 작업에서 데이터를 받지만 쿼리를 처리 하는 동안 오류를 생성 합니다. 오류를 찾으려면 [감사 로그](../azure-resource-manager/management/view-activity-logs.md)로 이동한 다음 **실패** 상태를 필터링 합니다.
   * **입력 이벤트** 값이 0 보다 크고 **출력 이벤트** 값이 0 이면 다음 문 중 하나가 true가 됩니다.
      * 쿼리를 처리 하는 동안 출력 이벤트가 0으로 나타났습니다.
      * 이벤트 또는 필드의 형식이 잘못 되어 쿼리 처리 후 출력은 0이 될 수 있습니다.
      * 작업이 연결 또는 인증 이유로 데이터를 출력 싱크에 푸시할 수 없습니다.

   작업 로그 메시지는 쿼리 논리가 모든 이벤트를 필터링 하는 경우를 제외 하 고 발생 하는 상황을 포함 하 여 추가 세부 정보를 설명 합니다. 여러 이벤트 처리에서 오류가 생성 되는 경우 오류는 10 분 마다 집계 됩니다.

## <a name="the-first-output-is-delayed"></a>첫 번째 출력이 지연 됩니다.

Stream Analytics 작업이 시작 되 면 입력 이벤트를 읽습니다. 그러나 특정 상황에서는 출력에 지연이 있을 수 있습니다.

temporal 쿼리 요소의 시간 값이 크면 출력이 지연의 원인이 될 수 있습니다. 많은 시간 동안 올바른 출력을 생성 하기 위해 스트리밍 작업은 시간 창을 채울 수 있는 가장 늦은 시간에서 데이터를 읽습니다. 데이터는 최대 7 일까 지 사용할 수 있습니다. 처리 중인 입력 이벤트를 읽을 때까지 출력이 생성 되지 않습니다. 이 문제는 시스템에서 스트리밍 작업을 업그레이드 하는 경우에 발생할 수 있습니다. 업그레이드를 수행 하면 작업이 다시 시작 됩니다. 이러한 업그레이드는 일반적으로 몇 달에 한 번 발생합니다.

Stream Analytics 쿼리를 디자인할 때 신중 하 게 사용 합니다. 작업의 쿼리 구문에서 temporal 요소에 대해 긴 기간을 사용 하는 경우 작업이 시작 되거나 다시 시작 될 때 첫 번째 출력에서 지연이 발생할 수 있습니다. 여러 시간 (최대 7 일)은 많은 시간으로 간주 됩니다.

이러한 종류의 첫 번째 출력 지연에 대 한 한 가지 완화 방법은 데이터 분할과 같은 쿼리 병렬 처리 기법을 사용 하는 것입니다. 또는 작업이 완료 될 때까지 처리량을 향상 시키기 위해 스트리밍 단위를 더 추가할 수 있습니다.  자세한 내용은 [Stream Analytics 작업을 만들 때의 고려 사항](stream-analytics-concepts-checkpoint-replay.md)을 참조 하세요.

이러한 요소는 첫 번째 출력의 적시성 영향을 받습니다.

* 연속, 도약 및 슬라이딩 윈도우의 GROUP BY 절과 같은 기간 이동 집계를 사용 합니다.

  * 연속 또는 도약 창 집계의 경우 결과는 창 기간이 끝날 때 생성 됩니다.
  * 슬라이딩 윈도우의 경우 이벤트가 슬라이딩 윈도우를 들어가거나 나갈 때 결과가 생성 됩니다.
  * 1 시간 이상 크기의 큰 창 크기를 사용 하려는 경우 도약 또는 슬라이딩 윈도우를 선택 하는 것이 가장 좋습니다. 이러한 창 유형을 사용 하면 출력을 더 자주 볼 수 있습니다.

* DATEDIFF를 사용한 조인과 같은 임시 조인 사용:
  * 일치 하는 이벤트의 양쪽에 도달 하면 즉시 생성 됩니다.
  * 왼쪽 우선 외부 조인과 같이 일치 항목이 없는 데이터는 왼쪽의 각 이벤트에 대해 DATEDIFF 창의 끝에 생성 됩니다.

* ISFIRST, LAST 및 LAG와 같은 임시 분석 함수를 사용 하는 경우 제한 시간이 사용 됩니다.
  * 분석 함수의 경우 모든 이벤트에 대해 출력이 생성 됩니다. 지연은 없습니다.

## <a name="the-output-falls-behind"></a>출력이 뒤에 있습니다.

작업의 정상적인 작업 중에는 출력에 대기 시간이 더 오래 걸릴 수 있습니다. 출력이 다음과 같이 줄어들면 다음과 같은 요인을 검사 하 여 근본 원인을 파악할 수 있습니다.

* 다운스트림 싱크의 제한 여부
* 업스트림 소스의 제한 여부
* 쿼리의 처리 논리에 계산이 많이 사용되는지 여부

출력 세부 정보를 보려면 Azure Portal에서 스트리밍 작업을 선택한 다음 **작업 다이어그램**을 선택 합니다. 각 입력에 대해 파티션당 백로그 이벤트 메트릭이 있습니다. 메트릭이 계속 유지 되는 경우 시스템 리소스가 제한 됨을 나타내는 표시기입니다. 증가는 출력 싱크 제한 또는 높은 CPU 사용량으로 인해 발생할 수 있습니다. 자세한 내용은 [작업 다이어그램을 사용 하 여 데이터 기반 디버깅](stream-analytics-job-diagram-with-metrics.md)을 참조 하세요.

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database 출력을 사용하여 키 위반 경고

Stream Analytics 작업에 대 한 출력으로 Azure SQL database를 구성 하는 경우 대상 테이블에 레코드를 대량 삽입 합니다. 일반적으로 Azure Stream Analytics는 출력 싱크로 [최소 한 번 배달](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) 을 보장 합니다. SQL 테이블에 unique 제약 조건이 정의 되어 있는 경우에도 SQL 출력에 대해 [정확히 한 번 배달]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) 을 수행할 수 있습니다.

SQL 테이블에 unique key 제약 조건을 설정 하면 Azure Stream Analytics에서 중복 레코드를 제거 합니다. 데이터를 일괄 처리로 분할 하 고 단일 중복 레코드가 발견 될 때까지 일괄 처리를 재귀적으로 삽입 합니다. 분할 및 삽입 프로세스는 한 번에 하나씩 중복 항목을 무시 합니다. 중복 행이 많은 스트리밍 작업의 경우 프로세스는 비효율적 이며 시간이 많이 소요 됩니다. 활동 로그에 이전 시간에 대 한 여러 키 위반 경고 메시지가 표시 되는 경우 SQL 출력이 전체 작업 속도를 저하 시킬 수 있습니다.

이 문제를 해결 하려면 IGNORE_DUP_KEY 옵션을 사용 하도록 설정 하 여 키 위반을 유발 하는 [인덱스를 구성]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) 합니다. 이 옵션을 사용 하면 대량 삽입 중에 SQL에서 중복 값을 무시할 수 있습니다. Azure SQL Database는 단순히 오류 대신 경고 메시지를 생성 합니다. 따라서 Azure Stream Analytics는 더 이상 기본 키 위반 오류를 생성 하지 않습니다.

몇 가지 유형의 인덱스에서 IGNORE_DUP_KEY를 구성할 때 다음 조사 내용을 적어둡니다.

* ALTER INDEX를 사용 하는 unique 제약 조건 또는 기본 키에 IGNORE_DUP_KEY를 설정할 수 없습니다. 인덱스를 삭제 하 고 다시 만들어야 합니다.  
* 고유 인덱스에 대해 ALTER INDEX를 사용 하 여 IGNORE_DUP_KEY를 설정할 수 있습니다. 이 인스턴스는 PRIMARY KEY/UNIQUE 제약 조건과 다르며 CREATE INDEX 또는 INDEX 정의를 사용 하 여 생성 됩니다.  
* IGNORE_DUP_KEY 옵션은 고유성을 적용할 수 없으므로 열 저장소 인덱스에는 적용 되지 않습니다.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>열 이름은 Azure Stream Analytics의 소문자 (1.0)입니다.

원래 호환성 수준 (1.0)을 사용 하는 경우 Azure Stream Analytics 열 이름을 소문자로 변경 합니다. 이 동작은 이후 호환성 수준에서 수정 되었습니다. 사례를 유지 하려면 호환성 수준 1.1 이상으로 이동 합니다. 자세한 내용은 [Stream Analytics 작업에 대 한 호환성 수준](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)을 참조 하세요.

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
