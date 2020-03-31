---
title: 가져오기 및 인덱싱을 위한 SQL 관계형 데이터 모델링
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 인덱싱 및 전체 텍스트 검색을 위해 관계형 데이터를 정규화하지 않은 평면 결과 집합으로 모델링하는 방법을 알아봅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790084"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search에서 가져오기 및 인덱싱을 위한 관계형 SQL 데이터를 모델링하는 방법

Azure Cognitive Search는 플랫 행 집합을 [인덱싱 파이프라인에](search-what-is-an-index.md)대한 입력으로 허용합니다. 원본 데이터가 SQL Server 관계형 데이터베이스의 조인된 테이블에서 시작된 경우 이 문서에서는 결과 집합을 구성하는 방법과 Azure Cognitive Search 인덱스에서 부모-자식 관계를 모델링하는 방법을 설명합니다.

예를 들어 [데모 데이터를](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)기반으로 하는 가상의 호텔 데이터베이스를 참조합니다. 데이터베이스가 50개의 호텔이 있는 호텔$ 테이블과 다양한 유형, 요금 및 편의 시설이 있는 Rooms$ 테이블로 구성되어 있으며 총 750개의 객실로 구성된다고 가정합니다. 테이블 간에는 일대다 관계가 있습니다. 이 접근 방식에서는 뷰가 각 행에 포함된 관련 룸 세부 정보를 사용하여 호텔당 한 행씩 50개의 행을 반환하는 쿼리를 제공합니다.

   ![호텔 데이터베이스의 테이블 및 보기](media/index-sql-relational-data/hotels-database-tables-view.png "호텔 데이터베이스의 테이블 및 보기")


## <a name="the-problem-of-denormalized-data"></a>비정규화 된 데이터의 문제

일대다 관계로 작업할 때 의한 과제 중 하나는 결합된 테이블에 빌드된 표준 쿼리가 Azure Cognitive Search 시나리오에서 잘 작동하지 않는 비정규화된 데이터를 반환한다는 것입니다. 호텔과 객실을 결합하는 다음 예제를 살펴보겠습니다.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
이 쿼리의 결과는 모든 호텔 필드를 반환하고 모든 룸 필드다음에 예비 호텔 정보가 각 객실 값에 대해 반복됩니다.

   ![비정규화 데이터, 룸 필드추가 시 중복 호텔 데이터](media/index-sql-relational-data/denormalize-data-query.png "비정규화 데이터, 룸 필드추가 시 중복 호텔 데이터")


이 쿼리는 표면에서 성공하지만(플랫 행 집합의 모든 데이터를 제공) 예상되는 검색 경험에 적합한 문서 구조를 제공하지 못합니다. 인덱싱 하는 동안 Azure 인지 검색 각 행에 대 한 하나의 검색 문서를 만듭니다. 검색 문서가 위의 결과와 비슷하다고 생각되면 트윈 돔 호텔에만 7개의 개별 문서인 중복 된 문서가 있는 것으로 인식되었을 것입니다. "플로리다의 호텔"에 대한 쿼리는 트윈 돔 호텔에 대한 7 개의 결과를 반환하여 다른 관련 호텔을 검색 결과에 깊이 밀어 넣습니다.

호텔당 하나의 문서에 대한 예상 된 경험을 얻으려면 올바른 세분성에 행 집합을 제공해야하지만 완전한 정보가 있어야합니다. 다행히이 문서의 기술을 채택 하 여 쉽게이 작업을 수행할 수 있습니다.

## <a name="define-a-query-that-returns-embedded-json"></a>포함된 JSON을 반환하는 쿼리 정의

예상검색 환경을 제공하려면 데이터 집합이 Azure Cognitive Search의 각 검색 문서에 대해 하나의 행으로 구성되어야 합니다. 이 예제에서는 각 호텔에 대해 한 행을 원하지만 사용자가 야간 요금, 침대 크기 및 수 또는 객실 세부 정보의 일부인 해변 보기와 같이 관심 있는 다른 객실 관련 필드를 검색할 수도 있습니다.

해결 방법은 룸 세부 정보를 중첩된 JSON으로 캡처한 다음 두 번째 단계에서와 같이 뷰의 필드에 JSON 구조를 삽입하는 것입니다. 

1. 50개의 호텔과 750개의 객실에 대한 세부 정보가 포함된 두 개의 조인테이블인 호텔$과 Rooms$가 HotelID 필드에 가입되어 있다고 가정합니다. 개별적으로 이 테이블에는 50개의 호텔과 750개의 관련 객실이 있습니다.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. 중첩된 쿼리의 출력을 포함하는`SELECT * from dbo.Hotels$`새 *Rooms* 필드가 추가된 상위 테이블()의 모든 필드로 구성된 뷰를 만듭니다. **JSON으로** 출력을 `SELECT * from dbo.Rooms$` 구조에 대한 For JSON AUTO 절입니다. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   다음 스크린샷은 *룸* nvarchar 필드가 맨 아래에 있는 결과 보기를 보여 주었습니다. *룸* 필드는 호텔객실 보기에만 존재합니다.

   ![호텔 객실 전망](media/index-sql-relational-data/hotelsrooms-view.png "핫룸 보기")

1. 행 `SELECT * FROM dbo.HotelRooms` 집합을 검색하려면 실행합니다. 이 쿼리는 JSON 컬렉션으로 연결된 룸 정보와 함께 호텔당 하나씩 50개의 행을 반환합니다. 

   ![호텔 객실 보기에서 행셋](media/index-sql-relational-data/hotelrooms-rowset.png "호텔 객실 보기에서 행셋")

이제 이 행 집합을 Azure 인지 검색으로 가져올 준비가 되었습니다.

> [!NOTE]
> 이 방법은 포함된 JSON이 SQL Server의 최대 열 크기 제한 미만이라고 [가정합니다.](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server) 데이터가 맞지 않는 경우 [예제: Azure 인지 검색에 대한 AdventureWorks 인벤토리 데이터베이스 모델링에](search-example-adventureworks-modeling.md)표시된 것처럼 프로그래밍 방식으로 방법을 시도할 수 있습니다.

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>일대다 관계의 "다수" 측면에 복잡한 컬렉션 사용

Azure 인지 검색 쪽에서는 중첩된 JSON을 사용하여 일대다 관계를 모델화하는 인덱스 스키마를 만듭니다. 이전 섹션에서 만든 결과 집합은 일반적으로 아래에 제공된 인덱스 스키마에 해당합니다(간결하게 일부 필드를 잘라내겠습니다).

다음 예제는 복잡한 데이터 [형식을 모델링하는 방법의](search-howto-complex-data-types.md#creating-complex-fields)예제와 유사합니다. 이 문서의 초점이 었던 *Rooms* 구조는 *호텔이라는*인덱스의 필드 컬렉션에 있습니다. 이 예제에서는 컬렉션에 허용되는 임의의 여러 항목 수와 달리 고정된 항목 집합으로 구성된다는 점에서 *Rooms와*다른 *주소에* 대한 복잡한 형식을 보여 주기도 합니다.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

이전 결과 집합 및 위의 인덱스 스키마를 감안할 때 성공적인 인덱싱 작업에 필요한 모든 구성 요소가 있습니다. 병합된 데이터 집합은 인덱싱 요구 사항을 충족하면서도 세부 정보를 유지합니다. Azure Cognitive Search 인덱스에서 검색 결과는 개별 룸 및 해당 속성의 컨텍스트를 유지하면서 호텔 기반 엔터티에 쉽게 들어갑니다.

## <a name="next-steps"></a>다음 단계

사용자 고유의 데이터 집합을 사용하여 [데이터 가져오기 마법사를](search-import-data-portal.md) 사용하여 인덱스를 만들고 로드할 수 있습니다. 마법사는 *룸에*포함된 것과 같이 포함된 JSON 컬렉션을 검색하고 복잡한 형식 컬렉션을 포함하는 인덱스 스키마를 유추합니다. 

  ![가져오기 데이터 마법사에서 유추한 인덱스](media/index-sql-relational-data/search-index-rooms-complex-collection.png "가져오기 데이터 마법사에서 유추한 인덱스")

데이터 가져오기 마법사의 기본 단계를 알아보려면 다음 빠른 시작을 시도해 보십시오.

> [!div class="nextstepaction"]
> [빠른 시작: Azure 포털을 사용하여 검색 인덱스 만들기](search-get-started-portal.md)