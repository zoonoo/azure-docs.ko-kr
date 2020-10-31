---
title: Azure Stream Analytics에서 Machine Learning Studio (클래식) 함수 확장
description: 이 문서에서는 분할 및 스트림 단위를 구성 하 여 Machine Learning Studio (클래식) 함수를 사용 하는 Stream Analytics 작업의 크기를 조정 하는 방법을 설명 합니다.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: feeb709f67a0e75f5980ec0520b95feb7edd5960
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124410"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Azure Machine Learning Studio(클래식) 함수를 사용하여 Stream Analytics 작업의 크기 조정

> [!TIP]
> 성능 및 안정성 향상을 위해 Azure Machine Learning Studio(클래식) UDF 대신 [Azure Machine Learning UDF](machine-learning-udf.md)를 사용하는 것이 좋습니다.

이 문서에서는 Azure Machine Learning Studio (클래식) 함수를 사용 하는 Azure Stream Analytics 작업을 효율적으로 확장 하는 방법을 설명 합니다. Stream Analytics 작업의 크기를 조정하는 일반적인 방법은 [작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Stream Analytics의 스튜디오 (클래식) 함수는 무엇 인가요?

Stream Analytics의 Machine Learning Studio (클래식) 함수는 Stream Analytics 쿼리 언어의 일반 함수 호출 처럼 사용할 수 있습니다. 그러나 내부적으로 이러한 함수 호출은 실제로 Studio (클래식) 웹 서비스 요청입니다.

동일한 웹 서비스 API 호출에서 여러 행을 함께 "일괄 처리" 하 여 Studio (클래식) 웹 서비스 요청의 처리량을 향상 시킬 수 있습니다. 이러한 그룹화를 미니 매치라고 합니다. 자세한 내용은 [Azure Machine Learning Studio(클래식) 웹 서비스](../machine-learning/classic/consume-web-services.md)를 참조하세요. Stream Analytics에서 Studio (클래식)에 대 한 지원은 미리 보기 상태입니다.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Studio (클래식) 함수를 사용 하 여 Stream Analytics 작업 구성

Stream Analytics 작업에서 사용 하는 Studio (클래식) 함수를 구성 하는 두 가지 매개 변수가 있습니다.

* Studio (클래식) 함수 호출의 일괄 처리 크기입니다.
* Stream Analytics 작업에 대해 프로비저닝된 SU(스트리밍 단위)의 수.

SU의 적절한 값을 정하려면 Stream Analytics 작업의 대기 시간을 최적화할지 아니면 각 SU의 처리량을 최적화할지 판단하세요. SU는 잘 파티션된 Stream Analytics 쿼리의 처리량을 개선하기 위해 작업에 항상 추가할 수 있습니다. 추가 SU가 있으면 작업 실행에 소요되는 비용이 늘어납니다.

Stream Analytics 작업의 대기 시간 ‘허용 범위’를 정합니다. 일괄 처리 크기를 늘리면 스튜디오 (클래식) 요청의 대기 시간과 Stream Analytics 작업의 대기 시간이 증가 합니다.

일괄 처리 크기를 높이면 Stream Analytics 작업에서 **동일한 수** 의 Studio (클래식) 웹 서비스 요청으로 **더 많은 이벤트** 를 처리할 수 있습니다. 스튜디오 (클래식) 웹 서비스 대기 시간은 일반적으로 일괄 처리 크기의 증가에 sublinear 됩니다. 

지정 된 상황에서 Studio (클래식) 웹 서비스에 대 한 가장 비용 효율적인 일괄 처리 크기를 고려 하는 것이 중요 합니다. 웹 서비스 요청의 기본 배치 크기는 1000입니다. 이 기본값은 [Stream Analytics REST API](/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") 또는 [Stream Analytics용 PowerShell 클라이언트](stream-analytics-monitor-and-manage-jobs-use-powershell.md)를 사용하여 변경할 수 있습니다.

배치 크기를 정했으면 함수가 초당 처리해야 하는 이벤트 수를 기준으로 SU(스트리밍 단위)의 수를 설정할 수 있습니다. 스트리밍 단위에 대한 자세한 내용은 [Stream Analytics 크기 조정 작업](stream-analytics-scale-jobs.md)을 참조하세요.

6 개 SUs 마다 스튜디오 (클래식) 웹 서비스에 대 한 동시 연결 20 개를 가져옵니다. 그러나 1개의 SU 작업과 3개의 SU 작업은 20개의 동시 연결을 갖습니다.  

애플리케이션이 초당 200,000개의 이벤트를 생성하고 배치 크기가 1000이라면 웹 서비스 대기 시간은 200ms가 됩니다. 이 요금은 모든 연결이 초당 Studio (클래식) 웹 서비스에 대 한 5 개의 요청을 만들 수 있음을 의미 합니다. 연결이 20개이면 Stream Analytics 작업은 200ms 동안 20,000개의 이벤트를 처리할 수 있으므로 초당 100,000개의 이벤트를 처리할 수 있습니다.

초당 200,000개의 이벤트를 처리하려면 Stream Analytics 작업에 동시 연결 40개가 필요하고, 12개의 SU가 필요하다는 결론이 나옵니다. 다음 다이어그램은 Stream Analytics 작업에서 Studio (클래식) 웹 서비스 끝점으로 요청 하는 것을 보여 줍니다. 모든 6 개의 SUs에는 최대 Studio (클래식) 웹 서비스에 대 한 동시 연결 수가 20 개 있습니다.

![Studio (클래식) 함수를 사용 하 여 Stream Analytics 확장 두 작업 예제](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Studio (클래식) 함수를 사용 하 여 Stream Analytics 확장 두 작업 예제")

일반적으로 배치 크기의 경우 * *_b_* _, 일괄 처리 크기 B의 웹 서비스 대기 시간에 대 한 _*_L_*_ (밀리초) _*_을 사용 하_*_ 는 Stream Analytics 작업의 처리량은 다음과 같습니다.

![Studio (클래식) 함수 수식을 사용 하 여 Stream Analytics 확장](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Studio (클래식) 함수 수식을 사용 하 여 Stream Analytics 확장")

스튜디오 (클래식) 웹 서비스에서 ' 최대 동시 호출 수 '를 구성할 수도 있습니다. 이 매개 변수는 최댓값(현재 200)으로 설정하는 것이 좋습니다.

이 설정에 대 한 자세한 내용은 [Machine Learning Studio (클래식) 웹 서비스에 대 한 크기 조정 문서](../machine-learning/classic/create-endpoint.md)를 참조 하세요.

## <a name="example--sentiment-analysis"></a>예 – 정서 분석
다음 예제에는 [Stream Analytics Machine Learning Studio (클래식) 통합 자습서](stream-analytics-machine-learning-integration-tutorial.md)에 설명 된 대로 감정 analysis Studio (클래식) 함수를 사용 하는 Stream Analytics 작업이 포함 되어 있습니다.

쿼리는 다음 예제와 같이 완전히 분할 된 간단한 쿼리 다음에 _ *감정* * 함수를 통해 수행 됩니다.

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

초당 10,000개의 트윗을 대상으로 감정 분석을 수행하는 Stream Analytics 작업을 만드는 데 필요한 구성을 살펴보겠습니다.

1개의 SU를 사용할 경우 이 Stream Analytics 작업에서 트래픽을 처리할 수 있을까요? 이 작업은 기본 배치 크기 1000을 사용하여 입력을 처리할 수 있습니다. 기본 일괄 처리 크기가 1000 인 감정 analysis Studio (클래식) 웹 서비스의 기본 대기 시간은 대기 시간 중 초를 초과 하지 않습니다.

Stream Analytics 작업의 **전체적인** 또는 엔드투엔드 대기 시간은 일반적으로 몇 초입니다. 이 Stream Analytics 작업, *특히* Studio (클래식) 함수 호출에 대해 좀 더 자세히 살펴보겠습니다. 배치 크기가 1000이면 10,000개의 이벤트를 처리하려면 웹 서비스로 약 10개의 요청을 보내야 합니다. 1SU를 사용하더라도 이 입력 트래픽을 처리하기에 충분한 동시 연결이 있습니다.

입력 이벤트 속도가 100배 증가하면 Stream Analytics 작업은 초당 1,000,000 트윗을 처리해야 합니다. 크기 증가를 위해 다음 두 가지 옵션을 사용할 수 있습니다.

1. 배치 크기를 늘립니다.
2. 입력 스트림을 분할하여 이벤트를 병렬로 처리합니다.

첫 번째 옵션을 사용하면 작업 **대기 시간** 이 증가합니다.

두 번째 옵션을 사용 하는 경우 더 많은 동시 스튜디오 (클래식) 웹 서비스 요청을 사용 하려면 더 많은 su를 프로 비전 해야 합니다. 이처럼 SU 수를 늘리면 작업 **비용** 이 늘어납니다.

각 배치 크기의 대기 시간 측정값을 사용하여 크기 조정을 살펴보겠습니다.

| 대기 시간 | Batch 크기 |
| --- | --- |
| 200ms | 1000개 이하의 이벤트로 구성된 배치 |
| 250ms | 5,000개 이벤트로 구성된 배치 |
| 300ms | 10,000개 이벤트로 구성된 배치 |
| 500ms | 25,000개 이벤트로 구성된 배치 |

1. 첫 번째 옵션을 사용하면(더 많은 SU를 프로비전하지 **않음** ) 일괄 처리 크기가 **25,000** 으로 늘어날 수 있습니다. 이러한 방식으로 일괄 처리 크기를 높이면 작업에서 Studio (클래식) 웹 서비스에 대 한 20 개의 동시 연결을 사용 하 여 100만 이벤트를 처리할 수 있습니다 (호출 당 500 밀리초 지연). 따라서 Studio (클래식) 웹 서비스 요청에 대 한 감정 함수 요청으로 인해 Stream Analytics 작업의 추가 대기 시간이 **200 밀리초** 에서 **500 밀리초** 로 증가 합니다. 그러나 Studio (클래식) 웹 서비스에서 요청 하는 페이로드 크기는 4mb이 하 여야 하 고 작업의 100 초 후에 웹 서비스 요청 시간이 초과 되므로 일괄 처리 크기를 무한정 늘릴 수 **없습니다** .
1. 두 번째 옵션을 사용할 경우 배치 크기는 1000으로 유지되고 웹 서비스 대기 시간이 200ms이므로, 웹 서비스에 대한 동시 연결 20개마다 1000 * 20 * 5 이벤트 = 초당 100,000개 이벤트를 처리할 수 있습니다. 따라서 초당 1,000,000 이벤트를 처리하려면 작업에 60SU가 필요합니다. 첫 번째 옵션과 비교하면, Stream Analytics 작업에서 웹 서비스 배치 요청을 더 많이 만들며, 그로 인해 비용도 함께 증가합니다.

아래는 SU 및 배치 크기(초당 이벤트 수)에 따른 Stream Analytics 작업의 처리량을 보여 주는 테이블입니다.

| 배치 크기(ML 대기 시간) | 500(200ms) | 1,000(200ms) | 5,000(250ms) | 10,000(300ms) | 25,000(500ms) |
| --- | --- | --- | --- | --- | --- |
| **1SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12SU** |5,000 |10000 |40,000 |60,000 |100,000 |
| **18SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24SU** |10000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

이제 Stream Analytics의 Studio (클래식) 함수가 작동 하는 방식을 이미 잘 알고 있어야 합니다. 또한 Stream Analytics 작업이 데이터 원본에서 데이터를 “끌어오며” 각 “끌어오기”는 Stream Analytics 작업에서 처리할 이벤트 일괄 처리를 반환한다는 내용도 이해하셨을 것입니다. 이 끌어오기 모델은 Studio (클래식) 웹 서비스 요청에 어떤 영향을 미칩니까?

일반적으로 Studio (클래식) 함수에 대해 설정 하는 일괄 처리 크기는 각 Stream Analytics 작업 "끌어오기"에서 반환 되는 이벤트 수로 정확 하 게 나눌 수 없습니다. 이 경우 Studio (클래식) 웹 서비스는 "부분" 일괄 처리를 사용 하 여 호출 됩니다. 부분 배치를 사용하면 끌어오기 간에 이벤트를 병합할 때 추가 작업 대기 시간 오버헤드가 발생하지 않습니다.

## <a name="new-function-related-monitoring-metrics"></a>새 함수 관련 모니터링 메트릭
Stream Analytics 작업의 모니터링 영역에 함수 관련 메트릭 세 개가 추가되었습니다. 이 세 개 함수는 아래 그림에 나와 있는 것처럼 **FUNCTION REQUESTS** , **FUNCTION EVENTS** 및 **FAILED FUNCTION REQUESTS** 입니다.

![Studio (클래식) 함수 메트릭을 사용 하 여 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Studio (클래식) 함수 메트릭을 사용 하 여 Stream Analytics 크기 조정")

함수는 아래와 같이 정의됩니다.

**FUNCTION REQUESTS** : 함수 요청의 수

**FUNCTION EVENTS** : 함수 요청의 이벤트 수

**FAILED FUNCTION REQUESTS** : 실패한 함수 요청의 수

## <a name="key-takeaways"></a>핵심 내용

Studio (클래식) 함수를 사용 하 여 Stream Analytics 작업의 크기를 조정 하려면 다음 요소를 고려 합니다.

1. 입력 이벤트 속도
2. 실행 중인 Stream Analytics 작업에 대해 허용 되는 대기 시간 (따라서 스튜디오 (클래식) 웹 서비스 요청의 일괄 처리 크기)입니다.
3. 프로 비전 된 Stream Analytics su와 Studio (클래식) 웹 서비스 요청 수입니다 (추가 함수 관련 비용).

이 예에서는 완전하게 분할된 Stream Analytics 쿼리가 사용되었습니다. 보다 복잡한 쿼리가 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)에서 Stream Analytics 팀의 추가 도움을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)