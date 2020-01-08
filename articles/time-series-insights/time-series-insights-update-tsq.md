---
title: 미리 보기의 데이터 쿼리-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Preview의 데이터 쿼리 개념 및 HTTP REST API 개요.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: 53693c64ff03146619e17b276a1f817e69f5ed6e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374581"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 쿼리

Azure Time Series Insights 미리 보기를 사용하면 공개 화면 API를 통해 환경에 저장된 이벤트 및 메타데이터에 대한 데이터를 쿼리할 수 있습니다. 이러한 API는 [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)에도 사용됩니다.

Time Series Insights에서 사용할 수 있는 세 가지 기본 API 범주는 다음과 같습니다.

* **환경 api**: 이러한 api는 Time Series Insights 환경 자체에 대 한 쿼리를 사용 하도록 설정 합니다. 쿼리 예제로는 호출자가 액세스 권한을 가진 환경 및 환경 메타데이터 목록을 들 수 있습니다.
* **시계열 모델-쿼리 (TSM-Q) api**: 시계열 모델의 환경 부분에 저장 된 메타 데이터에 대 한 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업을 활성화 합니다. 인스턴스, 형식 및 계층 구조를 예로 들 수 있습니다.
* **TSQ (Time Series Query) api**: 원본 공급자에서 기록 된 원격 분석 또는 이벤트 데이터를 검색 하거나, 스칼라 및 집계 함수를 사용 하 여 변수 부분을 사용 하 여 데이터를 줄일 수 있습니다. 이러한 Api는 시계열 데이터에 대 한 계산을 변환 하 고 결합 하 고 적용 하는 작업을 수행할 수 있습니다.

Time Series Insights은 계산을 표현 하는 데 사용 되는 다양 한 문자열 기반 식 언어인 [TSX (시계열 식)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)를 사용 합니다.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights 미리 보기 핵심 API

다음과 같은 핵심 API가 지원됩니다.

[![시계열 쿼리 개요](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

다음과 같은 Environment API를 사용할 수 있습니다.

* [환경 가져오기 API](/rest/api/time-series-insights/management/environments/get): 호출자에 게 액세스 권한이 부여 된 환경 목록을 반환 합니다.
* [환경 가용성 API 가져오기](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): `$ts`이벤트 타임 스탬프에 대 한 이벤트 수의 분포를 반환 합니다. 이 API는 이벤트가 있는 경우 이벤트 수를 반환하여 타임스탬프에 이벤트가 있는지 확인합니다.
* [이벤트 스키마 가져오기 API](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): 지정 된 검색 범위에 대 한 이벤트 스키마 메타 데이터를 반환 합니다. 이 API는 지정된 검색 범위의 스키마에서 사용 가능한 모든 메타데이터 및 속성을 검색할 수 있습니다.

## <a name="time-series-model-query-tsm-q-apis"></a>TSM-Q(Time Series Model-Query) API

다음 시계열 모델 쿼리 Api를 사용할 수 있습니다. 이러한 대부분의 Api는 일괄 처리 실행 작업을 지원 하 여 여러 시계열 모델 엔터티에 대해 일괄 처리 CRUD 작업을 활성화 합니다.

* [모델 설정 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): 기본 형식 및 환경의 모델 이름에 대해 *GET* 및 *PATCH* 를 사용 하도록 설정 합니다.
* [TYPES API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): 시계열 유형 및 연관 된 변수에 대 한 CRUD를 사용 하도록 설정 합니다.
* [계층 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): 시계열 계층 구조와 관련 필드 경로에 대 한 CRUD를 사용 하도록 설정 합니다.
* [INSTANCES API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): 시계열 인스턴스와 연결 된 인스턴스 필드에 대해 CRUD를 사용 하도록 설정 합니다. 또한 인스턴스 API는 다음과 같은 작업을 지원 합니다.
  * [검색](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): 인스턴스 특성에 따라 시계열 인스턴스 검색에 대 한 적중의 일부 목록을 검색 합니다.
  * [제안](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): 인스턴스 특성에 따라 시계열 인스턴스 검색에 대 한 일부 적중 목록을 검색 하 고 제안 합니다.

## <a name="time-series-query-tsq-apis"></a>TSQ(Time Series Query) API

다음 시계열 쿼리 Api를 사용할 수 있습니다. 이러한 Api는 Time Series Insights에서 지원 되는 모든 다중 계층 저장소에서 사용할 수 있습니다. 쿼리 URL 매개 변수를 사용 하 여 쿼리를 실행 해야 하는 [저장소 유형을](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) 지정 합니다.

* [Get EVENTS API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): 소스 공급자의 Time Series Insights에 기록 될 때 이벤트에서 Time Series Insights 데이터를 쿼리하고 검색할 수 있도록 합니다. 이 API는 지정 된 시계열 ID 및 검색 범위에 대 한 원시 이벤트 검색을 허용 합니다. 이 API는 선택한 입력에 대 한 전체 데이터 집합을 검색 하는 페이지 매김을 지원 합니다. 

* [SERIES API 가져오기](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): 연결 된 데이터를 사용 하 여 캡처된 이벤트에서 Time Series Insights 데이터를 쿼리하고 검색할 수 있도록 합니다. 반환되는 값은 모델에 정의된 변수 또는 인라인으로 제공된 변수를 기반으로 합니다. 이 API는 선택한 입력에 대 한 전체 데이터 집합을 검색 하는 페이지 매김을 지원 합니다. 이 API는 계산 된 속성이 나 열을 정의 하는 데 도움이 됩니다.

    >[!NOTE]
    > 집계 절은 모델에 정의되거나 인라인으로 제공된 경우에도 무시됩니다.

  Get Series API는 각 간격의 값에 대한 Time Series 값을 반환합니다. 시계열 값은 쿼리에서 출력 JSON에 사용 Time Series Insights 하는 형식입니다. 반환되는 값은 Time Series ID 및 제공된 변수 세트를 기반으로 합니다.

* [집계 시리즈 API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): 기록 된 데이터를 샘플링 하 고 집계 하 여 캡처된 이벤트에서 Time Series Insights 데이터를 쿼리하고 검색할 수 있도록 합니다. 이 API는 [연속 토큰](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)을 사용 하 여 비연속적 실행을 지원 합니다.

  Aggregate Series API는 각 간격의 값에 대한 Time Series 값을 반환합니다. 값은 Time Series ID 및 제공된 변수 세트를 기반으로 합니다. Aggregate Series API는 Time Series Model에 저장된 값 또는 인라인으로 제공된 값을 사용하여 데이터를 집계 또는 샘플링함으로써 크기를 줄입니다.

## <a name="next-steps"></a>다음 단계

- Azure Time Series Insights 미리 보기에서 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) 에 대해 자세히 알아보세요.
- Time Series Insights 미리 보기 [데이터 모델링](./time-series-insights-update-tsm.md) 문서를 참조 하세요.
- [시계열 ID를 선택 하기 위한 모범 사례](./time-series-insights-update-how-to-id.md)를 검색 합니다.
