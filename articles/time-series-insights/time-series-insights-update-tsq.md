---
title: Azure Time Series Insights 미리 보기 데이터 쿼리 | Microsoft Docs
description: Azure Time Series Insights 미리 보기 데이터 쿼리
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: bbf682df2df7a8cdc9fedb36aa4244fc5c0e9488
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244012"
---
# <a name="data-querying"></a>데이터 쿼리

Azure Time Series Insights 미리 보기를 사용하면 공개 화면 API를 통해 환경에 저장된 이벤트 및 메타데이터에 대한 데이터를 쿼리할 수 있습니다. 이러한 API는 [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)에도 사용됩니다.

Time Series Insights에서 사용할 수 있는 세 가지 기본 API 범주는 다음과 같습니다.

* **Environment API**: Time Series Insights 환경 자체의 쿼리를 가능하게 해줍니다. 쿼리 예제로는 호출자가 액세스 권한을 가진 환경 및 환경 메타데이터 목록을 들 수 있습니다.

* **TSM-Q(Time Series Model-Query) API**: 시계열 모델의 환경 부분에 저장된 메타데이터에 대해 만들기, 읽기, 업데이트 및 삭제 작업을 수행할 수 있게 해줍니다. 인스턴스, 형식 및 계층 구조를 예로 들 수 있습니다.

* **TSQ(Time Series Query) API**: 원본 공급자에서 기록된 것처럼 이벤트 데이터를 검색할 수 있습니다. 이러한 API는 시계열 데이터에 대한 변환, 결합 및 계산 작업을 수행할 수 있습니다.

[TSX(Time Series Expression) 언어](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)는 강력한 네 번째 범주입니다. 시계열 모델을 사용하여 고급 계산을 작성할 수 있습니다.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights 미리 보기 핵심 API

다음과 같은 핵심 API가 지원됩니다.

[![시간 시계열 쿼리 개요](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

다음과 같은 Environment API를 사용할 수 있습니다.

* [Get Environment API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): 호출자에게 액세스 권한이 부여된 환경 목록을 반환합니다.
* [Get Environment Availability API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): 이벤트 타임스탬프 `$ts`에 따른 이벤트 수의 분포를 반환합니다. 이 API는 이벤트가 있는 경우 이벤트 수를 반환하여 타임스탬프에 이벤트가 있는지 확인합니다.
* [Get Event Schema API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): 지정된 검색 범위의 이벤트 스키마 메타데이터를 반환합니다. 이 API는 지정된 검색 범위의 스키마에서 사용 가능한 모든 메타데이터 및 속성을 검색할 수 있습니다.

## <a name="time-series-model-query-tsm-q-apis"></a>TSM-Q(Time Series Model-Query) API

다음과 같은 Time Series Model-Query API를 사용할 수 있습니다.

* [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): 환경의 기본 형식 및 모델 이름에 대한 가져오기 및 패치가 가능합니다.
* [Types API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Time Series 형식 및 관련 변수에 대한 CRUD가 가능합니다.
* [Hierarchies API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Time Series 계층 구조 및 관련 필드 경로에 대한 CRUD가 가능합니다.
* [Instances API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Time Series 인스턴스 및 관련 인스턴스 필드에 대한 CRUD가 가능합니다.

## <a name="time-series-query-tsq-apis"></a>TSQ(Time Series Query) API

다음과 같은 Time Series Query API를 사용할 수 있습니다.

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): 원본 공급자의 Time Series Insights에서 기록된 것처럼 이벤트의 Time Series Insights 데이터를 쿼리하고 검색할 수 있습니다.

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): 전송 중에 기록된 데이터를 사용하여 캡처된 이벤트의 Time Series Insights 데이터를 쿼리하고 검색할 수 있습니다. 반환되는 값은 모델에 정의된 변수 또는 인라인으로 제공된 변수를 기반으로 합니다.

    >[!NOTE]
    > 집계 절은 모델에 정의되거나 인라인으로 제공된 경우에도 무시됩니다.

  Get Series API는 각 간격의 값에 대한 Time Series 값을 반환합니다. Time Series 값은 Time Series Insights가 쿼리의 JSON 출력에 사용하는 형식입니다. 반환되는 값은 Time Series ID 및 제공된 변수 세트를 기반으로 합니다.

* [Aggregate Series API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): 기록된 데이터를 샘플링 및 집계하여 캡처된 이벤트의 Time Series Insights 데이터를 쿼리하고 검색할 수 있습니다.

  Aggregate Series API는 각 간격의 값에 대한 Time Series 값을 반환합니다. 값은 Time Series ID 및 제공된 변수 세트를 기반으로 합니다. Aggregate Series API는 Time Series Model에 저장된 값 또는 인라인으로 제공된 값을 사용하여 데이터를 집계 또는 샘플링함으로써 크기를 줄입니다.

  지원되는 집계 형식: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) Azure Time Series Insights 미리 보기에 있습니다.

- Time Series Insights 미리 보기를 읽을 [데이터 모델링](./time-series-insights-update-tsm.md) 문서.

- 검색할 [시간 계열 ID를 선택할 때 모범 사례](./time-series-insights-update-how-to-id.md)합니다.
