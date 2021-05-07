---
title: 데이터 쿼리 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 데이터 쿼리 개념 및 REST API 개요.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374136"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2의 데이터 쿼리

Azure Time Series Insights Gen2를 사용할 경우, 공용 Surface API를 통해 환경에 저장된 메타데이터 및 이벤트에 대한 데이터를 쿼리할 수 있습니다. 해당 API는 [Azure Time Series Insights TSI Explorer](./concepts-ux-panels.md)에서도 사용됩니다.

Azure Time Series Insights Gen2에서는 세 가지 기본 API 범주를 사용할 수 있습니다.

* **환경 API**: 환경 API는 Azure Time Series Insights Gen2 환경 자체에 대한 쿼리를 설정합니다. 호출자가 액세스할 수 있는 환경 목록 및 환경 메타데이터를 수집하는 데 사용할 수 있습니다.
* **TSM-Q(Time Series Model-Query) API**: 해당 환경의 Time Series Model에 저장된 메타데이터에 대한 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 사용하도록 설정합니다. 인스턴스, 유형 및 계층 구조에 액세스하고 편집하는 데 사용할 수 있습니다.
* **TSQ(Time Series Query) API**: 원본 공급자가 기록한 원격 분석 데이터 또는 이벤트 데이터를 검색하고 고급 스칼라 및 집계 함수를 통해 데이터에 대한 성능 계산 및 집계를 사용하도록 설정합니다.

Azure Time Series Insights Gen2는 [시계열 변수](./concepts-variables.md)에서 계산을 표현하는 데 사용되는 폭넓은 문자열 기반 언어 식, [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax)(시계열 언어 식)를 사용합니다.

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure Time Series Insights Gen2 API 개요

다음과 같은 핵심 API가 지원됩니다.

[![Time Series Query 개요](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

* [환경 API 가져오기](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): 호출자에게 액세스 권한이 부여된 환경 목록을 반환합니다.
* [환경 가용성 API 가져오기](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): 이벤트 타임스탬프 `$ts`에 대한 이벤트 수의 분포를 반환합니다. 환경 가용성 API 가져오기는 시간 간격으로 분할된 이벤트 수가 있는 경우, 이를 반환하여 해당 환경에 이벤트가 있는지 확인하는 데 도움이 됩니다.
* [이벤트 스키마 API 가져오기](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): 지정된 검색 범위의 이벤트 스키마 메타데이터를 반환합니다. 이 API는 지정된 검색 범위의 스키마에서 사용 가능한 모든 메타데이터 및 속성을 검색할 수 있습니다.

## <a name="time-series-model-query-tsm-q-apis"></a>TSM-Q(Time Series Model-Query) API

이 유형의 API 대부분은 일괄 처리 실행 작업을 지원하여 여러 Time Series Model 엔터티에 대해 일괄 처리 CRUD 작업을 가능하게 합니다.

* [모델 설정 API](/rest/api/time-series-insights/reference-model-apis): 환경의 기본 형식 및 모델명에 대한 *가져오기* 및 *패치* 를 사용하도록 설정합니다.
* [형식 API](/rest/api/time-series-insights/reference-model-apis#types-api): Time Series 형식 및 관련 변수에 대한 CRUD 작업을 사용하도록 설정합니다.
* [계층 구조 API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): Time Series 계층 구조 및 관련 필드 경로에 대한 CRUD 작업을 사용하도록 설정합니다.
* [Instances API](/rest/api/time-series-insights/reference-model-apis#instances-api): Time Series 인스턴스 및 관련 인스턴스 필드에 대한 CRUD를 사용하도록 설정합니다. 또한 인스턴스 API는 다음과 같은 작업을 지원합니다.
  * [검색](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): 인스턴스 특성을 기반으로 시계열 인스턴스 검색에 대한 일부 적중 목록을 검색합니다.
  * [제안](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): 인스턴스 특성에 따라 시계열 인스턴스 검색에 대한 일부 적중 목록을 검색하고 제안합니다.

## <a name="time-series-query-tsq-apis"></a>TSQ(Time Series Query) API

이 API는 다중 계층 스토리지 솔루션의 웜과 콜드 저장소 모두에서 사용 가능합니다. 

* [이벤트 API 가져오기](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): 원본 공급자가 Azure Time Series Insights Gen2에 기록한 대로 원시 이벤트 및 관련 이벤트 타임스탬프에 쿼리 및 검색을 사용하도록 설정합니다. 이벤트 API 가져오기는 지정된 Time Series ID 및 검색 범위에 대한 원시 이벤트 검색을 허용합니다. 해당 API는 선택한 입력에 대한 전체 응답 데이터 세트를 검색하는 페이지 매김을 지원합니다.

  > [!IMPORTANT]
  > [예정된 JSON 평면화 및 이스케이프 규칙 변경](./ingestion-rules-update.md)의 일부로, 배열은 **동적** 형식으로 저장됩니다. 해당 유형으로 저장된 페이로드 속성은 **이벤트 가져오기 API를 통해서만 액세스할 수 있습니다**.

* [시리즈 API 가져오기](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): 원시 이벤트에 대한 변수로 정의된 계산을 적용하여 계산값 및 관련 이벤트 타임스탬프의 쿼리 및 검색을 수행할 수 있습니다. 원시 이벤트에 대한 변수는 Time Series Model에서 정의하거나 쿼리에서 인라인으로 제공할 수 있습니다. 해당 API는 선택한 입력에 대한 전체 응답 데이터 세트를 검색하는 페이지 매김을 지원합니다.

* [시리즈 API 집계하기](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): 원시 이벤트에 대한 변수로 정의된 계산을 적용하여 집계된 값 및 관련 간격 타임스탬프의 쿼리 및 검색을 사용하도록 설정합니다. 원시 이벤트에 대한 변수는 Time Series Model에서 정의하거나 쿼리에서 인라인으로 제공할 수 있습니다. 해당 API는 선택한 입력에 대한 전체 응답 데이터 세트를 검색하는 페이지 매김을 지원합니다.
  
  지정된 검색 범위 및 간격에 대해 이벤트 API 가져오기는 Time Series ID의 변수 간격 당 집계된 응답을 반환합니다. 응답 데이터 세트의 간격은 Epoch 틱(1970년 1월 1일 Unix Epoch 이후 밀리초 단위로 경과된 수)을 쿼리에 지정된 간격 범위로 나누어 계산합니다.

  응답 세트에서 반환된 타임스탬프는 간격에서 샘플링된 이벤트가 아니라 좌측 간격 경계에 속합니다.


### <a name="selecting-store-type"></a>저장소 유형 선택

위의 API는 한 번의 호출로 스토리지 유형(웜 또는 콜드) 중 하나의 유형에 대해서만 실행할 수 있습니다. 쿼리 URL 매개 변수를 사용하여 쿼리를 실행해야 하는 [저장소 유형](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)을 지정합니다. 

매개 변수를 지정하지 않으면 기본적으로 콜드 저장소에서 쿼리가 실행됩니다. 쿼리가 콜드 및 웜 저장소와 시간 범위가 겹치는 경우 웜 저장소는 일부 데이터만 포함하기 때문에 최상의 환경을 위해서는 콜드 저장소로 쿼리를 라우팅하는 것이 좋습니다. 

[Azure Time Series Insights Explorer](./concepts-ux-panels.md)와 [Power BI 커넥터](./how-to-connect-power-bi.md)는 위의 API를 호출하고 적절한 storeType 매개 변수를 자동으로 선택합니다. 


## <a name="next-steps"></a>다음 단계

* [Time Series Model](./concepts-model-overview.md)에서 정의할 수 있는 다양한 변수에 대해 자세히 알아보세요.
* [Azure Time Series Insights Explorer](./concepts-ux-panels.md)에서 데이터를 쿼리하는 방법에 대해 자세히 알아보세요.
