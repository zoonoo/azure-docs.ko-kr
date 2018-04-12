---
title: Azure Cosmos DB에 대한 처리량 프로비전 | Microsoft Docs
description: Azure Cosmos DB 컨테이너, 컬렉션, 그래프 및 테이블에 대해 프로비전된 처리량을 설정하는 방법을 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0e89b93764f51873d991524a5e226464c224b649
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Azure Cosmos DB 컨테이너에 대한 처리량 설정

Azure Portal 또는 클라이언트 SDK를 사용하여 Azure Cosmos DB 컨테이너에 대한 처리량을 설정할 수 있습니다. 

다음 테이블에는 컨테이너에 사용할 수 있는 처리량이 나열되어 있습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>단일 파티션 컨테이너</strong></p></td>
            <td valign="top"><p><strong>분할된 컨테이너</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>최소 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 400개</p></td>
            <td valign="top"><p>초당 요청 단위 1,000개</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최대 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 10,000개</p></td>
            <td valign="top"><p>Unlimited</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Azure Portal을 사용하여 처리량을 설정하려면

1. 새 창에서 [Azure Portal](https://portal.azure.com)을 엽니다.
2. 왼쪽 모음에서 **Azure Cosmos DB**를 클릭하거나 맨 아래에서 **모든 서비스**를 클릭한 다음, **데이터베이스**로 스크롤하고 **Azure Cosmos DB**를 클릭합니다.
3. Cosmos DB 계정을 선택합니다.
4. 새 창의 탐색 메뉴에서 **데이터 탐색기**를 클릭합니다.
5. 새 창에서 데이터베이스와 컨테이너를 확장하고 **배율 및 설정**을 클릭합니다.
6. 새 창에서 **처리량** 상자에 새 처리량 값을 입력하고 **저장**을 클릭합니다.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>SQL API for .NET을 사용하여 처리량을 설정하려면

다음 코드 조각은 현재 처리량을 검색하고 500RU/s로 변경합니다. 전체 코드 샘플은 GitHub의 [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) 프로젝트를 참조하세요.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>SQL API for Java를 사용하여 처리량을 설정하려면

다음 코드 조각은 현재 처리량을 검색하고 500RU/s로 변경합니다. 전체 코드 샘플은 GitHub의 [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 파일을 참조하세요. 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="throughput-faq"></a>처리량 FAQ

**내 처리량을 400RU/s 미만으로 설정할 수 있나요?**

Cosmos DB 단일 파티션 컨테이너에서 사용할 수 있는 최소 처리량은 400RU/s이고 분할된 컨테이너에 대한 최소값은 1000RU/s입니다. 요청 단위는 100RU/s 간격으로 설정되어 있지만 처리량은 100RU/s 또는 400RU/s 미만인 값으로 설정할 수 없습니다. Cosmos DB를 개발하고 테스트하는 비용 효과적인 방법을 찾으려는 경우 비용 없이 로컬에 배포할 수 있는 [Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용할 수 있습니다. 

**MongoDB API를 사용하여 처리량을 설정하려면 어떻게 해야 하나요?**

처리량을 설정할 수 있는 MongoDB API 확장은 없습니다. [SQL API for .NET을 사용하여 처리량을 설정하려면](#set-throughput-sdk)에 나와 있는 대로 SQL API를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Cosmos DB를 사용하여 프로비전을 수행하고 대규모로 크기를 조정하려면 [Cosmos DB로 분할 및 크기 조정](partition-data.md)을 참조하세요.
