---
title: Azure Cosmos DB Table API .NET SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 각 버전의 변경 내용을 포함하여 Azure Cosmos DB Table API에 대해 자세히 알아봅니다.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: db7cc556525ab57f14984232bf1797764865fca3
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606230"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB 테이블 .NET API: 다운로드 및 릴리스 정보

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API 설명서**|[.NET API 참조 설명서](https://aka.ms/acdbtableapiref)|
|**빠른 시작**|[Azure Cosmos DB: .NET 및 Table API를 사용하여 앱 빌드](create-table-dotnet.md)|
|**자습서**|[Azure Cosmos DB: .NET의 Table API를 사용하여 개발](tutorial-develop-table-dotnet.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 는 유지 관리에서 모드와 곧 중단 될 예정입니다. 새.NET Standard 라이브러리를 업그레이드 하세요 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) Table API에서 지 원하는 최신 기능을 계속 합니다.

> 미리 보기 도중에 Table API 계정을 만든 경우 일반 공급되는 Table API SDK와 작동하는 [새 Table API 계정](create-table-dotnet.md#create-a-database-account)을 만드세요.
>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* 버그 수정

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* 다중 지역 쓰기 지원이 추가되었습니다.
* Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial에 대한 NuGet 패키지 종속성이 수정되었습니다.

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Microsoft.Azure.Storage.Common 및 Microsoft.Azure.DocumentDB에 대한 NuGet 패키지 종속성이 수정되었습니다.
* JsonConvert.DefaultSettings가 구성된 경우, 테이블 serialization에 대한 버그 수정입니다.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 직접 모드에서 잘못된 형식의 Etag에 대한 유효성 검사를 추가했습니다.
* 게이트웨이 모드에서 LINQ 쿼리 버그를 수정했습니다.
* 이제 동기 API는 SynchronizationContext를 사용하여 스레드 풀에서 실행됩니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* TableRequestOptions에 TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism 및 TableQueryContinuationTokenLimitInKb 추가
* 버그 수정

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* 일반 공급 릴리스

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* 초기 미리 보기 릴리스

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜

Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

`Microsoft.Azure.CosmosDB.Table` 라이브러리는.NET Framework에 대 한 현재 사용할 수 있는 전용 유지 관리 모드 및 곧 중단 될 예정입니다. 새로운 기능 및 기능 및 최적화는만.NET Standard 라이브러리에 추가 되어 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)처럼 이러한 it를 업그레이드 하는 것이 좋습니다 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

합니다 [Windowsazure.storage-premiumtable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) 미리 보기 패키지 되지 않습니다. WindowsAzure.Storage-PremiumTable SDK는 2018년 11월 15일부터 사용이 중지되고 사용 중지된 SDK에 대한 요청도 허용되지 않습니다. 

사용 중지된 SDK를 사용하는 Azure Cosmos DB에 대한 요청은 서비스에서 거부됩니다.
<br/>

| Version | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.1.0](#2.1.0) |2019년 1월 22일|2020 년 4 월 1 일 |
| [2.0.0](#2.0.0) |2018년 9월 26일|2020 년 3 월 1 일 |
| [1.1.3](#1.1.3) |2018년 7월 17일|2019 년 12 월 1 일 |
| [1.1.1](#1.1.1) |2018년 3월 26일|2019 년 12 월 1 일 |
| [1.1.0](#1.1.0) |2018년 2월 21일|2019 년 12 월 1 일 |
| [1.0.0](#1.0.0) |2017년 11월 15일|2019 년 11 월 15 일 |
| 0.9.0-preview |2017년 11월 11일 |2019 년 11 월 11 일 |

## <a name="troubleshooting"></a>문제 해결

Microsoft.Azure.CosmosDB.Table NuGet 패키지를 사용하려고 할 때, 오류가 발생하면 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

문제를 해결할 수 있는 두 가지 옵션이 있습니다.

* 패키지 관리 콘솔을 사용하여 Microsoft.Azure.CosmosDB.Table 패키지와 해당 종속성을 설치합니다. 이렇게 하려면 솔루션의 패키지 관리자 콘솔에 다음을 입력합니다. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* 원하는 NuGet 패키지 관리 도구를 사용하여 Microsoft.Azure.CosmosDB.Table을 설치하기 전에 Microsoft.Azure.Storage.Common NuGet 패키지를 설치합니다.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목

Azure Cosmos DB Table API에 대한 자세한 내용은 [Azure Cosmos DB Table API 소개](table-introduction.md)를 참조하세요. 