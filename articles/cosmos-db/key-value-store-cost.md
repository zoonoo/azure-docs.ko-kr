---
title: 키 값 저장소로 Azure Cosmos DB에 대한 요청 단위 요금
description: 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB의 요청 단위 요금에 대해 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647514"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure 코스모스 DB를 핵심 가치 저장소로 – 비용 개요

Azure Cosmos DB는 대규모의 고가용성 애플리케이션을 쉽게 빌드하기 위한 전역으로 분산된 다중 모델 데이터베이스 서비스입니다. 기본적으로 Azure Cosmos DB는 수집한 모든 데이터를 자동으로 효율적으로 인덱싱합니다. 이렇게 하면 데이터에 대한 빠르고 일관된 [SQL(및](how-to-sql-query.md) [JavaScript)](stored-procedures-triggers-udfs.md)쿼리를 사용할 수 있습니다. 

이 문서는 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB 비용에 대해 설명합니다. 쓰기 작업에는 데이터 항목의 삽입, 바꾸기, 삭제 및 업서트가 포함됩니다. 모든 다중 리전 계정에 대해 99.999%의 가용성 SLA를 보장하는 것 외에도 Azure Cosmos DB는 99번째 백분위수에서 읽기 및 (인덱싱된) 쓰기에 대해 10ms 대기 시간을 <보장합니다. 

## <a name="why-we-use-request-units-rus"></a>RU(요청 단위)를 사용하는 이유

Azure Cosmos DB 성능은 [RU/s(요청 단위)로](request-units.md) 표현된 프로비저닝된 처리량의 양을 기반으로 합니다. 프로비저닝은 두 번째 세분성으로 되어 있으며 RU/s에서[구입합니다(시간당 청구와 혼동되지 않음).](https://azure.microsoft.com/pricing/details/cosmos-db/) R은 응용 프로그램에 필요한 처리량의 프로비저닝을 단순화하는 논리적 추상화(통화)로 간주되어야 합니다. 사용자는 읽기와 쓰기 처리량을 구분할 필요가 없습니다. 단일 통화 모델의 RU를 사용하면 읽기 및 쓰기 간에 프로비전된 용량을 효율적으로 공유할 수 있습니다. 이 프로비저닝된 용량 모델을 통해 서비스는 **예측 가능하고 일관된 처리량, 보장된 낮은 대기 시간 및 고가용성을**제공할 수 있습니다. 마지막으로 RU 모델이 처리량을 묘사하는 데 사용되지만 프로비저닝된 각 RU에는 정의된 양의 리소스(예: 메모리, 코어/CPU 및 IOPS)도 있습니다.

Cosmos DB는 전 세계적으로 분산된 데이터베이스 시스템으로 대기 시간, 처리량, 일관성 및 고가용성을 포괄하는 포괄적인 SLA를 제공하는 유일한 Azure 서비스입니다. 프로비저닝한 처리량은 Cosmos 계정과 연결된 각 지역에 적용됩니다. 읽기의 경우 Cosmos DB는 선택 가능한 여러 개의 잘 정의된 [일관성 수준](consistency-levels.md)을 제공합니다. 

다음 표에서는 기본 자동 인덱싱이 꺼져 있는 크기 1KB 및 100KB의 데이터 항목을 기반으로 읽기 및 쓰기 작업을 수행하는 데 필요한 RUs 수를 보여 둡니다. 

|항목 크기|1 읽기|1 쓰기|
|-------------|------|-------|
|1KB|1RU|5RU|
|100KB|10RU|50RU|

## <a name="cost-of-reads-and-writes"></a>읽기 및 쓰기의 비용

1,000 RU/s를 프로비전하는 경우 시간당 360만 RU에 해당하며 시간당 0.08 USD(미국 및 유럽)의 비용이 듭니다. 1KB 크기의 데이터 항목의 경우 프로비저닝된 처리량을 사용하여 360만 개의 읽기 또는 0.72백만 개의 쓰기(360만 RU/5)를 소비할 수 있습니다. 백만 개의 읽기 및 쓰기로 정규화하면 비용은 읽기 의 0.022 /백만 ($ 0.08 / 3.6) 및 쓰기의 0.111 / 백만 ($ 0.08 / 0.72)입니다. 백만당 비용은 아래 표에 나와 있는 것처럼 최소화됩니다.

|항목 크기|100만 읽기 비용|100만 건의 쓰기 비용|
|-------------|-------|--------|
|1KB|$0.022|$0.111|
|100KB|$0.222|$1.111|


대부분의 기본 Blob 또는 개체 저장소 서비스는 백만 읽기 트랜잭션당 $0.40, 백만 쓰기 트랜잭션당 $5가 부과됩니다. 코스모스 DB를 최적으로 사용하면 다른 솔루션(1KB 트랜잭션의 경우)보다 최대 98% 저렴할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [RU 계산기를](https://cosmos.azure.com/capacitycalculator/) 사용하여 워크로드에 대한 처리량을 예측합니다.

