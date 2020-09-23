---
title: Azure Machine Learning과 Azure Stream Analytics 통합
description: 이 문서에서는 Azure Stream Analytics 작업을 Azure Machine Learning 모델과 통합하는 방법을 설명합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: e9496dc70d847d0e9e830a216e8f435b1c48d878
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900970"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure Machine Learning과 Azure Stream Analytics 통합(미리 보기)

Azure Stream Analytics 작업에서 UDF(사용자 정의 함수)로 기계 학습 모델을 구현하여 스트리밍 입력 데이터에 대한 실시간 채점 및 예측을 수행할 수 있습니다. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md)을 사용하면 Tensorflow, scikit, PyTorch 등의 인기 있는 오픈 소스 도구를 사용하여 모델을 준비, 학습 및 배포할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

기계 학습 모델을 Stream Analytics 작업에 함수로 추가하기 전에 다음 단계를 완료합니다.

1. Azure Machine Learning을 사용하여 [모델을 웹 서비스로 배포](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)합니다.

2. 채점 스크립트에는 Azure Machine Learning에서 스키마 사양을 생성하는 데 사용되는 [샘플 입력 및 출력](../machine-learning/how-to-deploy-and-where.md)이 있어야 합니다. Stream Analytics는 스키마를 사용하여 웹 서비스의 함수 시그니처를 이해합니다. 이 [샘플 swagger 정의](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) 를 참조로 사용 하 여 올바르게 설정 되었는지 확인할 수 있습니다.

3. 웹 서비스가 JSON 직렬화된 데이터를 수락하고 반환하는지 확인합니다.

4. 대규모 프로덕션 배포를 위한 모델을 [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target)에 배포합니다. 웹 서비스가 작업에서 발생하는 요청 수를 처리할 수 없는 경우 Stream Analytics 작업의 성능이 저하되어 대기 시간에 영향을 줍니다. Azure Container Instances에 배포된 모델은 Azure Portal을 사용하는 경우에만 지원됩니다. [Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer) 를 사용 하 여 작성 된 모델은 Stream Analytics에서 아직 지원 되지 않습니다.

## <a name="add-a-machine-learning-model-to-your-job"></a>작업에 기계 학습 모델 추가

Azure Portal 또는 Visual Studio Code에서 직접 Stream Analytics 작업에 Azure Machine Learning 함수를 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. Azure Portal의 Stream Analytics 작업으로 이동하고 **작업 토폴로지**에서 **함수**를 선택합니다. 그런 다음 **+ 추가** 드롭다운 메뉴에서 **Azure Machine Learning 서비스** 를 선택 합니다.

   ![Azure Machine Learning UDF 추가](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. 다음 속성 값을 사용하여 **Azure Machine Learning Service 함수** 양식을 채웁니다.

   ![Azure Machine Learning UDF 구성](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Visual Studio Code에서 Stream Analytics 프로젝트를 열고 **함수** 폴더를 마우스 오른쪽 단추로 클릭 합니다. 그런 다음 **함수 추가**를 선택 합니다. 드롭다운 목록에서 **MACHINE LEARNING UDF** 를 선택 합니다.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="VS Code에서 UDF 추가":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="VS Code에서 Azure Machine Learning UDF 추가":::

2. 함수 이름을 입력 하 고 CodeLens의 **구독에서 선택을** 사용 하 여 구성 파일의 설정을 입력 합니다.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="VS Code에서 Azure Machine Learning UDF를 선택 합니다.":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="VS Code에서 Azure Machine Learning UDF 구성":::

다음 표에서는 Stream Analytics에서 Azure Machine Learning 서비스 함수의 각 속성을 설명 합니다.

|속성|Description|
|--------|-----------|
|함수 별칭|쿼리에서 함수를 호출할 이름을 입력합니다.|
|Subscription|사용자의 Azure 구독입니다.|
|Azure Machine Learning 작업 영역|모델을 웹 서비스로 배포하는 데 사용한 Azure Machine Learning 작업 영역입니다.|
|배포|모델을 호스팅하는 웹 서비스입니다.|
|함수 시그니처|API의 스키마 사양에서 유추된 웹 서비스의 서명입니다. 서명을 로드하지 못하면 채점 스크립트에 샘플 입력 및 출력을 제공하여 스키마를 자동으로 생성했는지 확인합니다.|
|파티션당 병렬 요청 수|이는 대규모의 처리량을 최적화하는 고급 구성입니다. 이 숫자는 작업의 각 파티션에서 웹 서비스로 전송되는 동시 요청을 나타냅니다. 6개 이하의 SU(스트리밍 단위)를 사용하는 작업에 하나의 파티션이 있습니다. 12개의 SU를 사용하는 작업에는 2개의 파티션이 있으며 18개의 SU 작업에는 3개의 파티션이 있습니다.<br><br> 예를 들어 작업에 두 개의 파티션이 있고 이 매개 변수를 4로 설정하면 작업에서 웹 서비스로의 동시 요청이 8개가 됩니다. 현재 공개 미리 보기의 경우 이 값은 기본적으로 20이며 업데이트할 수 없습니다.|
|최대 일괄 처리 수|이는 대규모의 처리량을 최적화하기 위한 고급 구성입니다. 이 숫자는 웹 서비스로 전송되는 단일 요청에서 함께 일괄 처리되는 최대 이벤트 수를 나타냅니다.|

## <a name="supported-input-parameters"></a>지원되는 입력 매개 변수

Stream Analytics 쿼리가 Azure Machine Learning UDF를 호출하면 작업이 웹 서비스에 대한 JSON 직렬화된 요청을 만듭니다. 요청은 모델별 스키마를 기반으로 합니다. [스키마를 자동으로 생성](../machine-learning/how-to-deploy-and-where.md)하려면 채점 스크립트에 샘플 입력 및 출력을 제공해야 합니다. 이 스키마를 사용하면 Stream Analytics가 numpy, pandas 및 PySpark와 같이 지원되는 데이터 형식에 대해 JSON 직렬화된 요청을 생성할 수 있습니다. 단일 요청으로 여러 입력 이벤트를 일괄 처리할 수 있습니다.

다음 Stream Analytics 쿼리는 Azure Machine Learning UDF를 호출하는 방법의 예입니다.

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics에서는 Azure Machine Learning 함수에 대해 하나의 매개 변수만 전달할 수 있습니다. 기계 학습 UDF에 대한 입력으로 데이터를 전달하기 전에 준비해야 합니다.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>여러 입력 매개 변수를 UDF에 전달

기계 학습 모델에 대한 입력의 가장 일반적인 예는 numpy 배열과 데이터 프레임입니다. JavaScript UDF를 사용하여 배열을 만들고 `WITH` 절을 사용하여 JSON 직렬화된 데이터 프레임을 만들 수 있습니다.

### <a name="create-an-input-array"></a>입력 배열 만들기

*N*개의 입력 수를 허용하고 Azure Machine Learning UDF에 대한 입력으로 사용할 수 있는 배열을 만드는 JavaScript UDF를 만들 수 있습니다.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

작업에 JavaScript UDF를 추가한 후에는 다음 쿼리를 사용하여 Azure Machine Learning UDF를 호출할 수 있습니다.

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

다음 JSON은 요청 예입니다.

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Pandas 또는 PySpark 데이터 프레임 만들기

`WITH` 절을 사용하여 아래와 같이 Azure Machine Learning UDF에 대한 입력으로 전달될 수 있는 JSON 직렬화된 데이터 프레임을 만들 수 있습니다.

다음 쿼리는 필요한 필드를 선택하여 데이터 프레임을 만들고 Azure Machine Learning UDF에 대한 입력으로 데이터 프레임을 사용합니다.

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

다음 JSON은 이전 쿼리의 요청 예입니다.

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Azure Machine Learning UDF의 성능 최적화

모델을 Azure Kubernetes Service에 배포하는 경우 [모델을 프로파일링하여 리소스 사용률을 확인](../machine-learning/how-to-deploy-profile-model.md)할 수 있습니다. 또한 [배포하기 위해 App Insights를 사용하도록 설정](../machine-learning/how-to-enable-app-insights.md)하여 요청 속도, 응답 시간 및 실패율을 이해할 수 있습니다.

이벤트 처리량이 높은 시나리오에서는 Stream Analytics에서 다음 매개 변수를 변경하여 엔드투엔드 대기 시간을 단축하여 최적의 성능을 구현해야 할 수 있습니다.

1. 최대 일괄 처리 수.
2. 파티션당 병렬 요청 수

### <a name="determine-the-right-batch-size"></a>올바른 일괄 처리 크기 결정

웹 서비스를 배포한 후에는 50 단위에서 수백 단위까지의 다양한 일괄 처리 크기로 샘플 요청을 보냅니다. 예를 들면 200, 500, 1000, 2000 등이 있습니다. 특정 일괄 처리 크기 이후에 응답의 대기 시간이 증가하는 것을 알 수 있습니다. 응답 대기 시간이 증가하는 지점은 작업의 최대 일괄 처리 수입니다.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>파티션당 병렬 요청 수 결정

최적의 크기 조정 시 Stream Analytics 작업에서 웹 서비스로 여러 병렬 요청을 보내고 몇 밀리초 내에 응답을 받을 수 있어야 합니다. 웹 서비스 응답의 대기 시간은 Stream Analytics 작업의 대기 시간 및 성능에 직접적인 영향을 줄 수 있습니다. 작업에서 웹 서비스로의 호출에 오랜 시간이 걸리는 경우 워터마크 지연이 증가하며 백로그된 입력 이벤트 수가 증가할 수도 있습니다.

이러한 대기 시간을 방지하려면 AKS(Azure Kubernetes Service) 클러스터가 [적절한 수의 노드 및 복제본](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)으로 프로비저닝되었는지 확인합니다. 웹 서비스는 가용성이 높아야 하며 응답을 성공적으로 반환해야 합니다. 작업이 웹 서비스의 서비스를 사용할 수 없음(503) 응답을 수신하는 경우 지수 백오프를 사용하여 계속해서 다시 시도합니다. 성공(200)이 아니고 서비스를 사용할 수 없음(503) 응답을 수신하면 작업이 실패 상태로 전환됩니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Stream Analytics JavaScript 사용자 정의 함수](stream-analytics-javascript-user-defined-functions.md)
* [Azure Machine Learning Studio(클래식) 함수를 사용하여 Stream Analytics 작업의 크기 조정](stream-analytics-scale-with-machine-learning-functions.md)
