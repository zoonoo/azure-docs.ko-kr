---
title: "Azure CosmosDB: 분당 요청 단위(RU/m) | Microsoft Docs"
description: "분당 요청 단위를 활용하여 비용을 절감하는 방법을 알아봅니다."
services: cosmosdb
documentationcenter: 
author: arnomicrosoft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: acomet
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 878b7335fb5e09bc8704f7211cc6293ad6ea4bea
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Azure Cosmos DB의 분당 요청 단위

Azure Cosmos DB는 신속하고 예측 가능한 성능을 얻을 수 있고, 응용 프로그램 증가에 따라 효율적인 확장이 가능하도록 설계되었습니다. 초당 및 분당(RU/m) 단위 모두에서 Cosmos DB 컨테이너의 처리량을 프로비전할 수 있습니다. 분당 단위로 프로비전된 처리량은 초당 단위로 발생하는 워크로드가 예기치 않게 급증하는 경우를 관리하는 데 사용됩니다. 

이 문서에서는 분당(RU/m) 요청 단위의 프로비전이 작동하는 방법에 대한 개요를 제공합니다. 예측할 수 없는 요구(특히 실행 데이터를 기반으로 분석을 실행해야 하는 경우) 및 급증하는 워크로드에 예측 가능한 성능을 제공하기 위해 RU/m을 프로비전합니다. 고객이 편안하고 신속하게 확장할 수 있도록 프로비전에 더 많은 처리량을 사용할 수 있게 합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* 분당 요청 단위는 어떻게 작동하나요?
* 분당 요청 단위 및 초당 요청 단위 간의 차이는 무엇인가요?
* RU/m을 프로비전하는 방법은 무엇인가요?
* 어떤 시나리오에서 분당 요청 단위를 프로비전하는 것이 좋은가요?
* 비용과 성능을 최적화하도록 포털 메트릭을 사용하는 방법은 무엇인가요?
* 어떤 종류의 요청에서 RU/m 예산을 소비할 수 있는지 정의하나요?

## <a name="provisioning-request-units-per-minute-rum"></a>분당 요청 단위(RU/m) 프로비전

두 번째 단위(RU/s)에서 Azure Cosmos DB를 프로비전할 때 처리량이 지정된 시간(초) 이내에 프로비전된 용량을 초과하지 않은 경우 요청이 짧은 대기 시간 동안 성공한다고 보증할 수 있습니다. RU/m에서 단위는 해당 시간(분) 이내에 요청이 성공한다고 보증하는 시간(분)입니다. 버스팅 시스템에 비해 얻게 되는 성능은 예측할 수 있으며 이를 기반으로 사용자가 계획할 수 있습니다.

분당 프로비전이 작동하는 방법은 간단합니다.

* RU/s 외에도 RU/m의 경우 매시간 요금이 청구됩니다. 자세한 내용은 Azure Cosmos DB [가격 책정 페이지](https://aka.ms/acdbpricing)를 방문하세요.
* RU/m은 컬렉션 수준에서 사용할 수 있습니다. SDK(Node.js, Java 또는 .Net)를 통하거나 포털(MongoDB API 워크로드도 포함)을 통해 수행할 수 있습니다.
* RU/m을 사용하는 경우 100RU/s가 프로비전될 때마다 1,000RU/m이 프로비전됩니다(10배 비율).
* 요청 단위는 지정된 시간(초) 이내에 분당 프로비전을 초과한 경우에만 지정된 시간(초)에서 RU/m 프로비전을 사용합니다.
* 60초(UTC)가 종료되면 분당 프로비전이 다시 채워집니다.
* RU/m은 파티션당 최대 5,000RU/s로 프로비전된 컬렉션에 대해서만 사용할 수 있습니다. 처리량 요구 사항을 확장하고 높은 수준의 파티션당 프로비전을 사용한 경우 경고 메시지가 표시됩니다.

아래에서는 구체적인 예로 고객이 100kRU/m인 10kRU/s를 프로비전합니다. 그러면 100,000RU/m인 10,000RU/s을 프로비전한 컬렉션에서 90초 동안 최대(50kRU/s) 프로비전에 대한 비용의 73%를 절약할 수 있습니다.

* 1초: RU/m 예산은 100,000으로 설정됩니다.
* 3초: 해당 시간(초) 동안 소비하는 요청 단위는 11,010RU입니다. RU/s를 초과하는 1,010RU가 프로비전됩니다. 따라서 1,010RU는 RU/m 예산에서 차감됩니다. 다음 57초 동안 RU/m 예산 중에 98,990RU를 사용할 수 있습니다.
* 29초: 해당 시간(초) 동안 급등이 발생(초당 프로비전보다 >4x 높음)했고 소비하는 요청 단위는 46,920RU였습니다. 92,323RU(28초)에서 55,403RU(29초)로 떨어진 RU/m 예산에서 36,920RU가 차감됩니다.
* 61초: RU/m 예산이 100,000RU로 다시 설정됩니다.
 
![Azure Cosmos DB의 사용 및 프로비전을 보여 주는 그래프](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>RU/m을 사용하여 요청 단위 용량 지정

Azure Cosmos DB 컬렉션을 만들 때 컬렉션에 대해 예약하려는 초당 요청 단위 수(RU/s)를 지정합니다. RU/m을 추가할지를 결정할 수 있습니다. 포털 또는 SDK를 통해 수행할 수 있습니다. 

### <a name="through-the-portal"></a>포털을 통해

분당 RU를 활성화 또는 비활성화하려면 컬렉션을 프로비전할 때 클릭해야 합니다. 

 ![Azure Portal에서 RU/m 설정 방법을 보여 주는 스크린샷](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>SDK를 통해
먼저 다음 SDK에만 RU/m을 사용할 수 있다는 것이 중요합니다.

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

.NET SDK를 사용하여 3,000RU/s 및 30,000RU/m인 컬렉션을 만들기 위한 코드 조각은 다음과 같습니다.

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

다음 코드 조각에서는 .NET SDK를 사용하여 RU/m을 프로비전하지 않고 컬렉션 처리량을 5,000RU/s로 변경합니다.

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>적합한 시나리오

이 섹션에서는 RU/m을 사용하기 위해 적합한 시나리오의 개요를 제공합니다.

**개발/테스트 환경:** 적합합니다. 개발 단계에서 다른 워크로드를 사용하여 응용 프로그램을 테스트하는 경우 RU/m은 이 단계에서 유연성을 제공할 수 있습니다. 반면 [에뮬레이터](../documentdb/documentdb-nosql-local-emulator.md)는 추가 비용 없이 Azure Cosmos DB를 테스트할 수 있는 유용한 도구입니다. 그러나 클라우드 환경에서 시작하려는 경우 임시 성능 요구 사항에 맞게 RU/m에 뛰어난 유연성을 제공합니다. 처음에는 성능 요구 사항에 대해 염려하지 않고 개발하는 데 많은 시간을 투자합니다. 최소 RU/s를 프로비전하여 시작하고 RU/m을 사용하도록 설정하는 것이 좋습니다.

**예측할 수 없고 급증하는 분 단위 요구 사항:** 적합 – 비용 절감: 25-75%. RU/m이 크게 개선되었고 대부분의 프로덕션 시나리오가 해당 그룹에 속합니다. IoT 워크로드가 몇 분 동안 여러 번 급증하는 경우, 시스템에서 동시에 대량 삽입을 수행할 때 쿼리가 실행되는 경우, 급증하는 요구 사항을 처리하기 위한 추가 용량이 필요합니다. 아래의 단계별 접근 방식을 적용하여 리소스 요구 사항을 최적화하는 것이 좋습니다.

 ![5분 단위로 요청 사용을 보여 주는 그래프](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *그림 - RU 사용 벤치마크*

**편안함:** 적합 – 비용 절감: 10-20%. 잠재적인 급증 및 제한에 대해 염려하지 않고 싶으신가요? 이 기능이 적합합니다. 이 경우에 RU/m을 사용하도록 설정하고 초당 프로비전을 약간 낮추는 것이 좋습니다. 프로비전을 적극적으로 최적화하려고 하지 않기 때문에 이 경우는 위에서 설명한 경우와 다릅니다. 오히려 사용 중인 "0 제한"에 가깝습니다.

임시 요구 사항을 포함한 주요 작업: 경우에 따라 임시 작업 또는 중요하지 않은 작업에서 예산을 사용하지 않도록 주요 작업만 RU/m 예산에 액세스할 수 있게 허용하는 것이 좋습니다. 이는 아래 섹션에서 쉽게 정의할 수 있습니다.

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>비용과 성능을 최적화하도록 포털 메트릭 사용

**몇 주 이내에 처리량 요구를 최적화하기 위해 RU/m 사용을 모니터링하는 콘텐츠가 더 발전될 예정입니다.**

포털 메트릭을 통해 RU/m에 대해 사용하는 일반적인 RU/s의 양을 확인할 수 있습니다. 이러한 메트릭을 모니터링하면 프로비전을 최적화하는 데 도움이 됩니다. 

사용자에게 이익이 되도록 RU/m을 사용하는 방법에 대한 단계별 접근 방식을 사용하는 것이 좋습니다. 각 단계에서 워크로드(시간, 일 심지어 주일 수 있음)의 전체 주기를 나타내는 RU 사용의 개요를 보고 프로비전한 부분을 활용하는 방법에 대한 통찰력을 얻어야 합니다.

이 방법을 사용하는 원칙은 아래 성능 조건과 일치하는 프로비전 지점에 최대한 가깝게 처리량을 프로비전하는 것입니다. 

![5분 단위로 요청 사용을 보여 주는 그래프](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
워크로드에 대한 최적의 프로비전 지점을 이해하려면 다음 항목을 이해해야 합니다.

* 소비 패턴: 아니요, 자주 사용하지 않거나 지속적으로 급증하나요? 작은(평균 2배), 중간 또는 크게(평균 10배 이상) 급증하나요?
* 제한된 요청 백분율: 약간의 제한이 있는 경우에도 편하게 생각하나요? 그렇다면, 얼마나 그런가요? 

목표를 식별하면 최적의 프로비전에 가까워질 수 있습니다.

사용자를 돕기 위해 RU/m 사용에 따라 프로비전을 최적화하는 방법에 대한 전반적인 지침을 제공하려고 합니다. 이 설명서는 모든 종류의 워크로드에 적용되지 않지만 비공개 미리 보기 정보를 기반으로 합니다. 자세히 알아보면 이러한 기준을 변경할 수 있습니다.

|RU/m % 사용률|RU/m의 사용률 수준|프로비전하기 위해 권장되는 작업|
|---|---|---|
|0-1%|과소 사용률|더 많은 RU/m을 사용하는 낮은 RU/s|
|1-10%|정상 사용|동일한 프로비전 수준 유지|
|10% 이상|과도 사용률|RU/s를 증가시켜 RU/m 사용 감소|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>RU/m 예산을 사용할 수 있는 작업을 선택합니다.

요청 수준에서 RU/m 예산을 활성화/비활성화하여 작업 형식에 관계 없이 요청을 처리할 수 있습니다. 일반적으로 프로비전된 RU/s 예산을 사용하고 요청이 RU/m 예산을 사용할 수 없는 경우 이 요청이 제한됩니다. RU/m 처리량 예산이 활성화된 경우 기본적으로 모든 요청은 RU/m 예산에서 처리됩니다. 

CRUD 및 쿼리 작업에 DocumentDB API를 사용하여 RU/m 예산을 비활성화하기 위한 코드 조각입니다.

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Cosmos DB에서 분할이 작동하는 방식, 분할된 컬렉션을 만드는 방법 및 응용 프로그램에 적합한 파티션 키를 선택하는 방법을 설명했습니다.

* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행합니다. 샘플에 대해서는 [Azure Cosmos DB를 사용한 성능 및 규모 테스트](../documentdb/documentdb-performance-testing.md)를 참조하세요.
* [SDK](../documentdb/documentdb-sdk-dotnet.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 코딩을 시작합니다.
* Azure Cosmos DB에서 [프로비전된 처리량](../documentdb/documentdb-request-units.md)에 대한 자세한 정보 


