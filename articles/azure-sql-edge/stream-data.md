---
title: Azure SQL Edge의 데이터 스트리밍
description: Azure SQL Edge의 데이터 스트리밍에 대해 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ca22b3d2c00bfef128455df4ad6b9bb6411f8a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900563"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Azure SQL Edge의 데이터 스트리밍

Azure SQL Edge는 T-sql 스트리밍 이라는 데이터 스트리밍 기능의 기본 구현을 제공 합니다. 실시간 데이터 스트리밍, 분석 및 이벤트 처리 기능을 제공 하 여 여러 원본에서 동시에 많은 양의 고속 스트리밍 데이터를 분석 하 고 처리 합니다. T-sql 스트리밍은 Microsoft Azure에서 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) 을 지 원하는 동일한 고성능 스트리밍 엔진을 사용 하 여 빌드됩니다. 이 기능은 edge에서 실행 되는 Azure Stream Analytics에서 제공 하는 것과 비슷한 기능 집합을 지원 합니다.

Stream Analytics와 마찬가지로 T-sql 스트리밍은 장치, 센서 및 응용 프로그램을 포함 하 여 여러 IoT 입력 원본에서 추출 된 정보의 패턴과 관계를 인식 합니다. 이러한 패턴을 사용 하 여 작업을 트리거하고 워크플로를 시작할 수 있습니다. 예를 들어 경고를 만들거나, 보고 또는 시각화 솔루션에 정보를 피드 하거나, 나중에 사용할 수 있도록 데이터를 저장할 수 있습니다. 

T-sql 스트리밍은 다음과 같은 작업을 지원할 수 있습니다.

* IoT 디바이스에서 실시간 원격 분석 스트림 분석.
* 자치 및 무인 차량의 생성 된 데이터에 대 한 실시간 분석을 사용 합니다.
* 높은 가치의 산업 또는 제조 자산의 원격 모니터링 및 예측 유지 관리를 사용 합니다.
* 농업 또는 에너지 팜에서 IoT 센서 판독값의 변칙 검색 및 패턴 인식을 사용 합니다.

## <a name="how-does-t-sql-streaming-work"></a>T-sql 스트리밍이 어떻게 작동 하나요?

T-sql 스트리밍은 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)와 정확히 같은 방식으로 작동 합니다. 예를 들어, 스트리밍 *작업* 의 개념을 사용 하 여 실시간 데이터 스트리밍을 처리 합니다. 

Stream analytics 작업은 다음으로 구성 됩니다.

- **스트림 입력**: 데이터 스트림을 읽을 데이터 소스에 대 한 연결을 정의 합니다. 현재 Azure SQL Edge는 다음과 같은 스트림 입력 유형을 지원합니다.
    - 에지 허브
    - Kafka 어 (Kafka 입력에 대 한 지원은 현재 Azure SQL Edge의 Intel/AMD64 버전 에서만 사용 가능 합니다.)

- **스트림 출력**: 데이터 스트림을 쓸 데이터 원본에 대 한 연결을 정의 합니다. 현재 Azure SQL Edge는 다음과 같은 스트림 출력 유형을 지원합니다.
    - 에지 허브
    - Sql (sql 출력은 Azure SQL Edge 인스턴스 내의 로컬 데이터베이스 또는 원격 SQL Server 또는 Azure SQL Database 일 수 있습니다.) 

- **스트림 쿼리**: 스트림 출력에 기록 되기 전에 입력 스트림에 적용할 변환, 집계, 필터, 정렬 및 조인을 정의 합니다. 스트림 쿼리는 Stream Analytics에서 사용 하는 것과 동일한 쿼리 언어를 기반으로 합니다. 자세한 내용은 [Stream Analytics 쿼리 언어](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)를 참조 하세요.

> [!IMPORTANT]
> Stream Analytics와 달리 t-sql 스트리밍은 현재 [조회에 대 한 참조 데이터를 사용](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) 하거나 [스트림 작업에서 UDF의 및 UDA를 사용 하는 것](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)을 지원 하지 않습니다.

> [!NOTE]
> T-sql 스트리밍은 Stream Analytics에서 지 원하는 언어 노출 영역의 하위 집합만 지원 합니다. 자세한 내용은 [Stream Analytics 쿼리 언어](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)를 참조 하세요.

## <a name="limitations-and-restrictions"></a>제한 사항

T-sql 스트리밍에는 다음과 같은 제한 사항이 적용 됩니다. 

- 특정 시간에 하나의 스트리밍 작업만 활성 상태일 수 있습니다. 이미 실행 중인 작업은 다른 작업을 시작 하기 전에 중지 해야 합니다.
- 각 스트리밍 작업 실행은 단일 스레드입니다. 스트리밍 작업에 여러 개의 쿼리가 포함 되어 있으면 각 쿼리는 일련의 순서로 계산 됩니다.
- Azure SQL Edge에서 스트리밍 작업을 중지 하면 다음 스트리밍 작업을 시작할 수 있을 때까지 약간의 지연이 있을 수 있습니다. 이 지연은 작업 중지 요청에 대 한 응답으로 기본 스트리밍 프로세스를 중지 한 다음 작업 시작 요청에 대 한 응답으로 다시 시작 해야 하기 때문에 발생 합니다. 
- T-sql 스트리밍은 kafka 스트림에 대해 32 파티션을 기반으로 합니다. 더 높은 파티션 수를 구성 하려고 하면 오류가 발생 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에서 Stream Analytics 작업 만들기 ](create-stream-analytics-job.md)
- [Azure SQL Edge에서 스트림 작업과 연결 된 메타 데이터 보기 ](streaming-catalog-views.md)
- [외부 스트림 만들기](create-external-stream-transact-sql.md)
