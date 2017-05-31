---
title: "Azure Cosmos DB에 대한 처리량 프로비전 | Microsoft Docs"
description: "Azure Cosmos DB 컬렉션에 대해 프로비전된 처리량을 설정하는 방법을 알아봅니다."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5258ba0bc37442c983d91c5dd7435fd5fbefd56f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="set-throughput-for-azure-cosmos-db-collections"></a>Azure Cosmos DB 컬렉션에 대한 처리량 설정

Azure Portal 또는 클라이언트 SDK를 사용하여 Azure Cosmos DB 컬렉션에 대한 처리량을 설정할 수 있습니다. 

다음 테이블에는 컬렉션에 사용할 수 있는 처리량이 나열되어 있습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>단일 파티션 컬렉션</strong></p></td>
            <td valign="top"><p><strong>분할된 컬렉션</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>최소 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 400개</p></td>
            <td valign="top"><p>초당 요청 단위 2,500개</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최대 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 10,000개</p></td>
            <td valign="top"><p>무제한</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> 분할된 컬렉션을 2,500RU/s ~ 10,000RU/s의 처리량 값에 설정하려면 Azure Portal을 임시로 사용해야 합니다. SDK에서는 이 기능을 사용할 수 없습니다.

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Azure Portal을 사용하여 처리량을 설정하려면

1. 새 창에서 [Azure Portal](https://portal.azure.com)을 엽니다.
2. 왼쪽 모음에서 **Azure Cosmos DB**를 클릭하거나 맨 아래에서 **더 많은 서비스**를 클릭한 다음 **데이터베이스**로 스크롤하고 **Azure Cosmos DB**를 클릭합니다.
3. Cosmos DB 계정을 선택합니다.
4. 새 창의 **컬렉션** 아래에서 다음 스크린샷과 같이 **규모**를 클릭합니다.
5. 새 창의 드롭다운에서 컬렉션을 선택하고 **처리량** 값을 변경한 다음 **저장**을 클릭합니다.

    ![계정으로 이동하고 규모를 클릭하여 Azure Portal에서 컬렉션에 대한 처리량을 변경하는 방법을 보여 주는 스크린샷](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>.NET SDK를 사용하여 처리량을 설정하려면

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>처리량 FAQ

**내 처리량을 400RU/s 미만으로 설정할 수 있나요?**

Cosmos DB 단일 파티션 컬렉션에서 사용할 수 있는 최소 처리량은 400RU/s이고 분할된 컬렉션에 대한 최소값은 2500RU/s입니다. 요청 단위는 100RU/s 간격으로 설정되어 있지만 처리량은 100RU/s 또는 400RU/s 미만인 값으로 설정할 수 없습니다. Cosmos DB를 개발하고 테스트하는 비용 효과적인 방법을 찾으려는 경우 비용 없이 로컬에 배포할 수 있는 [Azure Cosmos DB 에뮬레이터](documentdb-nosql-local-emulator.md)를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Cosmos DB를 사용하여 프로비전을 수행하고 대규모로 크기를 조정하려면 [Cosmos DB로 분할 및 크기 조정](documentdb-partition-data.md)을 참조하세요.

