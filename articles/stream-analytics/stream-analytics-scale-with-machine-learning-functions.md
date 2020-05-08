---
title: Azure Stream Analytics에서 Machine Learning 함수 크기 조정
description: 이 아티클에서는 분할 및 스트림 단위를 구성하여 Microsoft Azure Machine Learning 함수를 사용하는 Stream Analytics 작업의 크기를 조정하는 방법을 설명합니다.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4e2c99274df90d232754c862f3174b5767392cec
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929046"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Azure Machine Learning Studio (클래식) 함수를 사용 하 여 Stream Analytics 작업 크기 조정

> [!TIP]
> 성능 및 안정성 향상을 위해 클래식 UDF Azure Machine Learning Studio 대신 [Azure Machine Learning udf](machine-learning-udf.md) 를 사용 하는 것이 좋습니다.

이 문서에서는 Azure Machine Learning 함수를 사용 하는 Azure Stream Analytics 작업을 효율적으로 확장 하는 방법을 설명 합니다. Stream Analytics 작업의 크기를 조정하는 일반적인 방법은 [작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics의 Azure Machine Learning 함수란 무엇입니까?

Stream Analytics의 Machine Learning 함수는 Stream Analytics 쿼리 언어에 일반 함수 호출처럼 사용할 수 있습니다. 그러나 내부적으로 이러한 함수 호출은 웹 서비스 요청 Azure Machine Learning 됩니다.

동일한 웹 서비스 API 호출에서 여러 행을 함께 "일괄 처리" 하 여 Machine Learning 웹 서비스 요청의 처리량을 향상 시킬 수 있습니다. 이 그룹화를 미니 일괄 처리 라고 합니다. 자세한 내용은 [Azure Machine Learning Studio (클래식) 웹 서비스](../machine-learning/studio/consume-web-services.md)를 참조 하세요. Stream Analytics에서 Azure Machine Learning Studio (클래식)에 대 한 지원은 미리 보기 상태입니다.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning 함수를 사용하여 Stream Analytics 작업 구성

Stream Analytics 작업에서 사용 하는 Machine Learning 함수를 구성 하는 두 가지 매개 변수가 있습니다.

* Machine Learning 함수 호출의 일괄 처리 크기입니다.
* Stream Analytics 작업에 대해 프로 비전 된 su (스트리밍 단위) 수입니다.

SUs에 적절 한 값을 결정 하려면 Stream Analytics 작업의 대기 시간 또는 각 SU의 처리량을 최적화할지 여부를 결정 합니다. 항상 작업에 SUs를 추가 하 여 잘 분할 된 Stream Analytics 쿼리의 처리량을 높일 수 있습니다. 추가 SUs는 작업 실행 비용을 증가 시킵니다.

Stream Analytics 작업에 대 한 대기 시간 *허용 오차* 를 확인 합니다. 일괄 처리 크기를 늘리면 Azure Machine Learning 요청 대기 시간과 Stream Analytics 작업의 대기 시간이 증가 합니다.

일괄 처리 크기를 높이면 Stream Analytics 작업에서 **동일한 수** 의 Machine Learning 웹 서비스 요청으로 **더 많은 이벤트** 를 처리할 수 있습니다. Machine Learning 웹 서비스 대기 시간은 일반적으로 일괄 처리 크기의 증가에 sublinear 됩니다. 

지정 된 상황에서 Machine Learning 웹 서비스에 대 한 가장 비용 효율적인 일괄 처리 크기를 고려 하는 것이 중요 합니다. 웹 서비스 요청에 대 한 기본 일괄 처리 크기는 1000입니다. [Stream Analytics에 대 한](stream-analytics-monitor-and-manage-jobs-use-powershell.md) [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") 또는 PowerShell 클라이언트를 사용 하 여이 기본 크기를 변경할 수 있습니다.

일괄 처리 크기를 결정 한 후에는 함수에서 초당 처리할 이벤트 수에 따라 su (스트리밍 단위) 수를 설정할 수 있습니다. 스트리밍 단위에 대한 자세한 내용은 [Stream Analytics 크기 조정 작업](stream-analytics-scale-jobs.md)을 참조하세요.

6 개 SUs 마다 Machine Learning 웹 서비스에 대 한 동시 연결 20 개를 가져옵니다. 그러나 1 개 SU 작업 및 3 개의 SU 작업은 20 개의 동시 연결을 가져옵니다.  

응용 프로그램이 초당 20만 이벤트를 생성 하 고 일괄 처리 크기가 1000 이면 결과 웹 서비스 대기 시간은 200 밀리초입니다. 이 요금은 모든 연결이 초당 Machine Learning 웹 서비스에 대해 5 개의 요청을 수행할 수 있음을 의미 합니다. 20 개의 연결을 사용 하면 Stream Analytics 작업에서 200 밀리초의 2만 이벤트와 10만 이벤트를 초당 처리할 수 있습니다.

초당 20만 이벤트를 처리 하려면 Stream Analytics 작업에 40 동시 연결이 필요 하며,이는 12 개의 SUs로 제공 됩니다. 다음 다이어그램은 Stream Analytics 작업에서 Machine Learning 웹 서비스 엔드포인트로 전송되는 요청을 보여줍니다. 6SU마다 Machine Learning 웹 서비스에 대한 최대 동시 연결 20개가 있습니다.

![Machine Learning 함수를 사용 하 여 Stream Analytics 확장 두 작업 예제](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Machine Learning 함수를 사용 하 여 Stream Analytics 확장 두 작업 예제")

일반적으로 배치 크기가 ***B***이고, 배치 크기 B의 웹 서비스 대기 시간이 ***L***밀리초이면 SU ***N***개가 포함된 Stream Analytics 작업의 처리량은 다음과 같습니다.

![Machine Learning 함수 수식을 사용하여 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning 함수 수식을 사용하여 Stream Analytics 크기 조정")

Machine Learning 웹 서비스에서 ' 최대 동시 호출 수 '를 구성할 수도 있습니다. 이 매개 변수를 최 댓 값 (현재 200)으로 설정 하는 것이 좋습니다.

이 설정에 대한 자세한 내용은 [Machine Learning 웹 서비스와 관련된 크기 조정 문서](../machine-learning/studio/create-endpoint.md)를 참조하세요.

## <a name="example--sentiment-analysis"></a>예 – 정서 분석
다음 예제는 [Stream Analytics Machine Learning 통합 자습서](stream-analytics-machine-learning-integration-tutorial.md)에 설명된 대로 정서 분석 Machine Learning 함수를 사용하는 Stream Analytics 작업을 포함하고 있습니다.

다음 예제에 표시된 대로 완전히 분할된 간단한 쿼리 다음에 **sentiment** 함수가 옵니다.

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

1만 트 윗 초당 감정 분석을 수행 하는 Stream Analytics 작업을 만드는 데 필요한 구성을 살펴보겠습니다.

1 개 SU를 사용 하 여이 Stream Analytics 작업에서 트래픽을 처리할 수 있나요? 작업은 기본 일괄 처리 크기인 1000를 사용 하 여 입력을 처리할 수 있습니다. 감정 analysis Machine Learning 웹 서비스의 기본 대기 시간 (기본 일괄 처리 크기인 1000)은 대기 시간 중 초를 초과 하지 않습니다.

Stream Analytics 작업의 **전체** 또는 종단 간 대기 시간은 일반적으로 몇 초입니다. 이 Stream Analytics 작업, *특히* Machine Learning 함수 호출에 대해 좀 더 자세히 살펴보겠습니다. 1000의 일괄 처리 크기를 사용 하는 경우 1만 이벤트 처리량은 웹 서비스에 대 한 요청을 10 개 사용 합니다. 1SU를 사용하더라도 이 입력 트래픽을 처리하기에 충분한 동시 연결이 있습니다.

입력 이벤트 속도가 100배 증가하면 Stream Analytics 작업은 초당 1,000,000 트윗을 처리해야 합니다. 크기 증가를 위해 다음 두 가지 옵션을 사용할 수 있습니다.

1. 일괄 처리 크기를 늘립니다.
2. 입력 스트림을 분할 하 여 이벤트를 병렬로 처리 합니다.

첫 번째 옵션을 사용하면 작업 **대기 시간**이 증가합니다.

두 번째 옵션을 사용 하면 더 많은 동시 Machine Learning 웹 서비스 요청을 사용 하기 위해 더 많은 SUs를 프로 비전 해야 합니다. 이와 같이 많은 수의 SUs를 활용 하면 작업 **비용이**늘어납니다.

각 일괄 처리 크기에 대해 다음과 같은 대기 시간 측정값을 사용 하 여 크기 조정을 살펴보겠습니다.

| 대기 시간 | Batch 크기 |
| --- | --- |
| 200밀리초 | 1000-이벤트 일괄 처리 또는 미만 |
| 250 밀리초 | 5000-이벤트 일괄 처리 |
| 300 밀리초 | 1만-이벤트 일괄 처리 |
| 500ms | 25000-이벤트 일괄 처리 |

1. 첫 번째 옵션을 사용하면(더 많은 SU를 프로비전하지 **않음**) 일괄 처리 크기가 **25,000**으로 늘어날 수 있습니다. 이러한 방식으로 일괄 처리 크기를 높이면 작업에서 Machine Learning 웹 서비스에 대 한 20 개의 동시 연결을 사용 하 여 100만 이벤트를 처리할 수 있습니다 (호출 당 500 밀리초의 대기 시간). Machine Learning 웹 서비스 요청에 대한 감정 함수 요청으로 인해 Stream Analytics 작업의 대기 시간이 **200ms**에서 **500ms**로 증가합니다. 그러나 Machine Learning 웹 서비스에서 요청 하는 페이로드 크기는 4mb이 하 여야 하 고 100 초 후에 웹 서비스 요청 시간이 초과 되 면 일괄 처리 크기를 무한정 늘릴 수 **없습니다** .
1. 두 번째 옵션을 사용할 경우 배치 크기는 1000으로 유지되고 웹 서비스 대기 시간이 200ms이므로, 웹 서비스에 대한 동시 연결 20개마다 1000 * 20 * 5 이벤트 = 초당 100,000개 이벤트를 처리할 수 있습니다. 따라서 초당 1,000,000 이벤트를 처리하려면 작업에 60SU가 필요합니다. 첫 번째 옵션과 비교하면, Stream Analytics 작업에서 웹 서비스 배치 요청을 더 많이 만들며, 그로 인해 비용도 함께 증가합니다.

아래는 SU 및 배치 크기(초당 이벤트 수)에 따른 Stream Analytics 작업의 처리량을 보여 주는 테이블입니다.

| 배치 크기(ML 대기 시간) | 500(200ms) | 1,000(200ms) | 5,000(250ms) | 10,000(300ms) | 25,000(500ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12SU** |5,000 |10000 |40,000 |60,000 |100,000 |
| **18SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24SU** |10000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

이제 Stream Analytics 작업에서 Machine Learning 함수가 어떻게 작동하는지 충분히 이해하셨을 것입니다. 또한 Stream Analytics 작업이 데이터 원본에서 데이터를 “끌어오며” 각 “끌어오기”는 Stream Analytics 작업에서 처리할 이벤트 일괄 처리를 반환한다는 내용도 이해하셨을 것입니다. 이 끌어오기 모델이 Machine Learning 웹 서비스 요청에 어떤 영향을 미칠까요?

일반적으로 Machine Learning 함수에 대해 설정 하는 일괄 처리 크기는 각 Stream Analytics 작업 "끌어오기"에서 반환 된 이벤트 수로 정확 하 게 나눌 수 없습니다. 이와 같은 경우 Machine Learning 웹 서비스가 “부분” 배치를 통해 호출됩니다. 부분 일괄 처리를 사용 하면 끌어오기에서 끌어오기로 이벤트를 결합할 때 추가 작업 대기 시간 오버 헤드가 발생 하지 않습니다.

## <a name="new-function-related-monitoring-metrics"></a>새 함수 관련 모니터링 메트릭
Stream Analytics 작업의 모니터링 영역에 함수 관련 메트릭 세 개가 추가되었습니다. 아래 그림과 같이 **함수 요청**, **함수 이벤트** 및 **실패 한 함수 요청**입니다.

![Machine Learning 함수 메트릭을 사용하여 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning 함수 메트릭을 사용하여 Stream Analytics 크기 조정")

함수는 아래와 같이 정의됩니다.

**FUNCTION REQUESTS**: 함수 요청의 수

**FUNCTION EVENTS**: 함수 요청의 이벤트 수

**FAILED FUNCTION REQUESTS**: 실패한 함수 요청의 수.

## <a name="key-takeaways"></a>핵심 내용

Machine Learning 함수를 사용 하 여 Stream Analytics 작업의 크기를 조정 하려면 다음 요소를 고려 하십시오.

1. 입력 이벤트 율입니다.
2. 실행 중인 Stream Analytics 작업에 대해 허용 되는 대기 시간 (따라서 Machine Learning 웹 서비스 요청의 일괄 처리 크기)입니다.
3. 프로 비전 된 Stream Analytics Machine Learning 웹 서비스 요청 (추가 함수 관련 비용)의 수입니다.

이 예에서는 완전하게 분할된 Stream Analytics 쿼리가 사용되었습니다. 보다 복잡한 쿼리가 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)에서 Stream Analytics 팀에게 추가 도움을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
