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
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 0736a6f9790b70686c4d936f2af80d5e42b89615
ms.contentlocale: ko-kr
ms.lasthandoff: 05/31/2017

---

# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB 시작

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 멀티모델 데이터베이스입니다. Azure Cosmos DB에서는 단추를 클릭하는 간단한 방식으로 Azure의 지리적 위치 수에 관계없이 처리량 및 저장소를 탄력적 및 독립적으로 확장할 수 있습니다. 다른 데이터베이스 서비스에서는 제공하지 않는 포괄적 [Service Level Agreement(서비스 수준 약정)](https://aka.ms/acdbsla)(SLA)를 통해 처리량, 대기 시간, 가용성 및 일관성을 보장합니다.

![Azure Cosmos DB는 탄력적 확장, 낮은 대기 시간 보증, 일관성 모델 5개, 포괄적 보장 SLA를 갖춘 전세계에 배포된 데이터베이스 서비스입니다.](./media/introduction/azure-cosmos-db.png)

Azure Cosmos DB에는 기본적으로 키-값, 문서, 그래프, 다각형 등, 다양한 데이터 모델을 지원하며 쓰기에 최적화되고, 리소스가 관리하며, 모든 스키마에 적용되는 엔진이 포함됩니다. 또한 [MongoDB](mongodb-introduction.md), [DocumentDB SQL](documentdb-introduction.md), [Gremlin](graph-introduction.md)(미리 보기) 및 [Azure Tables](table-introduction.md)(미리 보기) 등을 망라하는 다양한 데이터 액세스용 API를 광범위한 방식으로 지원합니다. 

Azure Cosmos DB는 2010년 말 Microsoft 내부에서 대규모 응용 프로그램에 직면한 개발자의 과제에 부응하기 위해 시작되었습니다. 세계적으로 분산된 응용 프로그램의 구축은 Microsoft만의 문제가 아니기 때문에 이 서비스를 외부에 Azure DocumentDB의 형태로 모든 Azure 개발자들에게 제공하게 되었습니다. 이제 제공되는 Azure Cosmos DB는 DocumentDB의 진화 과정에서 한 단계 도약한 것입니다. Azure Cosmos DB의 이 릴리스에서는 DocumentDB 고객(데이터 포함)이 자동으로 Azure Cosmos DB 고객이 됩니다. 이러한 전환은 원활히 진행되며 이 고객은 Azure Cosmos DB가 제공하는 더 광범위한 새로운 기능에 액세스할 수 있습니다. 

## <a name="capability-comparison"></a>기능 비교

Azure Cosmos DB는 관계형 및 비관계형 데이터베이스의 최고 기능을 제공합니다.

| 기능 | 관계형 DB    | 비관계형(NoSQL) DB |     Azure Cosmos DB |
| --- | --- | --- | --- |
| 글로벌 분포 | x | x | ✓ 턴키, 30개 이상의 지역, 멀티 호밍 |
| 수평적 확장 | x | ✓ | ✓독립적 저장소 및 처리량 확장 | 
| 대기 시간 보장 | x | ✓ | ✓ p99에서 읽기 < 10ms, 쓰기 < 15ms | 
| 고가용성 | x | ✓ | ✓ 무중단, PACELC 절충, 자동 및 수동 장애 조치 |
| 데이터 모델 + API | 관계형 + SQL | 다중 모델 + OSS API | 다중 모델 + SQL + OSS API(추가 서비스 예정) |
| SLA | ✓ | x | ✓ 대기 시간, 처리량, 일관성, 가용성에 대한 포괄적 SLA |

## <a name="key-capabilities"></a>주요 기능
세계적으로 분산된 데이터베이스 서비스인 Azure Cosmos DB는 확장성 있고 세계적으로 배포되며 응답성 높은 응용 프로그램을 구축할 수 있도록 다음과 같은 기능을 제공합니다.

* [**턴키 글로벌 배포**](#global-distribution)
    * 응용 프로그램을 어디서나 사용자에게 즉시 제공할 수 있습니다. 데이터도 마찬가지입니다.
    * 하드웨어, 노드 추가, VM, 코어 등에 대해서 염려할 필요가 없습니다. 찍어 클릭하기만 하면 데이터가 제공됩니다. 

* [**데이터 액세스 및 쿼리를 위한 다중 데이터 모델 및 주요 API**](#data-models)
    * 키-값, 문서, 그래프, 다각형 등을 망라하는 여러 데이터 모델을 지원합니다.
    * Node.js, Java, .NET, .NET Core, Python, MongoDB 등에 대한 확장 가능한 API
    * 쿼리용 SQL 및 Gremlin 

* [**전세계에서 필요 시 탄력적으로 처리량 및 저장소 확장**](#horizontal-scale)
    * [초](request-units.md) 및 [minute](https://aka.ms/acdbrupm) 분 단위로 간편하게 확장하고 원하면 언제든 변경할 수 있습니다. 
    * 크기 요구 사항에 맞게 지속적으로 저장소를 [투명하게 자동으로](partition-data.md) 확장합니다.

* [**응답성 높은 중요 업무용 응용 프로그램 구축**](#low-latency) 
    * 전 세계 어디서나 백분위 99에서 한 자리 밀리초 대기 시간의 속도로 데이터에 액세스합니다. 

* [**"무중단" 가용성 보장**](#high-availability)
    * 단일 지역 내에서 99.99%의 가용성을 제공합니다.
    * 원하는 만큼의 [Azure 지역](https://azure.microsoft.com/regions)에 배포하여 더 높은 가용성을 보장합니다.
    * 데이터 무손실 보장을 통해 하나 이상의 지역에서 [오류를 시뮬레이션](regional-failover.md)합니다. 

* [**올바른 방법으로 전세계에 배포되는 응용 프로그램 작성**](#consistency)
    * [5개의 일관성 모델](consistency-levels.md)은 강력한 SQL 형태의 일관성과 비 SQL 형태의 결과적 일관성 및 그 중간 형태의 모든 것을 제공합니다.  
  
* [**환불 보장**](#sla) 
    * 데이터를 빠르게 가져오지 않는다면 환불할 수 있습니다. 
    * 가용성, 대기 시간, 처리량 및 일관성에 대한 [서비스 수준 계약](https://aka.ms/acdbsla)  

* [**데이터베이스 스키마/인덱스 관리 없음**](#schema-free)
    * 데이터베이스 스키마와 인덱스를 응용 프로그램의 스키마와 동기 상태로 유지하는 것에 대해 염려하지 않아도 됩니다. 이제 스키마 제약이 없는 것입니다. 

* [**낮은 총 소유 비용**](#tco)
    * 비 관리 솔루션에 비해 5~10배 [비용 효과적](https://aka.ms/documentdb-tco-paper) 
    * DynamoDB보다 세 배 저렴합니다.

<a id="global-distribution"></a>

## <a name="global-distribution"></a>글로벌 분포
Azure DB Cosmos 컨테이너는 두 차원으로 배포됩니다. 

1. 특정 지역 안에서는 모든 리소스가 리소스 파티션을 통해 수평적으로 파티셔닝됩니다(로컬 배포). 
2. 또한 각 리소스 파티션은 지리적 영역에 복제됩니다(글로벌 배포). 

![Azure Cosmos DB의 글로벌 배포를 나타내는 다이어그램](./media/introduction/azure-cosmos-db-global-distribution.png) 

저장소와 처리량을 확장해야 할 경우 Cosmos DB가 모든 지역에서 투명하게 파티션 관리 작업을 수행합니다. 확장, 배포 또는 실패와 관계없이 Cosmos DB는 지속적으로 글로벌 배포 리소스의 단일 이미지를 제공합니다. 

Cosmos DB의 글로벌 리소스 배포는 [턴키](distribute-data-globally.md)방식입니다. 언제든 몇 번의 단추 클릭(또는 단일 API 호출을 통한 프로그래밍 방식)을 통해 수에 관계없이 지리적 영역을 데이터베이스 계정과 연결할 수 있습니다. 

데이터의 규모나 지역 수에 무관하게 Cosmos DB는 새롭게 연결된 각각의 지역에서 백분위 99에서 한 시간 안에 클라이언트 요청 처리 시작을 보장합니다. 모든 원본 리소스 파티션의 데이터를 새로 연결된 지역에 병렬로 시드 및 복사하여 이루어집니다. 고객은 기존 영역을 제거하거나, 이전에 데이터베이스 계정과 연결되었던 지역을 오프라인으로 지정할 수도 있습니다.

<a id="data-models"></a>
## <a name="multi-model-multi-api-support"></a>다중 모델 다중 API 지원
 Azure Cosmos DB는 기본적으로 문서, 키-값, 그래프, 열-패밀리 등을 망라하는 다중 데이터 모델을 지원합니다. Cosmos DB 데이터베이스 엔진의 핵심 콘텐츠-모델은 ARS(아톰-레코드-시퀀스)를 기초로 합니다. 아톰은 문자열, 부울, 숫자 같은 기본 유형의 소규모 집합으로 이루어져 있습니다. 레코드는 이러한 유형으로 구성된 구조체입니다. 시퀀스는 아톰, 레코드 또는 시퀀스도 이루어진 배열입니다. 
 
 데이터베이스 엔진은 서로 다른 데이터 모델을 ARS 기반 데이터 모델로 효과적으로 전환 및 투영할 수 있습니다. Cosmos DB의 핵심 데이터 모델은 기본적으로 동적으로 입력된 프로그래밍 언어에서 액세스할 수 있고 있는 그대로 JSON으로 노출될 수 있습니다. 
 
 또한 이 서비스는 데이터 액세스 및 쿼리를 위해 주요 데이터베이스 API를 지원합니다. Cosmos DB의 데이터베이스 엔진은 현재 [DocumentDB SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), [Azure Tables](table-introduction.md)(미리 보기) 및 [Gremlin](graph-introduction.md)(미리 보기)을 지원합니다. 주요 OSS API를 사용하여 지속적으로 응용 프로그램을 구축할 수 있고, 실전에서 테스트되었고 완전 관리되며 전세계에 배포된 데이터베이스 서비스의 모든 장점을 활용할 수 있습니다. 

<a id="horizontal-scale"></a>
## <a name="horizontal-scaling-of-storage-and-throughput"></a>저장소 및 처리량의 수평적 확장
Cosmos DB 컨테이너의 모든 데이터(예: 문서 컬렉션, 테이블, 그래프 등)는 수평적으로 파티셔닝되며 리소스 파티션을 통해 투명하게 관리됩니다. 리소스 파티션은 [고객 특정 파티션-키](partition-data.md)를 통해 분할된 데이터의 일관되고 가용성 높은 컨테이너입니다. 자신이 관리하는 리소스 집합에 대한 단일 시스템 이미지를 제공하며 확장성 및 배포의 기본 단위가 됩니다. Cosmos DB에서는 지리적 위치 및 시간 모두에 따라 달라지는 유동적 워크로드를 지원하기 위해 다양한 지리적 영역에서 응용 프로그램 트래픽 패턴을 기초로 탄력적으로 처리량을 확장할 수 있습니다. 이 서비스는 Cosmos DB 컨테이너의 가용성, 일관성, 대기 시간 또는 처리량 손상 없이 투명하게 파티션을 관리합니다.  
 
![수평적 확장이 가능한 Azure Cosmos DB ](./media/introduction/azure-cosmos-db-partitioning.png) 

[초당 요청 단위(RU/s)](request-units.md)를 사용하여 프로그램 방식으로 처리량을 프로비전함으로써 Azure Cosmos DB의 처리량을 탄력적으로 확장할 수 있습니다. 내부적으로 이 서비스가 리소스 파티션을 투명하게 관리하여 특정 컨테이너에서 처리량을 제공합니다. Cosmos DB는 컨테이너와 연결된 모든 지역에서 처리량을 사용할 수 있도록 합니다. 새 처리량은 구성된 처리량 값의 변경으로부터 5초 안에 적용됩니다. 

초당 및 [분당(RU/m)](request-units-per-minute.md) 단위 모두에서 Cosmos DB 컨테이너의 처리량을 프로비전할 수 있습니다. 분 단위에서 프로비전된 처리량은 초 단위에서 발생하는 워크로드의 예기치 않은 급증을 관리하는 데 사용됩니다. 

<a id="low-latency"></a>
## <a name="low-latency-guarantees-at-the-99th-percentile"></a>백분위 99의 낮은 대기 시간 보증
SLA의 일부로 Cosmos DB는 고객에게 백분위 99의 종단 간 대기 시간을 보장합니다. 일반 1KB 항목의 경우 Cosmos DB는 동일한 Azure 지역에서 백분위 99로 읽기 10ms 미만, 인덱스된 쓰기 15ms 미만의 종단 간 대기 시간을 보장합니다. 중간 대기 시간이 크게 낮아집니다(5ms 미만).  Cosmos DB는 각 데이터베이스 트랜잭션에서 요청 처리의 상한을 통해 대기 시간이 높은 트랜잭션과 사용 불가능한 데이터베이스를 명확히 구분합니다.

<a id="high-availability"></a>
## <a name="transparent-multi-homing-and-9999-high-availability"></a>투명한 멀티 호밍 및 99.99% 고가용성
동적으로 Azure Cosmos DB 데이터베이스 계정이 연결된 영역에 "우선 순위"를 연결할 수 있습니다. 우선 순위는 국지적 장애가 발생할 경우 특정 지역으로 요청을 전달하는 데 사용됩니다. 지역 재해 발생 시와는 달리 Cosmos DB는 우선 순위 순서대로 장애 조치를 수행합니다.

응용 프로그램의 종단 간 가용성을 테스트하기 위해 [수동으로 장애 조치를 트리거](regional-failover.md)할 수 있습니다(비율은 한 시간에 두 작업으로 제한됨). Cosmos DB는 수동 지역별 장애 조치에서 데이터 무손실을 보장합니다. 지역 재해가 발생할 경우 Cosmos DB는 시스템이 시작한 자동 장애 조치 중에 데이터 손실의 상한을 보장합니다. 지역 장애 조치 후 응용 프로그램의 재배포가 필요하지 않으며 가용성 SLA는 Azure Cosmos DB에서 유지 관리됩니다. 

이 상황에서 Cosmos DB는 논리적(지역에 관계 없음) 또는 물리적(지역에 한정) 끝점을 사용하여 리소스와 상호 작용할 수 있습니다. 전자는 장애 조치 시 응용 프로그램을 투명하게 멀티 호밍할 수 있습니다. 후자는 읽기와 쓰기를 특정 지역에 리디렉션할 수 있는 세분화된 응용 프로그램 제어 기능을 제공합니다. Cosmos D는 모든 데이터베이스 계정에 대해 99.99%의 가용성 SLA를 보장합니다. 가용성 보장은 규모(프로비전된 처리량 및 저장소), 지역 수 또는 특정 데이터베이스에 연결된 지역 간 지리적 거리에 제한 받지 않습니다. 

<a id="consistency"></a>
## <a name="multiple-well-defined-consistency-models"></a>잘 정의된 다중 일관성 모델
상업적으로 분산된 데이터베이스는 입증 가능한 잘 정의된 일관성 선택 사항을 전혀 제공하지 않는 데이터베이스와, 두 가지 극단적인 프로그래밍 가능성 선택을 제공하는 데이터베이스(강력한 일관성, 결과적 일관성) 등의 두 가지 범주에 해당합니다. 전자의 경우 응용 프로그램 개발자들이 복제 프로토콜을 세부적으로 구현해야 하는 부담이 있고 개발자들이 일관성, 가용성, 대기 시간 및 처리량 간의 상호 균형 유지가 어렵습니다. 후자의 경우에는 두 가지 극단의 상황 중 하나를 선택해야 합니다. 50여 개가 넘는 일관성 모델의 연구와 제안이 있지만 분산형 데이터베이스 커뮤니티에서는 강력하고 결과적인 일관성 이상으로 일관성 수준을 상용화하지 못하고 있습니다. 

Cosmos DB에서는 [5개의 잘 정의된 일관성 모델](consistency-levels.md)을 일관성 스펙트럼(강력, 제한된 부실, [세션](http://dl.acm.org/citation.cfm?id=383631), 일관적인 접두사, 결과적)과 함께 선택할 수 있습니다. 

![Azure Cosmos DB는 선택 가능한 여러 개의 잘 정의된(관대한) 일관성 모델을 제공합니다.](media/introduction/azure-cosmos-db-consistency-levels.png)

다음 표에서는 각 일관성 수준이 제공하는 특정 보증을 보여 줍니다.
 
**일관성 수준 및 보증**

| 일관성 수준    | 보증 |
| --- | --- |
| 강력 | 선형화 가능성 |
| 제한된 부실    | 일관적인 접두사 k 접두사 또는 t 간격을 통해 쓰기 뒤 읽기 지연 |
| 세션    | 일관적인 접두사 단조 읽기, 단조 쓰기, 쓰기 읽기, 읽기 뒤 쓰기 |
| 일관적인 접두사    | 간격 없이 모든 업데이트의 일부 접두어의 업데이트 반환 |
| 최종    | 순서 외 읽기 |

Cosmos DB 계정에 대해 기본 일관성 수준을 구성할 수 있습니다(나중에 특정 읽기 요청에 대한 일관성 덮어씀). 내부적으로 기본 일관성 수준은 여러 지역에 걸칠 수 있는 파티션 집합 안의 데이터에 적용됩니다. 


<a id="sla"></a>
## <a name="guaranteed-service-level-agreements"></a>Azure Service Level Agreements(서비스 수준 약정) 보장

Cosmos DB는 가용성, 처리량, 낮은 대기 시간 및 일관성에 대해 99.99의 [SLA 보증](https://aka.ms/acdbsla)을 제공하는 최초의 관리형 데이터베이스 서비스입니다.
* 가용성: 각 데이터 및 제어 평면 작업에 대해 99.99% 가동 시간 가용성 SLA를 제공합니다.
* 처리량: 99.99%의 요청이 성공적으로 완료됩니다. 
* 백분위 99에서 대기 시간: 99.99%가 대기 시간 10ms 미만
* 일관성: 100% 읽기 요청이 사용자가 요청한 일관성 수준에 대해 일관성 보증에 부합합니다.


<a id="schema-free"></a>
## <a name="schema-free"></a>스키마 없음

관계형 및 NoSQL 데이터베이스 모두 스키마 및 인덱스 관리, 버전 및 마이그레이션 작업이 필요합니다. 이 작업은 모두 글로벌 배포 설정에서 매우 까다롭습니다. 이제 Cosmos DB가 이 문제를 해결했습니다. Cosmos DB에서는 스키마 및 인덱스 관리, 스키마 버전 관리, 스키마 마이그레이션 중의 응용 프로그램 중단 시간이 필요하지 않습니다. Cosmos DB의 데이터베이스 엔진은 스키마에서 완전히 자유로우며 스키마나 인덱스 없이 자신이 수집한 모든 데이터를 자동으로 인덱싱하며 매우 빠르게 쿼리를 서비스합니다. 

<a id="tco"></a>
## <a name="low-cost-of-ownership"></a>낮은 총 소유 비용 

 TCO(모든 총 소유 비용) 고려 사항을 생각한다면 Azure Cosmos DB처럼 관리되는 클라우드 서비스가 온-프레미스나 가상 컴퓨터에서 실행되는 OSS 제품보다 5~10배 더 비용 효과적입니다. 또한 Azure Cosmos DB는 대용량 워크로드에서 DynamoDB에 비해 2~3배 저렴합니다. [TCO 백서](https://aka.ms/documentdb-tco-paper)에서 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계
다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB의 DocumentDB API 시작](create-documentdb-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](create-mongodb-nodejs.md)
* [Azure Cosmos DB의 Graph API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB의 Table API 시작](create-table-dotnet.md)

