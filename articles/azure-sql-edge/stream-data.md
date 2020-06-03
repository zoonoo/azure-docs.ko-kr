---
title: Azure SQL Edge에서 데이터 스트리밍(미리 보기)
description: Azure SQL Edge에서의 데이터 스트리밍에 대한 정보(미리 보기)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ea1bb05e111432a45fffa3b770e01c6da9a076b6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233119"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Azure SQL Edge에서 데이터 스트리밍(미리 보기)

Azure SQL Edge(미리 보기)는 두 가지 데이터 스트리밍 구현 옵션을 제공합니다. 

1. Azure에서 만든 Azure Streaming Analytics Edge 작업을 배포합니다. Azure SQL Edge에서 Azure Streaming Analytics Edge 작업을 배포하는 방법에 대한 자세한 내용은 [Azure Stream Analytics 작업 배포](deploy-dacpac.md)를 참조하십시오.
2. 새로운 **T-SQL 스트리밍** 기능을 사용하여 Azure에서 스트리밍 작업을 구성할 필요 없이 SQL Edge에서 스트리밍 작업을 만들 수 있습니다. 

SQL Edge에서 데이터 스트리밍을 구현하기 위해 두 가지 옵션을 모두 사용할 수 있으나 하나만 사용하는 것이 좋습니다. 두 옵션을 모두 사용하는 경우 데이터 스트리밍 작업의 기능에 영향을 미치는 경합 상태가 있을 수 있습니다.

이 문서의 나머지 부분에서는 여러 소스에서 동시에 대량의 고속 스트리밍 데이터를 분석하고 처리하기 위해 실시간 데이터 스트리밍, 분석, 이벤트 처리를 제공하는 새로운 기능인 **T-SQL 스트리밍**에 대해 설명합니다. *T-SQL 스트리밍*은 Microsoft Azure의 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)을 지원하는 것과 동일한 고성능 스트리밍 엔진을 사용하여 빌드되었으며, 에지에서 실행되는 Azure Stream Analytics를 통해 제공되는 기능과 비슷한 기능을 지원합니다.

Azure Stream Analytics와 마찬가지로 T-SQL 스트리밍을 사용하면 디바이스, 센서 및 애플리케이션을 포함한 여러 IoT 입력 원본에서 추출한 정보의 패턴과 관계를 인식할 수 있습니다. 이러한 패턴을 사용하여 경고를 만들거나, 시각화 솔루션에 정보를 공급하거나, 나중에 사용할 수 있도록 데이터를 저장하는 등의 작업을 트리거하고 워크플로를 시작할 수 있습니다. 

다음 시나리오는 T-SQL 스트리밍을 사용할 수 있는 경우의 예입니다.

* IoT 디바이스에서 실시간 원격 분석 스트림 분석.
* 자율 및 드라이버가 없는 자동차에서 생성된 데이터의 실시간 분석.
* 높은 가치 산업 또는 제조 자산의 원격 모니터링 및 예측 유지 관리.
* 농업 또는 발전소에서 IoT 센서 판독값의 변칙 검색 및/또는 패턴 인식.

## <a name="how-does-t-sql-streaming-work"></a>T-SQL 스트리밍의 작동 방식

T-SQL 스트리밍은 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)와 완전히 동일한 방식으로 작동합니다. 예를 들어 실시간 데이터 스트리밍을 처리하는 데에 스트리밍 작업의 개념을 사용합니다. 

스트림 분석 작업은 다음으로 구성됩니다.

- 스트림 입력 - 스트림 입력은 데이터 스트림을 읽어오는 데이터 원본으로의 연결을 정의합니다. 현재 Azure SQL Edge는 다음과 같은 스트림 입력 유형을 지원합니다.
    - 에지 허브
    - Kafka - 현재 Kafka 입력 지원은 Azure SQL Edge의 Intel/AMD64 버전에서만 사용할 수 있습니다.

- 스트림 출력 - 스트림 출력은 데이터 스트림을 쓰는 데이터 원본으로의 연결을 정의합니다. 현재 Azure SQL Edge는 다음과 같은 스트림 출력 유형을 지원합니다.
    - 에지 허브
    - SQL - SQL 출력은 SQL Edge 인스턴스 내의 로컬 데이터베이스 또는 원격 SQL Server 또는 Azure SQL Database일 수 있습니다. 
    - Azure Blob Storage

- 스트림 쿼리 - 스트림 쿼리는 스트림 출력에 기록되기 전에 입력 스트림에 적용되어야 하는 변환, 집계, 필터링, 정렬 및 참가를 정의합니다. 스트림 쿼리는 Azure Stream Analytics에서 사용하는 것과 동일한 쿼리 언어를 기반으로 합니다. Azure Stream Analytics 쿼리 언어에 대한 자세한 내용은 [Stream Analytics 쿼리 언어](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)를 참조하십시오.

> [!IMPORTANT]
> T-SQL 스트리밍은 Azure Stream Analytics와 달리 현재 [조회에 대한 참조 데이터 사용](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) 또는 [스트리밍 작업에서 UDF 및 UDA 사용](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)을 지원하지 않습니다.

> [!NOTE]
> T-SQL 스트리밍은 Azure Stream Analytics에서 지원되는 언어 노출 영역의 하위 집합만 지원합니다. Azure Stream Analytics 쿼리 언어에 대한 자세한 내용은 [Stream Analytics 쿼리 언어](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)를 확인하십시오.

## <a name="limitations-and-restrictions"></a>제한 사항

다음과 같은 제한 사항이 T-SQL 스트리밍에 적용됩니다. 

- 한 번에 하나의 스트리밍 작업만 활성 상태일 수 있습니다. 다른 작업을 시작하기 전에 이미 실행 중인 작업을 중지해야 합니다.
- 모든 스트리밍 작업은 단일 스레드입니다. 스트리밍 작업에 여러 개의 쿼리가 포함된 경우, 모든 쿼리는 순서대로 평가됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에서 Stream Analytics 작업 만들기(미리 보기)](create-stream-analytics-job.md)
- [Azure SQL Edge에서 스트림 작업과 관련된 메타데이터 보기(미리 보기) ](streaming-catalog-views.md)
- [외부 스트림 만들기](create-external-stream-transact-sql.md)