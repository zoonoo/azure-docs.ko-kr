---
title: Anomaly Finder란? - Microsoft Cognitive Services | Microsoft Docs
description: Anomaly Finder의 고급 알고리즘을 사용하여 Microsoft Cognitive Services에서 시계열 데이터의 변칙을 식별하고 정보를 반환할 수 있습니다.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: c0c15f077a0a7dd302a377aa6f82ba2f8533d862
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456255"
---
# <a name="what-is-anomaly-finder"></a>Anomaly Finder란?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Anomaly Finder를 사용하면 시간에 따라 데이터를 모니터링하고 업계, 시나리오 또는 데이터 볼륨에 관계없이 올바른 통계 모델을 자동으로 적용하여 고유 데이터에 맞게 수정되는 Machine Learning으로 변칙을 검색할 수 있습니다. 시계열을 입력으로 사용하여 Anomaly Finder API는 데이터 요소가 변칙인지 여부를 반환하고, 시각화를 위한 예상 값과 상한 및 하한을 결정합니다. 미리 빌드된 AI 서비스인 Anomaly Finder는 RESTful API를 사용하는 방법만 이해하면 Machine Learning 전문 지식이 필요하지 않습니다. 이 때문에 모든 시계열 데이터에서 작동하고 스트리밍 데이터 시스템에도 빌드될 수 있으므로 개발이 단순하고 유연해집니다. Anomaly Finder는 사기, 도난, 변화하는 지역/국가, 비즈니스 인시던트를 관리하거나 익명성을 유지하면서 IoT 디바이스 트래픽을 모니터링하기 위한 재무 도구 등 광범위한 사용 사례를 포괄합니다. 이 솔루션을 최종 고객이 데이터, 지출, 투자 수익 또는 사용자 활동의 변경 내용을 파악하기 위한 서비스의 일부로 제공하여 수익을 창출할 수도 있습니다.
Anomaly Finder API를 사용해 보고 데이터에 대해 자세히 알아봅니다. 

다음 API로 빌드할 수 있는 사항을 확인합니다.

* 시계열의 과거 데이터를 기준으로 예상 값을 예측하는 방법 알아보기
* 데이터 요소가 과거 패턴에서 변칙적으로 벗어났는지 여부 설명
* “정상” 값의 범위를 시각화하는 밴드 생성

![Anomaly_Finder](./media/anomaly_detection1.png) 

그림 1: 판매 수익의 변칙 검색

![Anomaly_Finder](./media/anomaly_detection2.png)

그림 2: 서비스 요청의 패턴 변경 내용 검색

## <a name="requirements"></a>요구 사항

- 입력 시계열의 최소 데이터: 명확한 주기가 없는 시계열의 경우 데이터 요소 최소 13개, 알려진 주기가 있는 시계열의 경우 데이터 요소 주기 최소 4개입니다. 
- 데이터 무결성: 시계열 데이터 요소가 동일한 간격으로 구분되어 있고 누락된 요소가 없습니다. 

## <a name="identify-anomalies"></a>변칙 식별

변칙 검색 API는 지정된 데이터 요소가 변칙인지 여부에 대한 결과를 반환하고 다음과 같이 추가 정보를 제공합니다.
* Period - API가 이상 요소를 감지하는 데 사용한 주기입니다.
* WarningText - 가능한 경고 정보입니다.
* ExpectedValue - 학습 기반 모델에서 예측된 값입니다.
* IsAnomaly - 데이터 요소가 변칙인지 여부에 대한 결과입니다.
* IsAnomaly_Neg - 데이터 요소가 음수 방향(급감)에서 이상인지 여부에 대한 결과입니다.
* IsAnomaly_Pos - 데이터 요소가 양수 방향(급증)에서 이상인지 여부에 대한 결과입니다.
* UpperMargin - ExpectedValue 및 UpperMargin의 합계는 데이터 요소가 여전히 정상으로 간주되는 상한을 결정합니다.
* LowerMargin - (ExpectedValue - LowerMargin) 데이터 요소가 여전히 정상으로 간주되는 하한을 결정합니다.

> [!Note]
> UpperMargin 및 LowerMargin을 사용하여 실제 시계열 주위에 밴드를 생성하고 정상 값의 범위를 시각화할 수 있습니다. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>응답에서 사후 처리 시 하한 및 상한 조정

변칙 검색 API는 데이터 요소가 변칙인지 여부에 대한 기본 결과를 반환하며, ExpectedValue 및 UpperMargin/LowerMargin에서 상한 및 하한을 계산할 수 있습니다. 대부분의 경우 이러한 기본값을 사용하면 됩니다. 그러나 일부 시나리오에서는 기본값과 다른 경계가 필요합니다. 권장 사례는 UpperMargin 또는 LowerMargin에 계수를 적용하여 동적 경계를 조정하는 것입니다.

### <a name="examples-with-1152-as-coefficiency"></a>1/1.5/2를 계수로 사용한 예제

![기본 민감도](./media/sensitivity_1.png)

![1.5 민감도](./media/sensitivity_1.5.png)

![2 민감도](./media/sensitivity_2.png)

샘플 데이터를 사용한 요청

[!INCLUDE [Request](./includes/request.md)]

샘플 JSON 응답

[!INCLUDE [Response](./includes/response.md)]
