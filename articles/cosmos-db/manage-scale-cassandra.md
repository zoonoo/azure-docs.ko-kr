---
title: Azure 코스모스 DB의 카산드라 API로 탄력적으로 확장
description: Azure 코스모스 DB 카산드라 API 계정을 확장하는 데 사용할 수 있는 옵션과 그 장점/단점에 대해 알아보십시오.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474682"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure 코스모스 DB 카산드라 API 계정 탄력적으로 확장

카산드라에 대한 Azure 코스모스 DB API의 탄력적 특성을 탐색하는 다양한 옵션이 있습니다. Azure Cosmos DB에서 효과적으로 확장하는 방법을 이해하려면 시스템의 성능 요구를 설명하기 위해 적절한 양의 RU/s를 프로비전하는 방법을 이해하는 것이 중요합니다. 요청 단위에 대한 자세한 내용은 [요청 단위](request-units.md) 문서를 참조하세요. 

Cassandra API의 경우 [.NET 및 Java SDK를](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api)사용하여 개별 쿼리에 대한 요청 단위 요금을 검색할 수 있습니다. 이는 서비스에 프로비전해야 하는 RU/s의 양을 결정하는 데 유용합니다.

![데이터베이스 작업은 요청 단위를 사용함](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>처리 속도 제한(오류 429개)

Azure Cosmos DB는 클라이언트가 프로비전한 금액보다 더 많은 리소스(RU/s)를 사용하는 경우 속도 제한(429) 오류를 반환합니다. Azure Cosmos DB의 Cassandra API는 Cassandra 네이티브 프로토콜에서 이러한 예외를 오버로드된 오류로 변환합니다. 

시스템이 대기 시간에 민감하지 않은 경우 재시도를 사용하여 처리량 속도 제한을 처리하는 것으로 충분할 수 있습니다. [Java에서 Cassandra](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) [재시도 정책에](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) 대한 [Azure Cosmos DB 확장을](https://github.com/Azure/azure-cosmos-cassandra-extensions) 사용하여 속도 제한을 투명하게 처리하는 방법에 대한 Java 코드 샘플을 참조하십시오. [Spark 확장을](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) 사용하여 속도 제한을 처리할 수도 있습니다.

## <a name="manage-scaling"></a>확장 관리

대기 시간을 최소화해야 하는 경우 Cassandra API에서 확장 및 프로비저닝 처리량(R)을 관리하는 다양한 옵션이 있습니다.

* [Azure 포털을 사용하여 수동으로](#use-azure-portal)
* [제어 평면 피쳐를 사용하여 프로그래밍 방식으로](#use-control-plane)
* [특정 SDK와 함께 CQL 명령을 사용하여 프로그래밍 방식으로](#use-cql-queries)
* [오토파일럿을 사용하여 동적으로](#use-autopilot)

다음 섹션에서는 각 접근 방식의 장점과 단점을 설명합니다. 그런 다음 시스템의 확장 요구, 전체 비용 및 솔루션의 효율성 요구 사항의 균형을 맞추기 위한 최상의 전략을 결정할 수 있습니다.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Azure 포털 사용

Azure 포털을 사용하여 Azure Cosmos DB Cassandra API 계정의 리소스를 확장할 수 있습니다. 자세한 내용은 컨테이너 및 [데이터베이스의 프로비저닝 처리량에 대한](set-throughput.md)문서를 참조하십시오. 이 문서에서는 Azure Portal의 [데이터베이스](set-throughput.md#set-throughput-on-a-database) 또는 [컨테이너](set-throughput.md#set-throughput-on-a-container) 수준에서 처리량을 설정하는 상대적인 이점을 설명합니다. 이 문서에서 언급한 "데이터베이스" 및 "컨테이너"라는 용어는 Cassandra API에 대해 각각 "키스페이스" 및 "테이블"에 매핑됩니다.

이 방법의 장점은 데이터베이스의 처리량 용량을 관리하는 간단한 턴키 방식이라는 것입니다. 그러나 대부분의 경우 확장 방식에는 비용 효율적이고 성능이 높은 특정 수준의 자동화가 필요할 수 있다는 단점이 있습니다. 다음 섹션에서는 관련 시나리오와 방법을 설명합니다.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>제어 평면 사용

Cassandra용 Azure Cosmos DB의 API는 다양한 제어 평면 기능을 사용하여 프로그래밍 방식으로 처리량을 조정할 수 있는 기능을 제공합니다. 지침 및 샘플은 [Azure 리소스 관리자,](manage-cassandra-with-resource-manager.md) [Powershell](powershell-samples-cassandra.md)및 [Azure CLI](cli-samples-cassandra.md) 문서를 참조하십시오.

이 방법의 장점은 최대 활동 또는 낮은 활동 기간을 고려하기 위해 타이머를 기반으로 리소스의 확장 또는 축소를 자동화할 수 있다는 것입니다. Azure Functions 및 Powershell을 사용하여 이 작업을 수행하는 방법에 대한 [샘플은 여기를](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) 참조하세요.

이 방법의 단점은 예측할 수 없는 변화하는 규모 요구에 실시간으로 대응할 수 없다는 것입니다. 대신 시스템, 클라이언트/SDK 수준에서 또는 [자동 조종 장치를](provision-throughput-autopilot.md)사용하여 응용 프로그램 컨텍스트를 활용해야 할 수 있습니다.

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>특정 SDK에서 CQL 쿼리 사용

지정된 데이터베이스 또는 컨테이너에 대한 [CQL ALTER 명령을](cassandra-support.md#keyspace-and-table-options) 실행하여 코드를 사용하여 시스템을 동적으로 확장할 수 있습니다.

이 방법의 장점은 응용 프로그램에 맞는 사용자 지정 방식으로 확장 요구에 동적으로 대응할 수 있다는 것입니다. 이 방법을 사용하면 표준 RU/s 요금 및 요금을 계속 활용할 수 있습니다. 시스템의 확장 요구 사항이 대부분 예측 가능한 경우(약 70% 이상), CQL과 함께 SDK를 사용하는 것이 자동 조종 장치를 사용하는 것보다 더 비용 효율적인 자동 크기 조정 방법일 수 있습니다. 이 방법의 단점은 속도 제한이 대기 시간을 증가시킬 수 있지만 재시도를 구현하는 것은 매우 복잡할 수 있다는 것입니다.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>자동 조종 장치 사용

수동 또는 프로그래밍 방식의 처리량 프로비저닝 방법 외에도 자동 조종 모드에서 Azure cosmos 컨테이너를 구성할 수도 있습니다. 오토파일럿 모드는 SLA를 손상시키지 않으면서 지정된 RU 범위 내에서 소비 요구에 맞게 자동으로 즉시 확장됩니다. 자세한 내용은 자동 조종 모드 문서의 [Azure Cosmos 컨테이너 및 데이터베이스 만들기를](provision-throughput-autopilot.md) 참조하십시오.

이 방법의 장점은 시스템에서 크기 조정 요구를 관리하는 가장 쉬운 방법입니다. 구성된 RU 범위 내에서 속도 제한을 적용하지 않도록 **보장합니다.** 단점은 시스템의 확장 요구 사항을 예측할 수 있는 경우 위에서 언급한 맞춤형 제어 평면 또는 SDK 수준 접근 방식을 사용하는 것보다 조정 요구를 처리하는 비용 효율적인 방법이 될 수 있다는 것입니다.

## <a name="next-steps"></a>다음 단계

- Java 응용 프로그램을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-cassandra-api-account-java.md) 시작
