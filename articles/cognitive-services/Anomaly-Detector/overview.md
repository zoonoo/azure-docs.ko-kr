---
title: Anomaly Detector API란?
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API의 고급 알고리즘을 사용하여 시계열 데이터에서 변칙을 식별합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 80e0984deff83726fd96a462a1ae8a4375db9d2e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721613"
---
# <a name="what-is-the-anomaly-detector-api"></a>Anomaly Detector API란?

Anomaly Detector API를 사용하면 기계 학습을 통해 시계열 데이터에서 변칙을 검색하고 모니터링할 수 있습니다. Anomaly Detector API는 업계, 시나리오 또는 데이터 볼륨에 관계없이 가장 적합한 모델을 자동으로 식별하여 데이터에 적용하여 조정됩니다. 이 API는 시계열 데이터를 사용하여 변칙 검색, 예상 값 및 어떤 데이터 요소가 변칙인지에 대한 경계를 결정합니다.

![서비스 요청의 패턴 변경 내용 검색](./media/anomaly_detection2.png)

Anomaly Detector 사용에는 기계 학습에 대한 사전 지식이 필요하지 않으며 RESTful API를 통해 애플리케이션과 프로세스에 서비스를 쉽게 통합할 수 있습니다.

## <a name="features"></a>기능

Anomaly Detector를 사용하면 시계열 데이터 전체에서 또는 실시간으로 발생하는 변칙을 자동으로 검색할 수 있습니다. 

|기능  |설명  |
|---------|---------|
|실시간으로 발생하는 변칙을 검색합니다. | 이전에 본 데이터 요소를 사용하여 최신 데이터가 변칙인지 확인하여 스트리밍 데이터에서 변칙을 검색합니다. 이 작업은 보내는 데이터 요소를 사용하여 모델을 생성하고 대상 포인트가 변칙인지 여부를 결정합니다. 생성하는 각각의 새 데이터 요소를 사용하여 API를 호출하면 데이터가 생성될 때 모니터링할 수 있습니다. |
|일괄 처리로 데이터 세트 전체의 변칙을 검색합니다. | 시계열을 사용하여 데이터 전체에 존재할 수 있는 변칙을 검색합니다. 이 작업은 전체 시계열 데이터를 사용하여 모델을 생성하고, 각 포인트는 동일한 모델로 분석됩니다.         |
| 데이터에 대한 추가 정보를 가져옵니다. | 데이터 및 관찰된 변칙에 대한 유용한 정보를 가져오며, 여기에는 예상되는 값, 변칙 경계 및 위치 등이 포함됩니다. |
| 변칙 검색 경계를 조정합니다. | Anomaly Detector API는 변칙 검색에 대한 경계를 자동으로 만듭니다. 이러한 경계를 조정하여 데이터에 잘 맞도록 데이터 변칙에 대한 API의 민감도를 높이거나 낮춥니다. |

## <a name="demo"></a>데모

Anomaly Detector API 사용을 신속하게 시작하려면 브라우저에서 실행할 수 있는 [온라인 데모](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)를 사용해보세요. 이 데모는 웹 호스팅 Jupyter Notebook에서 실행되며 API 요청을 보내고 결과를 시각화하는 방법을 보여줍니다.

데모를 실행하려면 다음 단계를 완료하세요.

1. 유효한 Anomaly Detector API 구독 키와 API 엔드포인트를 가져옵니다. 아래 섹션에는 가입에 대한 지침이 나와 있습니다. 
2. 로그인하고 오른쪽 위 모서리의 복제를 클릭합니다.
3. **Run on free compute**(무료 컴퓨팅에서 실행)을 클릭합니다.
4. 이 샘플의 Notebook 중 하나를 선택합니다.
5. 유효한 Anomaly Detector API 구독 키를 `subscription_key` 변수에 추가합니다. `endpoint` 변수를 엔드포인트로 변경합니다. 예: `https://westus2.api.cognitive.microsoft.com`
1. 상단 메뉴 모음에서 **셀**과 **모두 실행**을 차례로 클릭합니다.

## <a name="workflow"></a>워크플로

Anomaly Detector API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

가입 후:

1. 시계열 데이터를 가져와서 유효한 JSON 형식으로 변환합니다. 데이터 준비 시 [모범 사례](concepts/anomaly-detection-best-practices.md)를 사용하면 최상의 결과를 얻을 수 있습니다.
1. 자신의 데이터로 Anomaly Detector API에 요청을 보냅니다.
1. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Anomaly Detector REST API를 사용하여 시계열 데이터에서 변칙 검색](quickstarts/detect-data-anomalies-csharp.md)
* Anomaly Detector API [온라인 데모](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Anomaly Detector [REST API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)