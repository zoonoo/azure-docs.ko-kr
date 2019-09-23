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
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3bd60ff1e732940bbb13c2e224084cf7e331266b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934872"
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

이 [대화형 데모](https://aka.ms/adDemo)를 확인하여 Anomaly Detector가 작동하는 방식을 이해합니다.
데모를 실행하려면 Anomaly Detector 리소스를 만들고 API 키와 엔드포인트를 가져와야 합니다.

## <a name="notebook"></a>노트북

Anomaly Detector API를 호출하는 방법을 알아보려면 이 [Azure Notebook](https://aka.ms/adNotebook)을 사용해 보세요. 이 웹 호스팅 Jupyter Notebook은 API 요청을 보내고 결과를 시각화하는 방법을 보여 줍니다.

Notebook을 실행하려면 다음 단계를 완료하세요.

1. 유효한 Anomaly Detector API 구독 키와 API 엔드포인트를 가져옵니다. 아래 섹션에는 가입에 대한 지침이 나와 있습니다.
1. 로그인하고 오른쪽 위 모서리의 복제를 클릭합니다.
1. 복제 작업을 완료하기 전에 대화 상자에서 "공용" 옵션의 선택을 취소합니다. 그렇지 않으면 구독 키를 포함하여 노트북이 공개됩니다.
1. **Run on free compute**(무료 컴퓨팅에서 실행)을 클릭합니다.
1. Notebook 중 하나를 선택합니다.
1. 유효한 Anomaly Detector API 구독 키를 `subscription_key` 변수에 추가합니다. 
1. `endpoint` 변수를 엔드포인트로 변경합니다. 예: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. 상단 메뉴 모음에서 **셀**과 **모두 실행**을 차례로 클릭합니다.

## <a name="workflow"></a>워크플로

Anomaly Detector API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

가입 후:

1. 시계열 데이터를 가져와서 유효한 JSON 형식으로 변환합니다. 데이터 준비 시 [모범 사례](concepts/anomaly-detection-best-practices.md)를 사용하면 최상의 결과를 얻을 수 있습니다.
1. 자신의 데이터로 Anomaly Detector API에 요청을 보냅니다.
1. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="algorithms"></a>알고리즘

* 내부의 알고리즘에 대한 이 기술 블로그 [Azure Anomaly Detector API 소개](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)를 참조하세요.
* Microsoft에서 개발한 최첨단 SR-CNN 알고리즘에 대한 이 문서 [Microsoft의 시계열 Anomaly Detection 서비스](https://arxiv.org/abs/1906.03821)(KDD 2019에서 수락)를 참조하세요.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector 커뮤니티 참여

* [Microsoft Teams의 Anomaly Detector Advisors 그룹](https://aka.ms/AdAdvisorsJoin)에 조인
* 선택한 [사용자 생성 콘텐츠](user-generated-content.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Anomaly Detector REST API를 사용하여 시계열 데이터에서 변칙 검색](quickstarts/detect-data-anomalies-csharp.md)
* Anomaly Detector API [온라인 데모](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Anomaly Detector [REST API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
