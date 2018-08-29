---
title: Cosmos DB에 Azure Stream Analytics 출력
description: 이 문서에서는 비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 위해 Azure Stream Analytics를 사용하여 JSON 출력에 대한 Azure Cosmos DB에 출력을 저장하는 방법을 알아봅니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 95cfc7e6d9515274aa7a3c5fde382244f3b33fab
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143604"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB에 Azure Stream Analytics 출력  
비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 사용하기 위해 Stream Analytics에서 JSON 출력의 대상을 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)로 지정할 수 있습니다. 이 문서에서는 이 구성을 구현하기 위한 몇 가지 모범 사례를 설명합니다.

Cosmos DB에 대해 잘 모를 경우 먼저 [Azure Cosmos DB의 학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)를 살펴보세요. 

> [!Note]
> 현재 Azure Stream Analytics는 **SQL API**를 사용한 Azure Cosmos DB 연결만을 지원합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Azure Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>출력 대상으로서 Cosmos DB의 기본 사항
Stream Analytics의 Azure Cosmos DB 출력을 사용하면 스트림 처리 결과를 Cosmos DB 컬렉션에 JSON 출력으로 쓸 수 있습니다. Stream Analytics에서는 데이터베이스에 컬렉션을 만들지 않습니다. 대신 사용자가 사전에 만들어야 합니다. 이는 사용자가 Cosmos DB 컬렉션에 대한 청구 비용을 사용자가 제어하고, [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 컬렉션의 성능, 일관성 및 용량을 직접 조정할 수 있도록 하기 위한 것입니다. 

아래에서는 Cosmos DB 컬렉션 옵션 중 일부를 자세히 설명합니다.

## <a name="tune-consistency-availability-and-latency"></a>일관성, 가용성 및 대기 시간 조정
Azure Cosmos DB를 사용하면 응용 프로그램 요구 사항에 맞게 데이터베이스 및 컬렉션을 미세 조정하고, 일관성, 가용성 및 대기 시간 간의 균형을 유지할 수 있습니다. 시나리오에서 읽기 및 쓰기 대기 시간에 대해 필요로 하는 읽기 일관성 수준에 따라 데이터베이스 계정에 대한 일관성 수준을 선택할 수 있습니다. 또한 기본적으로 Azure Cosmos DB는 컬렉션에 대한 각 CRUD 작업에서 동기 인덱싱을 지원합니다. 이는 Azure Cosmos DB에서 쓰기/읽기 성능을 제어하는 또 다른 유용한 옵션입니다. 자세한 내용은 [데이터베이스 및 쿼리 일관성 수준 변경](../cosmos-db/consistency-levels.md) 문서를 검토하세요.

## <a name="upserts-from-stream-analytics"></a>Stream Analytics에서 Upsert
Stream Analytics를 Azure Cosmos DB와 통합하면 지정된 문서 ID 열에 따라 컬렉션에 레코드를 삽입하거나 업데이트할 수 있습니다. 이를 *Upsert*라고도 합니다.

Stream Analytics에서는 문서 ID 충돌로 인해 삽입에 실패한 경우에만 업데이트가 수행되는 낙관적 Upsert 방식을 사용합니다. 이 업데이트는 패치로 수행되므로 문서의 부분 업데이트가 가능합니다. 즉, 새 속성 추가 또는 기존 속성 바꾸기가 증분식으로 수행됩니다. 그렇지만 JSON 문서에서 배열 속성 값을 변경하면 전체 배열을 덮어씁니다. 즉, 배열이 병합되지 않습니다.

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB의 데이터 분할
Azure Cosmos DB는 워크로드에 따라 파티션 크기를 자동으로 조정하므로 Azure Cosmos DB [무제한](../cosmos-db/partition-data.md)은 데이터 분할을 위해 권장되는 방법입니다. 무제한 컨테이너에 쓸 때 Stream Analytics는 이전 쿼리 단계 또는 입력 분할 구성표 수만큼의 병렬 기록기를 사용합니다.
> [!Note]
> 이때 Azure Stream Analytics는 최상위 수준에서 파티션 키를 사용하여 무제한 컬렉션만 지원합니다. 예를 들어 `/region`이 지원됩니다. 중첩된 파티션 키(예: `/region/name`)는 지원되지 않습니다. 

고정된 Azure Cosmos DB 컬렉션의 경우, Stream Analytics에서는 컬렉션이 꽉 찰 때 강화하거나 스케일 아웃할 방법이 없습니다. 10GB 및 10,000RU/s의 처리량 상한 값이 적용됩니다.  고정 컨테이너에서 무제한 컨테이너(최소 1,000RU/s 처리량 및 파티션 키가 있는 하나의 컨테이너)로 데이터를 마이그레이션하려면 [데이터 마이그레이션 도구](../cosmos-db/import-data.md) 또는 [변경 피드 라이브러리](../cosmos-db/change-feed.md)를 사용해야 합니다.

여러 고정 컨테이너에 쓰는 기능은 더 이상 지원되지 않으며, Stream Analytics 작업을 스케일 아웃하기 위한 권장되는 방법이 아닙니다. [Cosmos DB에서 분할 및 크기 조정](../cosmos-db/sql-api-partition-data.md) 문서에서 이에 대한 세부 정보를 제공합니다.

## <a name="cosmos-db-settings-for-json-output"></a>JSON 출력에 대한 Cosmos DB 설정
Cosmos DB를 Stream Analytics의 출력으로 만들면 아래와 같은 정보를 묻는 메시지가 생성됩니다. 이 섹션에서는 속성 정의에 대해 설명합니다.


![documentdb Stream Analytics 출력 화면](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

필드           | 설명 
-------------   | -------------
출력 별칭    | ASA 쿼리에서 이 출력을 참조할 별칭입니다.   
계정 이름    | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다. 
계정 키     | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다.
데이터베이스        | Azure Cosmos DB 데이터베이스 이름입니다.
컬렉션 이름 | 사용할 컬렉션에 대한 컬렉션 이름입니다. `MyCollection`은 유효한 샘플 입력입니다. `MyCollection`이라는 컬렉션이 하나 있어야 합니다.  
문서 ID     | 선택 사항입니다. 삽입 또는 업데이트 작업의 기준으로 사용해야 하는 고유 키로 사용되는 출력 이벤트의 열 이름입니다. 이 필드를 비워두면 업데이트 옵션 없이 모든 이벤트가 삽입됩니다.
