---
title: Azure Cosmos DB Table API .NET Standard SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜, 각 버전 간의 변경 내용을 포함하여 Azure Cosmos DB Table API 및 .NET Standard SDK에 대한 모든 것을 알아봅니다.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: f7c6829d858e36b55f079f8ee03e431f67995e66
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272579"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 다운로드**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**샘플**|[코스모스 DB 테이블 API .NET 샘플](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**빠른 시작**|[빠른 시작](create-table-dotnet.md)|
|**자습서**|[자습서](tutorial-develop-table-dotnet.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**보고서 문제**|[보고서 문제](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2.0.0 시리즈에 대한 릴리스 정보
2.0.0 시리즈는 성능 향상 및 코스모스 DB 끝점에 대한 네임스페이스 통합을 통해 [Microsoft.Azure.Cosmos에](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)대한 종속성을 취합니다.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* 성능 향상 및 코스모스 DB 끝점에 네임스페이스 통합과 함께 [Microsoft.Azure.Cosmos에](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)대한 종속성을 취하는 2.0.0 테이블 SDK의 초기 미리 보기입니다. 공용 API는 동일하게 유지됩니다.

## <a name="release-notes-for-100-series"></a>1.0.0 시리즈에 대한 릴리스 정보
1.0.0 시리즈는 [Microsoft.Azure.DocumentDB.Core에](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)대한 종속성을 취합니다.

### <a name="107"></a><a name="1.0.7"/>1.0.7
* 테이블 SDK 기본 추적 수준을 SourceLevel.Off로 설정하여 성능 향상을 향상시켰으며, app.config를 통해 옵트인할 수 있습니다.

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Cosmos DB 테이블 API와 통신하기 위해 나머지 실행기를 사용하는 TableClientConfiguration 아래에 새 구성을 도입

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5 미리 보기
* 버그 수정

### <a name="104"></a><a name="1.0.4"/>1.0.4
* 버그 수정
* 레스트엑세이터 구성에 대한 HttpClientTimeout 옵션을 제공합니다.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4 미리 보기
* 버그 수정
* 레스트엑세이터 구성에 대한 HttpClientTimeout 옵션을 제공합니다.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* 버그 수정

### <a name="100"></a><a name="1.0.0"/>1.0.0
* 일반 공급 릴리스

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0 미리 보기
* CloudTableClient를 구성할 수 있는 방법에 대한 변경 사항이 변경되었습니다. 이제 생성 하는 동안 TableClientConfiguration 개체를 걸립니다. TableClientConfiguration는 대상 끝점이 Cosmos DB 테이블 API인지 Azure 저장소 테이블 API인지에 따라 클라이언트 동작을 구성하는 다양한 속성을 제공합니다.
* 사용자 지정 열에서 정렬된 순서로 결과를 반환하기 위해 TableQuery에 지원을 추가했습니다. 이 기능은 코스모스 DB 테이블 끝점에서만 지원됩니다.
* 다양한 결과 유형에 요청 요금을 노출하는 지원을 추가했습니다. 이 기능은 코스모스 DB 테이블 끝점에서만 지원됩니다.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Azure Storage Table 엔드포인트에 대한 TablePermissions, ServiceProperties 및 ServiceStats 작업, SAS 토큰 지원을 추가합니다. 
   > [!NOTE]
   > 클라이언트 쪽 암호화와 같은 이전 Azure Storage Table SDK의 일부 기능은 아직 지원되지 않습니다.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Azure Storage Table 엔드포인트에 대한 핵심 CRUD, 배치 및 쿼리 작업 지원을 추가합니다. 
   > [!NOTE]
   > 클라이언트 쪽 암호화와 같은 이전 Azure Storage Table SDK의 일부 기능은 아직 지원되지 않습니다.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Azure Cosmos DB Table.NET Standard SDK는 Cosmos DB의 테이블 데이터 모델에 효율적으로 액세스할 수 있는 플랫폼 간 .NET 라이브러리입니다. 이 초기 릴리스는 [Cosmos DB Table SDK For .NET Framework](table-sdk-dotnet.md)와 비슷한 API를 사용하여 테이블 및 엔터티 CRUD + 쿼리 기능 전체 집합을 지원합니다. 
   > [!NOTE]
   >  Azure Storage Table 엔드포인트는 아직 0.9.1-preview 버전에서 지원되지 않습니다.

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

이 크로스 플랫폼 .NET 표준 라이브러리 [Microsoft.Azure.Cosmos.Table은](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) .NET 프레임워크 라이브러리 [Microsoft.Azure.CosmosDB.Table을](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)대체합니다.

### <a name="200-series"></a>2.0.0 시리즈
| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |오게스트 22, 2019 |--- |

### <a name="100-series"></a>1.0.0 시리즈
| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019년 9월 13일 |--- |
| [1.0.5 미리 보기](#1.0.5-preview) |오게스트 20, 2019 |--- |
| [1.0.4](#1.0.4) |오게스트 12, 2019 |--- |
| [1.0.4 미리 보기](#1.0.4-preview) |2019년 7월 26일 |--- |
| 1.0.2 미리 보기 |2019년 5월 2일 |--- |
| [1.0.1](#1.0.1) |2019년 4월 19일 |--- |
| [1.0.0](#1.0.0) |2019년 3월 13일 |--- |
| [0.11.0 미리 보기](#0.11.0-preview) |2019년 3월 5일 |--- |
| [0.10.1-preview](#0.10.1-preview) |2019년 1월 22일 |--- |
| [0.10.0-preview](#0.10.0-preview) |2018년 12월 18일 |--- |
| [0.9.1 미리 보기](#0.9.1-preview) |2018년 10월 18일 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Azure Cosmos DB Table API에 대한 자세한 내용은 [Azure Cosmos DB Table API 소개](table-introduction.md)를 참조하세요.
