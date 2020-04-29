---
title: Azure Machine Learning와 Azure Stream Analytics 통합
description: 이 문서에서는 Azure Machine Learning 모델을 사용 하 여 Azure Stream Analytics 작업을 통합 하는 방법을 설명 합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481982"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure Machine Learning와 Azure Stream Analytics 통합 (미리 보기)

Azure Stream Analytics 작업에서 UDF (사용자 정의 함수)로 기계 학습 모델을 구현 하 여 스트리밍 입력 데이터에 대 한 실시간 점수 매기기 및 예측을 수행할 수 있습니다. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) 를 사용 하면 Tensorflow, Scikit, PyTorch 등의 인기 있는 오픈 소스 도구를 사용 하 여 모델을 준비, 학습 및 배포할 수 있습니다.

> [!NOTE]
> 이 기능은 공개 미리 보기로 제공 됩니다. [Stream Analytics 포털 미리 보기 링크](https://aka.ms/asaportalpreview)를 사용 하 여 Azure Portal에서이 기능에 액세스할 수 있습니다. 이 기능은 최신 버전의 [Visual Studio 용 Stream Analytics 도구](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)에서도 사용할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

Stream Analytics 작업에 함수로 machine learning 모델을 추가 하기 전에 다음 단계를 완료 합니다.

1. Azure Machine Learning를 사용 하 여 [모델을 웹 서비스로 배포](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)합니다.

2. 점수 매기기 스크립트는 Azure Machine Learning에서 스키마 사양을 생성 하는 데 사용 되는 [샘플 입력 및 출력](../machine-learning/how-to-deploy-and-where.md#example-entry-script) 을 포함 해야 합니다. Stream Analytics은 스키마를 사용 하 여 웹 서비스의 함수 시그니처를 이해 합니다.

3. 웹 서비스가 JSON 직렬화 된 데이터를 수락 하 고 반환 하는지 확인 합니다.

4. 확장성이 뛰어난 프로덕션 배포를 위해 [Azure Kubernetes 서비스](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) 에 모델을 배포 합니다. 웹 서비스가 작업에서 들어오는 요청 수를 처리할 수 없는 경우 Stream Analytics 작업의 성능이 저하 되므로 대기 시간이 영향을 받습니다. Azure Container Instances에 배포 된 모델은 현재 지원 되지 않지만 향후 몇 개월 내에 제공 될 예정입니다.

## <a name="add-a-machine-learning-model-to-your-job"></a>작업에 machine learning 모델 추가

Azure Portal에서 직접 Stream Analytics 작업에 Azure Machine Learning 함수를 추가할 수 있습니다.

1. Azure Portal에서 Stream Analytics 작업으로 이동 하 고 **작업 토폴로지**아래에서 **함수** 를 선택 합니다. 그런 다음 **+ 추가** 드롭다운 메뉴에서 **Azure ML 서비스** 를 선택 합니다.

   ![Azure ML UDF 추가](./media/machine-learning-udf/add-azureml-udf.png)

2. 다음 속성 값을 사용 하 여 **Azure Machine Learning 서비스 함수** 폼에 입력 합니다.

   ![Azure ML UDF 구성](./media/machine-learning-udf/configure-azureml-udf.png)

다음 표에서는 Stream Analytics에서 Azure ML 서비스 함수의 각 속성을 설명 합니다.

|속성|Description|
|--------|-----------|
|함수 별칭|쿼리에서 함수를 호출 하는 이름을 입력 합니다.|
|Subscription|Azure 구독.|
|Azure ML 작업 영역|모델을 웹 서비스로 배포 하는 데 사용한 Azure Machine Learning 작업 영역입니다.|
|배포|모델을 호스팅하는 웹 서비스입니다.|
|함수 시그니처|API의 스키마 사양에서 유추 된 웹 서비스의 서명입니다. 서명이 로드 되지 않으면 점수 매기기 스크립트에 샘플 입력 및 출력을 제공 하 여 스키마를 자동으로 생성 했는지 확인 합니다.|
|파티션당 병렬 요청 수|이는 높은 규모의 처리량을 최적화 하는 고급 구성입니다. 이 숫자는 작업의 각 파티션에서 웹 서비스로 전송 되는 동시 요청을 나타냅니다. 6 개의 스트리밍 단위 (SU)와 낮은 작업에는 하나의 파티션이 있습니다. 12 개의 SUs를 사용 하는 작업에는 두 개의 파티션이 있으며 18 개의 SUs에는 3 개의 파티션이 있습니다.<br><br> 예를 들어 작업에 두 개의 파티션이 있고이 매개 변수를 4로 설정 하면 작업에서 웹 서비스로의 동시 요청이 8 개가 됩니다. 현재 공개 미리 보기의 경우이 값은 기본적으로 20 이며 업데이트할 수 없습니다.|
|최대 일괄 처리 수|이는 높은 규모의 처리량을 최적화 하기 위한 고급 구성입니다. 이 숫자는 웹 서비스로 전송 되는 단일 요청에서 함께 일괄 처리 되는 최대 이벤트 수를 나타냅니다.|

## <a name="supported-input-parameters"></a>지원 되는 입력 매개 변수

Stream Analytics 쿼리가 Azure Machine Learning UDF를 호출 하면 작업은 웹 서비스에 대 한 JSON 직렬화 된 요청을 만듭니다. 요청은 모델 관련 스키마를 기반으로 합니다. [스키마를 자동으로 생성](../machine-learning/how-to-deploy-and-where.md)하려면 점수 매기기 스크립트에 샘플 입력 및 출력을 제공 해야 합니다. 이 스키마를 사용 Stream Analytics 하면 numpy, pandas 및 PySpark와 같은 지원 되는 데이터 형식에 대해 JSON으로 serialize 된 요청을 생성할 수 있습니다. 단일 요청에서 여러 입력 이벤트를 함께 일괄 처리할 수 있습니다.

다음 Stream Analytics 쿼리는 Azure Machine Learning UDF를 호출 하는 방법의 예입니다.

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics는 Azure Machine Learning 함수에 대해 하나의 매개 변수만 전달할 수 있습니다. 기계 학습 UDF에 대 한 입력으로 전달 하기 전에 데이터를 준비 해야 할 수 있습니다.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>여러 입력 매개 변수를 UDF에 전달

기계 학습 모델에 대 한 입력의 가장 일반적인 예는 numpy 배열과 데이터 프레임입니다. JavaScript UDF를 사용 하 여 배열을 만들고 `WITH` 절을 사용 하 여 JSON 직렬화 된 데이터 프레임를 만들 수 있습니다.

### <a name="create-an-input-array"></a>입력 배열 만들기

*N 개* 수의 입력을 허용 하 고 Azure Machine Learning UDF에 대 한 입력으로 사용할 수 있는 배열을 만드는 JavaScript UDF를 만들 수 있습니다.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

JavaScript UDF를 작업에 추가한 후에는 다음 쿼리를 사용 하 여 Azure Machine Learning UDF를 호출할 수 있습니다.

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

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Pandas 또는 PySpark 데이터 프레임 만들기

다음과 같이 `WITH` 절을 사용 하 여 Azure Machine Learning UDF에 대 한 입력으로 전달 될 수 있는 JSON 직렬화 된 데이터 프레임를 만들 수 있습니다.

다음 쿼리는 필요한 필드를 선택 하 고 Azure Machine Learning UDF에 대 한 입력으로 데이터 프레임를 사용 하 여 데이터 프레임를 만듭니다.

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Azure Machine Learning Udf의 성능 최적화

Azure Kubernetes Service에 모델을 배포 하는 경우 모델을 [프로 파일링 하 여 리소스 사용률을 확인할](../machine-learning/how-to-deploy-and-where.md#profilemodel)수 있습니다. 또한 [배포에 대해 App Insights를 사용 하도록 설정](../machine-learning/how-to-enable-app-insights.md) 하 여 요청 속도, 응답 시간 및 실패율을 이해할 수 있습니다.

이벤트 처리량이 높은 시나리오를 사용 하는 경우에는 Stream Analytics에서 다음 매개 변수를 변경 하 여 종단 간 대기 시간을 최소화 하는 최적의 성능을 구현 해야 할 수 있습니다.

1. 최대 일괄 처리 수입니다.
2. 파티션당 병렬 요청 수입니다.

### <a name="determine-the-right-batch-size"></a>올바른 일괄 처리 크기 결정

웹 서비스를 배포한 후에는 50에서 시작 하 여 다양 한 일괄 처리 크기를 가진 샘플 요청을 전송 하 고 수백의 순서로 늘립니다. 예를 들면 200, 500, 1000, 2000 등이 있습니다. 특정 일괄 처리 크기 이후에 응답의 대기 시간이 증가 하는 것을 알 수 있습니다. 응답 대기 시간이 증가 하는 지점의 위치는 작업의 최대 일괄 처리 수입니다.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>파티션당 병렬 요청 수 결정

최적의 크기 조정 시 Stream Analytics 작업에서 웹 서비스로 여러 병렬 요청을 보내고 몇 밀리초 내에 응답을 받을 수 있어야 합니다. 웹 서비스 응답의 대기 시간은 Stream Analytics 작업의 대기 시간 및 성능에 직접적인 영향을 줄 수 있습니다. 작업에서 웹 서비스로의 호출에 오랜 시간이 걸리는 경우 워터 마크 지연이 증가 하는 것을 볼 수 있으며 백로그 된 입력 이벤트 수가 증가 하는 것을 볼 수도 있습니다.

이러한 대기 시간을 방지 하려면 Azure Kubernetes 서비스 (AKS) 클러스터가 [적절 한 수의 노드 및 복제본](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)으로 프로 비전 되었는지 확인 합니다. 웹 서비스는 항상 사용 가능 하 고 성공적인 응답을 반환 하는 것이 중요 합니다. 작업에서 웹 서비스의 서비스를 사용할 수 없음 응답 (503)을 수신 하는 경우 지 수 백오프를 사용 하 여 계속 해 서 다시 시도 합니다. 성공 (200)이 아닌 응답 및 서비스를 사용할 수 없는 (503) 이면 작업이 실패 상태로 전환 됩니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Stream Analytics JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [Azure Machine Learning Studio (클래식) 함수를 사용 하 여 Stream Analytics 작업 크기 조정](stream-analytics-scale-with-machine-learning-functions.md)

