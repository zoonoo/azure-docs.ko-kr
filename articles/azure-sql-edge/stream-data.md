---
title: Azure SQL Edge의 데이터 스트리밍
description: Azure SQL Edge의 데이터 스트리밍에 대한 정보
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392115"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Azure SQL Edge의 데이터 스트리밍

Azure SQL Edge는 T-SQL 스트리밍이라는 데이터 스트리밍 기능의 기본 구현을 제공합니다. 이 기능은 여러 소스에서 동시에 대량의 고속 스트리밍 데이터를 분석하고 처리하기 위해 실시간 데이터 스트리밍, 분석, 이벤트 처리를 제공합니다. T-SQL 스트리밍은 Microsoft Azure에서 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)를 지원하는 동일한 고성능 스트리밍 엔진을 사용하여 빌드됩니다. 이 기능은 에지에서 실행되는 Azure Stream Analytics에서 제공하는 것과 비슷한 기능 세트를 지원합니다.

Stream Analytics와 마찬가지로 T-SQL 스트리밍을 사용하면 디바이스, 센서 및 애플리케이션을 포함한 여러 IoT 입력 원본에서 추출한 정보의 패턴과 관계를 인식할 수 있습니다. 이러한 패턴을 사용하여 작업을 트리거하고 워크플로를 시작할 수 있습니다. 예를 들어 경고를 만들거나, 보고 또는 시각화 솔루션에 정보를 피드하거나, 나중에 사용할 수 있도록 데이터를 저장할 수 있습니다. 

T-SQL 스트리밍은 다음과 같은 작업을 지원할 수 있습니다.

* IoT 디바이스에서 실시간 원격 분석 스트림 분석.
* 자율 및 드라이버가 없는 자동차에서 생성된 데이터의 실시간을 분석합니다.
* 높은 가치 산업 또는 제조 자산의 원격 모니터링 및 예측 유지 관리를 사용합니다.
* 농업 또는 발전소에서 IoT 센서 판독값의 변칙 검색 및/또는 패턴을 인식합니다.

## <a name="how-does-t-sql-streaming-work"></a>T-SQL 스트리밍의 작동 방식

T-SQL 스트리밍은 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work)와 정확히 같은 방식으로 작동합니다. 예를 들어 실시간 데이터 스트리밍을 처리하는 데에 스트리밍 *작업* 의 개념을 사용합니다. 

스트림 분석 작업은 다음으로 구성됩니다.

- **스트림 입력**: 데이터 스트림을 읽어오는 데이터 원본으로의 연결을 정의합니다. 현재 Azure SQL Edge는 다음과 같은 스트림 입력 유형을 지원합니다.
    - 에지 허브
    - Kafka(현재 Kafka 입력 지원은 Azure SQL Edge의 Intel/AMD64 버전에서만 사용할 수 있습니다.)

- **스트림 출력**: 데이터 스트림을 쓰는 데이터 원본으로의 연결을 정의합니다. 현재 Azure SQL Edge는 다음과 같은 스트림 출력 유형을 지원합니다.
    - 에지 허브
    - SQL(SQL 출력은 Azure SQL Edge 인스턴스 내의 로컬 데이터베이스 또는 원격 SQL Server 또는 Azure SQL Database일 수 있습니다.) 

- **스트림 쿼리**: 스트림 출력에 기록되기 전에 입력 스트림에 적용되어야 하는 변환, 집계, 필터링, 정렬 및 참가를 정의합니다. 스트림 쿼리는 Stream Analytics에서 사용하는 것과 동일한 쿼리 언어를 기반으로 합니다. 자세한 내용은 [Stream Analytics 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요.

> [!IMPORTANT]
> T-SQL 스트리밍은 Stream Analytics와 달리 현재 [조회에 대한 참조 데이터 사용](../stream-analytics/stream-analytics-use-reference-data.md) 또는 [스트리밍 작업에서 UDF 및 UDA 사용](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)을 지원하지 않습니다.

> [!NOTE]
> T-SQL 스트리밍은 Stream Analytics에서 지원되는 언어 노출 영역의 하위 집합만 지원합니다. 자세한 내용은 [Stream Analytics 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요.

## <a name="limitations-and-restrictions"></a>제한 사항

다음과 같은 제한 사항이 T-SQL 스트리밍에 적용됩니다. 

- 한 번에 하나의 스트리밍 작업만 활성 상태일 수 있습니다. 다른 작업을 시작하기 전에 이미 실행 중인 작업을 중지해야 합니다.
- 모든 스트리밍 작업은 단일 스레드입니다. 스트리밍 작업에 여러 개의 쿼리가 포함된 경우, 모든 쿼리는 순서대로 평가됩니다.
- Azure SQL Edge에서 스트리밍 작업을 중지하면 다음 스트리밍 작업을 시작할 수 있을 때까지 약간의 지연이 있을 수 있습니다. 이 지연은 작업 중지 요청에 대한 응답으로 기본 스트리밍 프로세스를 중지한 다음, 작업 시작 요청에 대한 응답으로 다시 시작해야 하기 때문에 발생합니다. 
- T-SQL 스트리밍은 kafka 스트림에 대해 최대 32개의 파티션을 지원합니다. 더 높은 파티션 수를 구성하려고 하면 오류가 발생합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에서 Stream Analytics 작업 만들기](create-stream-analytics-job.md)
- [Azure SQL Edge에서 스트림 작업과 관련된 메타데이터 보기](streaming-catalog-views.md)
- [외부 스트림 만들기](create-external-stream-transact-sql.md)