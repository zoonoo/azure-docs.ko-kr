---
title: "Azure Cosmos DB 소개 | Microsoft Docs"
description: "Azure Cosmos DB에 대해 알아봅니다. 이 전세계에 배포된 멀티모델 데이터베이스는 낮은 대기 시간, 탄력적 확장성 및 고가용성을 위해 구축되었습니다."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/14/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 5b6f016ce634fd244f1ea3b54361b9a5da9d5483
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB 시작

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 멀티모델 데이터베이스입니다. Azure Cosmos DB에서는 단추를 클릭하는 간단한 방식으로 Azure의 지리적 위치 수에 관계없이 처리량 및 저장소를 탄력적 및 독립적으로 확장할 수 있습니다. 다른 데이터베이스 서비스에서는 제공하지 않는 포괄적 [Service Level Agreement(서비스 수준 약정)](https://aka.ms/acdbsla)(SLA)를 통해 처리량, 대기 시간, 가용성 및 일관성을 보장합니다.

![Azure Cosmos DB는 탄력적 확장, 낮은 대기 시간 보증, 일관성 모델 5개, 포괄적 보장 SLA를 갖춘 전세계에 배포된 데이터베이스 서비스입니다.](./media/introduction/azure-cosmos-db.png)

Azure 구독, 요금 및 약정 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB를 활용하는 솔루션

[전역적](distribute-data-globally.md)으로 짧은 응답 시간 내에 다양한 데이터에 대규모의 읽기 및 쓰기를 처리해야 하는 모든 [웹, 모바일, 게임 및 IoT 응용 프로그램](use-cases.md)은 Azure Cosmos DB의 [보장된](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 사용 가용성, 높은 처리량, 짧은 대기 시간 및 조정 가능한 일관성으로 인해 이익을 얻게 됩니다.

## <a name="key-capabilities"></a>주요 기능
세계적으로 분산된 데이터베이스 서비스인 Azure Cosmos DB는 확장성 있고 응답성 높은 응용 프로그램을 빌드할 수 있도록 다음과 같은 기능을 제공합니다.

* **턴키 글로벌 배포**
    * [단추를 클릭](tutorial-global-distribution-documentdb.md)하여 원하는 수의 [Azure 지역](https://azure.microsoft.com/regions/)에 [데이터를 배포](distribute-data-globally.md)할 수 있습니다. 이렇게 하면 사용자가 있는 위치에 데이터를 배치하여 고객에게 가장 짧을 대기 시간을 보장할 수 있습니다. 
    * 앱에서는 Azure Cosmos DB의 멀티 호밍 API를 사용하여 가장 가까운 지역을 알고 가장 가까운 데이터 센터에 요청을 보냅니다. 이 모든 작업은 구성을 변경하지 않고 수행할 수 있습니다. 쓰기 지역 및 읽기 지역을 원하는 만큼 설정하면 나머지도 처리됩니다.

* **데이터 액세스 및 쿼리를 위한 다중 데이터 모델 및 주요 API**
    * Azure Cosmos DB를 빌드한 ARS(atom-record-sequence) 기반 데이터 모델은 문서, 그래프, 키-값, 테이블 및 열 형식 데이터 모델을 비롯한 다양한 데이터 모델을 고유하게 지원합니다.
    * 여러 언어에서 사용할 수 있는 SDK로 다음 데이터 모델의 API가 지원됩니다.
        * [DocumentDB API](documentdb-introduction.md)
        * [MongoDB API](mongodb-introduction.md)
        * [Table API](table-introduction.md)
        * [Graph(Gremlin) API](graph-introduction.md)
        * 추가 데이터 모델 제공 예정 

* **전세계에서 필요 시 탄력적으로 처리량 및 저장소 확장**
    * 데이터베이스 처리량을 [초](request-units.md) 단위로 간편하게 확장하고 원하면 언제든 변경할 수 있습니다. 
    * 크기 요구 사항에 맞게 지속적으로 저장소 크기를 [투명하게 자동으로](partition-data.md) 확장합니다.

* **응답성 높은 중요 업무용 응용 프로그램 빌드**
    * Azure Cosmos DB는 고객에게 백분위 99의 종단 간 대기 시간을 보장합니다. 
    * 일반적인 1KB 항목의 경우 Cosmos DB는 동일한 Azure 지역에서 백분위 99로 읽기 10ms 미만, 인덱스된 쓰기 15ms 미만의 종단 간 대기 시간을 보장합니다. 중간 대기 시간이 크게 낮아집니다(5ms 미만).

* **"Always On" 가용성 보장**
    * 단일 지역 내에서 99.99%의 가용성을 제공합니다.
    * 원하는 만큼의 [Azure 지역](https://azure.microsoft.com/regions)에 배포하여 더 높은 가용성을 보장합니다.
    * 데이터 무손실 보장을 통해 하나 이상의 지역에서 [오류를 시뮬레이션](regional-failover.md)합니다. 

* **올바른 방법으로 전세계에 배포되는 응용 프로그램 작성**
    * 다섯 가지 [일관성 모델](consistency-levels.md)은 항상 강력한 SQL 형태의 일관성과 NoSQL 형태의 결과적 일관성 및 그 중간 형태의 모든 범위를 제공합니다. 
  
* **환불 보장**
    * 데이터를 빠르게 가져오지 않는다면 환불할 수 있습니다. 
    * 가용성, 대기 시간, 처리량 및 일관성에 대한 [서비스 수준 계약](https://aka.ms/acdbsla)  

* **데이터베이스 스키마/인덱스 관리 없음**
    * 데이터베이스 스키마와 인덱스를 응용 프로그램의 스키마와 동기 상태로 유지하는 것에 대해 염려하지 않아도 됩니다. 이제 스키마 제약이 없는 것입니다. 
    * Azure Cosmos DB의 데이터베이스 엔진은 스키마에서 완전히 자유로우며 스키마나 인덱스 없이 자신이 수집한 모든 데이터를 자동으로 인덱싱하며 매우 빠르게 쿼리를 제공합니다. 

* **낮은 소유 비용**
    * 비 관리 솔루션에 비해 5~10배 [비용 효과적](https://aka.ms/cosmos-db-tco-paper) 
    * DynamoDB보다 세 배 저렴합니다.

## <a name="capability-comparison"></a>기능 비교

Azure Cosmos DB는 관계형 및 비관계형 데이터베이스의 최고 기능을 제공합니다.

| 기능 | 관계형 데이터베이스   | 비관계형(NoSQL) 데이터베이스 |    Azure Cosmos DB |
| --- | --- | --- | --- |
| 글로벌 분포 | 아니요 | 아니요 | 예, 멀티 호밍 API를 사용하여 30개 이상의 지역에서 턴키 배포|
| 수평적 확장 | 아니요 | 예 | 예, 독립적으로 저장소 및 처리량을 확장할 수 있습니다. | 
| 대기 시간 보장 | 아니요 | 예 | 예, <10ms인 읽기의 99% 및 <15 ms인 쓰기 | 
| 고가용성 | 아니요 | 예 | 예, Cosmos DB는 Always On이며 PACELC 장단점이 있고, 자동 및 수동 장애 조치 옵션을 제공합니다.|
| 데이터 모델 + API | 관계형 + SQL | 다중 모델 + OSS API | 다중 모델 + SQL + OSS API(추가 서비스 예정) |
| SLA | 예 | 아니요 | 예, 대기 시간, 처리량, 일관성, 가용성에 대한 포괄적 SLA |


## <a name="next-steps"></a>다음 단계
다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB의 DocumentDB API 시작](create-documentdb-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](create-mongodb-nodejs.md)
* [Azure Cosmos DB의 Graph API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB의 Table API 시작](create-table-dotnet.md)

