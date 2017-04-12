---
title: "Azure DocumentDB의 날짜 사용 | Microsoft Docs"
description: "Azure DocumentDB에서 날짜로 작업하는 방법에 대해 알아봅니다."
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c18d17d40dff658a8fc47ef2126dd2c21b68606a
ms.lasthandoff: 03/29/2017


---
# <a name="working-with-dates-in-azure-documentdb"></a>Azure DocumentDB의 날짜 사용
DocumentDB는 네이티브 [JSON](http://www.json.org) 데이터 모델을 통해 스키마 유연성과 풍부한 인덱싱을 제공합니다. 데이터베이스, 컬렉션, 문서 및 저장 프로시저를 포함한 모든 DocumentDB 리소스는 모델링되어 JSON 문서로 저장됩니다. 이식 가능성을 위한 요구 사항인 JSON(및 DocumentDB)은 String, Number, Boolean, Array, Object 및 Null과 같은 기본 형식만 지원합니다. 그러나 JSON은 유연하므로 개발자와 프레임워크는 이러한 기본형을 사용하여 개체 또는 배열로 구성하여 보다 복잡한 형식을 나타낼 수 있습니다. 

기본형 외에도 많은 응용 프로그램에는 날짜 및 타임스탬프를 나타내기 위해 [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) 형식이 필요합니다. 이 문서에서는 개발자가 .NET SDK를 사용하여 DocumentDB에서 날짜를 저장, 검색 및 쿼리하는 방법에 대해 설명합니다.

## <a name="storing-datetimes"></a>날짜/시간 저장
기본적으로 [DocumentDB SDK](documentdb-sdk-dotnet.md)는 DateTime 값을 [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874) 문자열로 직렬화합니다. 대부분의 응용 프로그램에서는 다음과 같은 이유로 DateTime의 기본 문자열 표현을 사용할 수 있습니다.

* 문자열을 비교할 수 있으며, 문자열로 변환할 때 DateTime 값의 상대적인 순서는 유지됩니다. 
* 이 접근 방식에는 JSON 변환에 대해 사용자 지정 코드 또는 속성이 필요하지 않습니다.
* JSON에 저장된 날짜는 사람이 읽을 수 있습니다.
* 이 접근 방식은 빠른 쿼리 성능을 위해 DocumentDB의 인덱스를 활용할 수 있습니다.

예를 들어 다음 코드 조각에서는 .NET SDK를 사용하여 두 가지 DateTime 속성(`ShipDate` 및 `OrderDate`)을 문서로 포함하는 `Order` 개체를 저장합니다.

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

이 문서는 다음과 같이 DocumentDB에 저장됩니다.

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

또는 DateTime을 Unix 타임스탬프, 즉 1970년 1월 1일 이후로 경과된 초 수를 나타내는 숫자로 저장할 수 있습니다. DocumentDB의 내부 타임스탬프(`_ts`) 속성은 이 접근 방식을 따릅니다. [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) 클래스를 사용하여 DateTime을 숫자로 직렬화할 수 있습니다. 

## <a name="indexing-datetimes-for-range-queries"></a>범위 쿼리의 날짜/시간 인덱싱
범위 쿼리는 일반적으로 DateTime 값입니다. 예를 들어 어제 이후로 만들어진 주문을 모두 찾거나 지난 5분 내에 배송된 주문을 모두 찾으려면 범위 쿼리를 수행해야 합니다. 이러한 쿼리를 효율적으로 실행하려면 범위 인덱싱을 위한 컬렉션을 문자열에 구성해야 합니다.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

[DocumentDB 인덱싱 정책](documentdb-indexing-policies.md)에서 인덱싱 정책을 구성하는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="querying-datetimes-in-linq"></a>LINQ에서 날짜/시간 쿼리
DocumentDB .NET SDK는 LINQ를 통해 DocumentDB에 저장된 데이터의 쿼리를 자동으로 지원합니다. 예를 들어 다음 코드 조각에서는 지난 3일 동안 배송된 주문을 필터링하는 LINQ 쿼리를 보여 줍니다.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on DocumentDB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

[DocumentDB 쿼리](documentdb-sql-query.md)에서 DocumentDB의 SQL 쿼리 언어와 LINQ 공급자에 대해 자세히 알아볼 수 있습니다.

이 문서에서는 DocumentDB에서 날짜/시간을 저장, 인덱싱 및 쿼리하는 방법에 대해 살펴보았습니다.

## <a name="next-steps"></a>다음 단계
* [GitHub 샘플 코드](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)(영문)에서 코드 샘플을 다운로드하여 실행합니다.
* [DocumentDB 쿼리](documentdb-sql-query.md)
* [DocumentDB 인덱싱 정책](documentdb-indexing-policies.md)

