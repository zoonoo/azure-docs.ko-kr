---
title: 키 값 저장소로 Azure Cosmos DB에 대한 요청 단위 요금
description: 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB의 요청 단위 요금에 대해 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339307"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>키 값 저장소로서 Azure Cosmos DB – 비용 개요
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB는 대규모의 고가용성 애플리케이션을 쉽게 빌드하기 위한 전역으로 분산된 다중 모델 데이터베이스 서비스입니다. 기본적으로 Azure Cosmos DB는 수집하는 모든 데이터를 자동으로 효율적으로 인덱싱합니다. 이를 통해 데이터에 대해 빠르고 일관된 [SQL](./sql-query-getting-started.md)(및 [JavaScript](stored-procedures-triggers-udfs.md)) 쿼리가 가능해집니다. 

이 문서는 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB 비용에 대해 설명합니다. 쓰기 작업에는 데이터 항목의 삽입, 바꾸기, 삭제 및 upsert가 포함됩니다. Azure Cosmos DB는 모든 다중 지역 계정에 99.999% 고가용성 SLA를 보장하는 것 외에도 99번째 백분위수로 읽기 및 (인덱싱된) 쓰기에 10ms 미만의 대기 시간을 보장합니다. 

## <a name="why-we-use-request-units-rus"></a>RU(요청 단위)를 사용하는 이유

Azure Cosmos DB 성능은 [요청 단위](request-units.md)(RU/초)로 표현된 프로비저닝된 처리량을 기준으로 합니다. 프로비저닝은 초 단위이며 RU/초 단위로 구매합니다([시간당 청구와 혼동하지 말 것](https://azure.microsoft.com/pricing/details/cosmos-db/)). RU는 애플리케이션의 필수 처리량 프로비저닝을 간소화하는 논리적 추상화(통화)로 간주해야 합니다. 사용자는 읽기 및 쓰기 처리량을 구별해서 생각할 필요가 없습니다. 단일 통화 모델의 RU를 사용하면 읽기 및 쓰기 간에 프로비전된 용량을 효율적으로 공유할 수 있습니다. 이 프로비저닝된 용량 모델을 사용하면 서비스는 **예측 가능하고 일관된 처리량, 보장된 짧은 대기 시간 및 고가용성** 을 제공할 수 있습니다. 마지막으로, RU 모델은 처리량을 표현하는 데 사용되지만 프로비저닝된 각 RU에는 정의된 양의 리소스(예: 메모리, 코어/CPU, IOPS)도 있습니다.

전역적으로 분산된 데이터베이스 시스템인 Cosmos DB는 대기 시간, 처리량, 일관성, 고가용성 등을 포함하는 포괄적인 SLA를 제공하는 유일한 Azure 서비스입니다. 프로비저닝하는 처리량은 Cosmos 계정과 연결된 각 하위 지역에 적용됩니다. 읽기의 경우 Cosmos DB는 선택 가능한 여러 개의 잘 정의된 [일관성 수준](consistency-levels.md)을 제공합니다. 

다음 표에서는 기본 자동 인덱싱이 꺼진 크기가 1KB 및 100KB인 데이터 항목을 기반으로 읽기 및 쓰기 작업을 수행하는 데 필요한 RU 수를 보여 줍니다. 

|항목 크기|1 읽기|1 쓰기|
|-------------|------|-------|
|1KB|1RU|5RU|
|100KB|10RU|50RU|

## <a name="cost-of-reads-and-writes"></a>읽기 및 쓰기의 비용

초당 1,000RU를 프로비저닝하는 경우 이 크기는 시간당 360만 RU에 해당하며 비용은 시간당 $0.08입니다(미국 및 유럽). 1KB 크기 데이터 항목의 경우 프로비저닝된 처리량을 사용하여 360만 회 읽기 또는 72만 회 쓰기(초당 360만 RU)를 소비할 수 있음을 의미합니다. 100만 회 읽기 및 쓰기로 정규화되는 비용은 읽기 100만 회당 $0.022($0.08/3.6) 및 쓰기 100만 회당 $0.111($0.08/0.72)입니다. 백만당 비용은 아래 표에 나와 있는 것처럼 최소화됩니다.

|항목 크기|100만 회 읽기 비용|100만 회 쓰기 비용|
|-------------|-------|--------|
|1KB|$0.022|$0.111|
|100KB|$0.222|$1.111|


대부분의 기본 Blob 또는 개체 저장소 서비스는 백만 읽기 트랜잭션당 $0.40, 백만 쓰기 트랜잭션당 $5가 부과됩니다. 최적으로 사용하는 경우 Cosmos DB는 이러한 다른 솔루션보다 최대 98% 더 저렴할 수 있습니다(1KB 트랜잭션에 대해).

## <a name="next-steps"></a>다음 단계

* [RU 계산기](https://cosmos.azure.com/capacitycalculator/)를 사용하여 워크로드의 처리량을 예측합니다.