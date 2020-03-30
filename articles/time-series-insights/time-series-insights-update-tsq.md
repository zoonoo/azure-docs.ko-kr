---
title: 미리 보기에서 데이터 쿼리 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기에서 개념 및 HTTP REST API 개요를 쿼리하는 데이터 입니다.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284894"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure Time Series 인사이트 미리 보기에서 데이터 쿼리

Azure Time Series Insights를 사용하면 공용 표면 API를 통해 환경에 저장된 이벤트 및 메타데이터에 대한 데이터 쿼리를 수행할 수 있습니다. 이러한 API는 [또한 타임시리즈 인사이트 탐색기에서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)사용됩니다.

Time Series Insights에서 사용할 수 있는 세 가지 기본 API 범주는 다음과 같습니다.

* **환경 API**: 이러한 API는 타임시리즈 인사이트 환경 자체에서 쿼리를 활성화합니다. 호출자 액세스 권한이 있는 환경 및 환경 메타데이터를 수집하는 데 사용할 수 있습니다.
* **TSM-Q(열렬 모델 쿼리) API**: 환경의 타임시리즈 모델에 저장된 메타데이터에 대해 CRUD(제거) 작업을 생성, 읽기, 업데이트 및 삭제할 수 있습니다. 인스턴스, 형식 및 계층구조에 액세스하고 편집하는 데 사용할 수 있습니다.
* **TSQ(시계열 쿼리) API**: 원본 공급자에서 기록된 원격 분석 또는 이벤트 데이터를 검색할 수 있으며 고급 스칼라 및 집계 함수를 사용하여 데이터에 대한 수행 계산 및 집계를 지원합니다.

Time Series Insights는 계산을 표현하기 위해 풍부한 문자열 기반 표현 언어인 [TSX(타임시리즈 표현)를](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)사용합니다.

## <a name="azure-time-series-insights-core-apis"></a>Azure 타임시리즈 인사이트 핵심 API

다음과 같은 핵심 API가 지원됩니다.

[![열렬 쿼리 개요](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

* [환경 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): 호출자의 액세스 권한이 있는 환경 목록을 반환합니다.
* [환경 가용성 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): 이벤트 타임스탬프를 `$ts`통해 이벤트 수의 분포를 반환합니다. 이 API는 시간 간격으로 끊어진 이벤트 수를 반환하여 환경에 이벤트가 있는지 확인하는 데 도움이 됩니다.
* [이벤트 스키마 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): 지정된 검색 범위에 대한 이벤트 스키마 메타데이터를 반환합니다. 이 API는 지정된 검색 범위의 스키마에서 사용 가능한 모든 메타데이터 및 속성을 검색할 수 있습니다.

## <a name="time-series-model-query-tsm-q-apis"></a>TSM-Q(Time Series Model-Query) API

이러한 API의 대부분은 여러 열렬 모델 엔터티에서 일괄 처리 CRUD 작업을 사용하도록 일괄 처리 실행 작업을 지원합니다.

* [모델 설정 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): 기본 유형 및 환경의 모델 이름에 *GET* 및 *PATCH를* 활성화합니다.
* [형식 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): 시간계 유형 및 관련 변수에서 CRUD를 활성화합니다.
* [계층 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): 시간계 계층 구조 및 관련 필드 경로에서 CRUD를 활성화합니다.
* [인스턴스 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): 타임시리즈 인스턴스 및 관련 인스턴스 필드에서 CRUD를 활성화합니다. 또한 인스턴스 API는 다음 작업을 지원합니다.
  * [검색](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): 인스턴스 특성을 기반으로 한 시간계 인스턴스 검색시 일부 조회 목록을 검색합니다.
  * [제안](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): 인스턴스 속성을 기반으로 한 시간계 인스턴스 검색에서 일부 조회 목록을 검색하고 제안합니다.

## <a name="time-series-query-tsq-apis"></a>TSQ(Time Series Query) API

이러한 API는 타임시리즈 인사이트(Time Series Insights)의 다층 스토리지 솔루션의 모든 매장에서 사용할 수 있습니다. 쿼리 URL 매개 변수는 쿼리가 실행해야 하는 [저장소 유형을](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) 지정하는 데 사용됩니다.

* [이벤트 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): 원본 공급자의 타임시리즈 Insights에 기록되는 원시 이벤트 및 관련 이벤트 타임스탬프의 쿼리 및 검색을 활성화합니다. 이 API를 사용하면 지정된 시간계 ID 및 검색 범위에 대한 원시 이벤트를 검색할 수 있습니다. 이 API는 페이지 설정을 지원하여 선택한 입력에 대한 전체 응답 데이터 집합을 검색합니다. 

* [시리즈 API 받기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): 원시 이벤트에 변수로 정의된 계산을 적용하여 계산된 값과 관련 이벤트 타임스탬프의 쿼리 및 검색을 활성화합니다. 이러한 변수는 타임시리즈 모델에서 정의하거나 쿼리에서 인라인으로 정의할 수 있습니다. 이 API는 페이지 설정을 지원하여 선택한 입력에 대한 전체 응답 데이터 집합을 검색합니다. 

* [집계 시리즈 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): 원시 이벤트에 변수로 정의된 계산을 적용하여 집계된 값과 관련 간격 타임스탬프의 쿼리 및 검색을 가능하게 합니다. 이러한 변수는 타임시리즈 모델에서 정의하거나 쿼리에서 인라인으로 정의할 수 있습니다. 이 API는 페이지 설정을 지원하여 선택한 입력에 대한 전체 응답 데이터 집합을 검색합니다. 
  
  지정된 검색 범위 및 간격에 대해 이 API는 타임시리즈 ID에 대한 간격당 변수당 집계된 응답을 반환합니다. 응답 데이터 집합의 간격 수는 epoch 틱(유닉스 시대 - 1970년 1월 1일 이후 경과된 밀리초 수)을 계산하고 틱을 쿼리에 지정된 간격 범위 크기로 나누어 계산합니다.

  응답 집합에서 반환되는 타임스탬프는 간격에서 샘플링된 이벤트가 아니라 왼쪽 간격 경계입니다. 

## <a name="next-steps"></a>다음 단계

- [타임시리즈 모델에서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)정의할 수 있는 다양한 변수에 대해 자세히 알아보기.
- [타임시리즈 인사이트 탐색기에서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)데이터를 쿼리하는 방법에 대해 자세히 알아보기.
