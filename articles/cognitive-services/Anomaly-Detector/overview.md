---
title: Anomaly Detector API란? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 시계열 데이터에서 변칙을 식별 하는 비정상 탐지기 API의 고급 알고리즘을 사용 합니다.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "64415820"
---
# <a name="what-is-the-anomaly-detector-api"></a>Anomaly Detector API란?

비정상 탐지기 API를 사용 하면 모니터링 및 기계 학습을 사용 하 여 시계열 데이터에서 변칙을 수 있습니다. 비정상 탐지기 API는 자동으로 식별 하 고 산업, 시나리오 또는 데이터 볼륨에 관계 없이 데이터에 가장 적합 한 모델을 적용 하 여 조정 됩니다. 시계열 데이터를 사용 하는 API 변칙 검색에 예상 값에 대 한 경계를 확인 하 고 데이터 요소가 잘못 된 부분을 수행 하는.

![서비스 요청의 패턴 변경 내용 검색](./media/anomaly_detection2.png)

이상 감지기를 사용 하 여 기계 학습의 사전 지식이 필요 하지 않습니다 및 RESTful API를 사용 하면 응용 프로그램 및 프로세스에 서비스를 쉽게 통합할 수 있습니다.

## <a name="features"></a>기능

비정상 탐지기를 사용 하 여 자동으로 이상을 감지할 수 있습니다, 시계열 데이터에서 실시간으로 또는 합니다. 

|기능  |설명  |
|---------|---------|
|실시간으로 변칙을 검색 합니다. | 최신 컴퓨터로 비정상 인지 확인 하려면 이전에 표시 된 데이터 요소를 사용 하 여 스트리밍 데이터에서 변칙을 검색 합니다. 이 작업에는 전송 및 대상 지점 비정상 인지 여부를 확인 하 여 데이터 요소를 사용 하 여 모델을 생성 합니다. 생성 하면 각 새 데이터 요소를 사용 하 여 API를 호출 하 여 생성 될 때 데이터를 모니터링할 수 있습니다. |
|일괄 처리로 데이터 집합 전체에서 변칙을 검색 합니다. | 데이터 전체에 걸쳐 존재할 수 있는 모든 변칙을 검색 하 여 시계열을 사용 합니다. 이 작업에는 동일한 모델을 사용 하 여 분석 하는 각 요소를 사용 하 여 전체 시계열 데이터를 사용 하 여 모델을 생성 합니다.         |
| 데이터에 대 한 추가 정보를 가져옵니다. | 데이터 및 예상 값, 변칙 경계 및 위치를 포함 하 여 관찰 된 모든 예외에 대 한 유용한 정보를 가져옵니다. |
| 변칙 검색 경계를 조정 합니다. | 비정상 탐지기 API는 자동으로 변칙 검색에 대 한 경계를 만듭니다. 데이터 문제에 대 한 API의 민감도 늘리거나 이러한 경계를 조정 하 고 데이터에 따라 키를 누릅니다. |

## <a name="demo"></a>데모

비정상 탐지기 API 사용을 빠르게 시작 하려면 시도 [온라인 데모](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) 브라우저에서 실행할 수 있습니다. 이 데모 웹 호스팅 Jupyter notebook에서 실행 되며 API 요청을 보내고 결과 시각화 하는 방법을 보여 줍니다.

데모를 실행 하려면 다음 단계를 수행 합니다.

1. 유효한 비정상 탐지기 API 구독 키 및 API 끝점을 가져옵니다. 아래 섹션에는 등록에 대 한 지침입니다. 
2. 로그인 하 고 오른쪽 위 모서리에서 복제를 클릭 합니다.
3. 클릭 **무료 계산에서 실행**
4. 이 샘플에 대 한 노트북이 중 하나를 선택 합니다.
5. 유효한 비정상 탐지기 API 구독 키를 추가 합니다 `subscription_key` 변수입니다. 변경 된 `endpoint` 엔드포인트에 변수입니다. 예: `https://westus2.api.cognitive.microsoft.com`
1. 맨 위 메뉴 모음에서 클릭 **셀**, 한 다음 **모두 실행**합니다.

## <a name="workflow"></a>워크플로

비정상 탐지기 API는 RESTful 웹 서비스 HTTP 요청을 수행 하 고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어에서 쉽게 호출할 수 있도록 합니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

가입 후:

1. 시계열 데이터를 가져오고 유효한 JSON 형식으로 변환 합니다. 사용 하 여 [모범 사례](concepts/anomaly-detection-best-practices.md) 최상의 결과 얻으려면 데이터를 준비 하는 경우.
1. 데이터를 사용 하 여 비정상 탐지기 API에 요청을 보냅니다.
1. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 비정상 탐지기 REST API를 사용 하 여 시계열 데이터에서 변칙을 검색 합니다.](quickstarts/detect-data-anomalies-csharp.md)
* 비정상 탐지기 API [온라인 데모](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* 이상 감지기 [REST API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)