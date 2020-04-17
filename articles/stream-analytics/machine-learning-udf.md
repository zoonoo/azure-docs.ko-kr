---
title: Azure 스트림 분석을 Azure 기계 학습과 통합
description: 이 문서에서는 Azure Stream Analytics 작업을 Azure 기계 학습 모델과 통합하는 방법을 설명합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481982"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure 스트림 분석을 Azure 기계 학습과 통합(미리 보기)

Azure Stream Analytics 작업에서 사용자 정의 함수(UDF)로 기계 학습 모델을 구현하여 스트리밍 입력 데이터에 대한 실시간 채점 및 예측을 수행할 수 있습니다. [Azure 기계 학습을](../machine-learning/overview-what-is-azure-ml.md) 사용하면 Tensorflow, scikit-learn 또는 PyTorch와 같은 인기 있는 모든 오픈 소스 도구를 사용하여 모델을 준비, 학습 및 배포할 수 있습니다.

> [!NOTE]
> 이 기능은 공개 미리 보기입니다. Azure 포털에서 이 기능에 액세스할 수 있는 [것은 [검색 분석] 포털 미리 보기 링크를](https://aka.ms/asaportalpreview)사용 하 여. 이 기능은 [Visual Studio에 대한 최신](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)버전의 스트림 분석 도구에서도 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

스트림 분석 작업에 기계 학습 모델을 함수로 추가하기 전에 다음 단계를 완료합니다.

1. Azure 기계 학습을 사용하여 [모델을 웹 서비스로 배포합니다.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

2. 채점 스크립트에는 Azure 기계 학습에서 스키마 사양을 생성하는 데 사용되는 [샘플 입력 및 출력이](../machine-learning/how-to-deploy-and-where.md#example-entry-script) 있어야 합니다. Stream Analytics는 스키마를 사용하여 웹 서비스의 기능 서명을 이해합니다.

3. 웹 서비스가 JSON 직렬화된 데이터를 수락하고 반환하는지 확인합니다.

4. 대규모 프로덕션 배포를 위해 [Azure Kubernetes 서비스에](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) 모델을 배포합니다. 웹 서비스가 작업에서 들어오는 요청 수를 처리할 수 없는 경우 Stream Analytics 작업의 성능이 저하되어 대기 시간에 영향을 미칩니다. Azure 컨테이너 인스턴스에 배포된 모델은 현재 지원되지 않지만 향후 몇 개월 내에 사용할 수 있게 됩니다.

## <a name="add-a-machine-learning-model-to-your-job"></a>작업에 기계 학습 모델 추가

Azure 포털에서 직접 Stream Analytics 작업에 Azure 기계 학습 기능을 추가할 수 있습니다.

1. Azure 포털에서 스트림 분석 작업으로 이동한 다음 **작업 토폴로지**에서 **함수를 선택합니다.** 그런 다음 + 드롭다운 **추가** 메뉴에서 Azure **ML 서비스를** 선택합니다.

   ![Azure ML UDF 추가](./media/machine-learning-udf/add-azureml-udf.png)

2. **Azure 기계 학습 서비스 함수** 양식에 다음 속성 값을 입력합니다.

   ![Azure ML UDF 구성](./media/machine-learning-udf/configure-azureml-udf.png)

다음 표에서는 스트림 분석에서 Azure ML 서비스 함수의 각 속성에 대해 설명합니다.

|속성|Description|
|--------|-----------|
|함수 별칭|쿼리에서 함수를 호출하는 이름을 입력합니다.|
|Subscription|Azure 구독.|
|Azure ML 작업 영역|모델을 웹 서비스로 배포하는 데 사용한 Azure 기계 학습 작업 영역입니다.|
|배포|모델을 호스팅하는 웹 서비스입니다.|
|함수 시그니처|API의 스키마 사양에서 유추된 웹 서비스의 서명입니다. 서명이 로드되지 않으면 채점 스크립트에 샘플 입력 및 출력을 제공하여 스키마를 자동으로 생성했는지 확인합니다.|
|파티션당 병렬 요청 수|이 구성은 대규모 처리량을 최적화하기 위한 고급 구성입니다. 이 숫자는 작업의 각 파티션에서 웹 서비스로 전송된 동시 요청을 나타냅니다. 6개의 스트리밍 장치(SU) 및 하위 작업에는 하나의 파티션이 있습니다. 12개의 S가 있는 작업에는 2개의 파티션이 있고, 18개의 SUs에는 3개의 파티션이 있습니다.<br><br> 예를 들어 작업에 두 개의 파티션이 있고 이 매개 변수를 4개로 설정하면 작업에서 웹 서비스에 대한 8개의 동시 요청이 있습니다. 이 공개 미리 보기 시점에서 이 값은 기본값 20으로 설정되며 업데이트할 수 없습니다.|
|최대 일괄 처리 수|이 구성은 대규모 처리량을 최적화하기 위한 고급 구성입니다. 이 숫자는 웹 서비스로 전송된 단일 요청에서 함께 일괄 처리되는 최대 이벤트 수를 나타냅니다.|

## <a name="supported-input-parameters"></a>지원되는 입력 매개 변수

Stream Analytics 쿼리가 Azure 기계 학습 UDF를 호출하면 작업이 웹 서비스에 JSON 직렬화된 요청을 만듭니다. 요청은 모델별 스키마를 기반으로 합니다. [스키마를 자동으로 생성하려면](../machine-learning/how-to-deploy-and-where.md)점수 매기기 스크립트에 샘플 입력 및 출력을 제공해야 합니다. 이 스키마를 사용하면 Stream Analytics에서 numpy, 팬더 및 PySpark와 같은 지원되는 데이터 형식에 대해 JSON 직렬화된 요청을 생성할 수 있습니다. 단일 요청에서 여러 입력 이벤트를 함께 일괄 배치할 수 있습니다.

다음 스트림 분석 쿼리는 Azure 기계 학습 UDF를 호출하는 방법의 예입니다.

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

스트림 분석은 Azure 기계 학습 기능에 대한 하나의 매개 변수 전달만 지원합니다. 기계 학습 UDF에 입력으로 데이터를 전달하기 전에 데이터를 준비해야 할 수 있습니다.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>UDF에 여러 입력 매개 변수전달

기계 학습 모델에 대한 입력의 가장 일반적인 예는 numpy 배열 및 DataFrames입니다. JavaScript UDF를 사용하여 배열을 만들고 절을 사용하여 JSON 직렬화된 DataFrame을 `WITH` 만들 수 있습니다.

### <a name="create-an-input-array"></a>입력 배열 만들기

*N개의* 입력을 허용하고 Azure 기계 학습 UDF에 대한 입력으로 사용할 수 있는 배열을 만드는 JavaScript UDF를 만들 수 있습니다.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

작업에 JavaScript UDF를 추가한 후에는 다음 쿼리를 사용하여 Azure 기계 학습 UDF를 호출할 수 있습니다.

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

다음 JSON은 예제 요청입니다.

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>팬더 또는 파이스파크 데이터프레임 만들기

이 절을 `WITH` 사용하여 아래와 같이 Azure 기계 학습 UDF에 입력으로 전달할 수 있는 JSON 직렬화된 DataFrame을 만들 수 있습니다.

다음 쿼리는 필요한 필드를 선택하여 DataFrame을 만들고 DataFrame을 Azure 기계 학습 UDF에 대한 입력으로 사용합니다.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

다음 JSON은 이전 쿼리의 예제 요청입니다.

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Azure 기계 학습 DF에 대한 성능 최적화

Azure Kubernetes 서비스에 모델을 배포할 때 [모델을 프로파일화하여 리소스 사용률을 확인할](../machine-learning/how-to-deploy-and-where.md#profilemodel)수 있습니다. [배포에 대한 App Insights를 활성화하여](../machine-learning/how-to-enable-app-insights.md) 요청 속도, 응답 시간 및 실패율을 파악할 수도 있습니다.

이벤트 처리량이 높은 시나리오가 있는 경우 낮은 종단 간 대기 시간으로 최적의 성능을 달성하려면 Stream Analytics에서 다음 매개 변수를 변경해야 할 수 있습니다.

1. 최대 일괄 처리 수입니다.
2. 파티션당 병렬 요청 수입니다.

### <a name="determine-the-right-batch-size"></a>올바른 배치 크기 결정

웹 서비스를 배포한 후 50개에서 시작하여 수백 개 씩 증가하는 다양한 배치 크기로 샘플 요청을 보냅니다. 예를 들어 200, 500, 1000, 2000 등을 예로 들 수 있습니다. 특정 일괄 처리 크기 가 지나면 응답의 대기 시간이 증가합니다. 응답 대기 시간이 증가하는 시점은 작업에 대한 최대 일괄 처리 수여야 합니다.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>파티션당 병렬 요청 수 결정

최적의 스케일링에서 Stream Analytics 작업은 웹 서비스에 여러 병렬 요청을 보내고 몇 밀리초 내에 응답을 받을 수 있어야 합니다. 웹 서비스의 응답 대기 시간은 Stream Analytics 작업의 대기 시간 및 성능에 직접적인 영향을 줄 수 있습니다. 작업에서 웹 서비스로의 호출에 시간이 오래 걸리는 경우 워터마크 지연이 증가하고 백로그된 입력 이벤트 수가 증가할 수 있습니다.

이러한 대기 시간을 방지하려면 AZURE Kubernetes 서비스(AKS) 클러스터에 [적절한 수의 노드 및 복제본이](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)프로비전되었는지 확인합니다. 웹 서비스를 가용성이 높고 성공적인 응답을 반환하는 것이 중요합니다. 작업이 웹 서비스에서 서비스 사용할 수 없는 응답(503)을 수신하는 경우 지수 가시적으로 다시 시도됩니다. 성공(200) 및 서비스(503)를 제외한 모든 응답은 작업이 실패한 상태로 이동하게 됩니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Stream Analytics JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [Azure 기계 학습 스튜디오(클래식) 기능으로 스트림 분석 작업 확장](stream-analytics-scale-with-machine-learning-functions.md)

