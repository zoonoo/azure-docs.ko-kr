---
title: Azure Cosmos DB Table API .NET Standard SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜, 각 버전 간의 변경 내용을 포함하여 Azure Cosmos DB Table API 및 .NET Standard SDK에 대한 모든 것을 알아봅니다.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 1c9894772f440a568cbc08890feb961471d84137
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480208"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 테이블 .NET Standard API: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**샘플**|[Cosmos DB 테이블 API.NET 샘플](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**빠른 시작**|[빠른 시작](create-table-dotnet.md)|
|**자습서**|[자습서](tutorial-develop-table-dotnet.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 버그 수정

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 일반 공급 릴리스

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* CloudTableClient 구성할 수 있습니다 어떻게 변경 되었습니다. 이제 걸리는 TableClientConfiguration 개체를 생성 하는 동안. TableClientConfiguration은 Cosmos DB Table API 또는 Azure Storage Table API의 대상 끝점 인지에 따라 클라이언트 동작을 구성 하는 다른 속성을 제공 합니다.
* 사용자 지정 열에서 정렬 된 순서로 결과 반환할 TableQuery에 대 한 지원이 추가 되었습니다. 이 기능은 Cosmos DB 테이블 끝점 에서만 지원 됩니다.
* 다양 한 결과 형식에 RequestCharges 노출할 지원이 추가 되었습니다. 이 기능은 Cosmos DB 테이블 끝점 에서만 지원 됩니다.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Azure Storage Table 엔드포인트에 대한 TablePermissions, ServiceProperties 및 ServiceStats 작업, SAS 토큰 지원을 추가합니다. 
   > [!NOTE]
   > 클라이언트 쪽 암호화와 같은 이전 Azure Storage Table SDK의 일부 기능은 아직 지원되지 않습니다.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Azure Storage Table 엔드포인트에 대한 핵심 CRUD, 배치 및 쿼리 작업 지원을 추가합니다. 
   > [!NOTE]
   > 클라이언트 쪽 암호화와 같은 이전 Azure Storage Table SDK의 일부 기능은 아직 지원되지 않습니다.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Azure Cosmos DB Table.NET Standard SDK는 Cosmos DB의 테이블 데이터 모델에 효율적으로 액세스할 수 있는 플랫폼 간 .NET 라이브러리입니다. 이 초기 릴리스는 [Cosmos DB Table SDK For .NET Framework](table-sdk-dotnet.md)와 비슷한 API를 사용하여 테이블 및 엔터티 CRUD + 쿼리 기능 전체 집합을 지원합니다. 
   > [!NOTE]
   >  Azure Storage Table 엔드포인트는 아직 0.9.1-preview 버전에서 지원되지 않습니다.

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

이 플랫폼 간.NET 표준 라이브러리 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) .NET Framework 라이브러리를 대체할 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)합니다.

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.0.1](#1.0.1) |2019 년 4 월 19 일 |--- |
| [1.0.0](#1.0.0) |2019년 3월 13일 |--- |
| [0.11.0-preview](#0.11.0-preview) |2019 년 3 월 5 일 |--- |
| [0.10.1-preview](#0.10.1-preview) |2019년 1월 22일 |--- |
| [0.10.0-preview](#0.10.0-preview) |2018년 12월 18일 |--- |
| [0.9.1-preview](#0.9.1-preview) |2018년 10월 18일 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Azure Cosmos DB Table API에 대한 자세한 내용은 [Azure Cosmos DB Table API 소개](table-introduction.md)를 참조하세요. 
