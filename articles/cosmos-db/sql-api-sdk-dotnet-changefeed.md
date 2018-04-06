---
title: 'Azure Cosmos DB: .NET 변경 피드 프로세서 API, SDK 및 리소스 | Microsoft Docs'
description: 릴리스 날짜, 사용 중지 날짜 및 .NET 변경 피드 프로세서 SDK의 각 버전 간 변경 내용을 포함하여 변경 피드 프로세서 API 및 SDK에 대한 모든 것을 알아봅니다.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/16/2018
ms.author: maquaran
ms.openlocfilehash: 43ba189332cf012a80c309a0dc0f383e38a0915c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 변경 피드 프로세서 SDK: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 설명서**|[피드 프로세서 라이브러리 API 참조 문서 변경](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**시작**|[변경 피드 프로세서 .NET SDK 시작](change-feed.md)|
|**현재 지원되는 프레임워크**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* 안정성 향상
* 수동 검사점 설정 지원
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.21 이상과 호환

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* .NET Standard 2.0에 대한 지원을 추가합니다. 이제 패키지는 `netstandard2.0` 및 `net451` 프레임워크 모니커를 지원합니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.17.0 이상과 호환 가능합니다.
* [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 버전 1.5.1 이상과 호환 가능합니다.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 변경 피드가 비어 있거나 보류 중인 작업이 없을 때 예상되는 남은 작업을 계산할 때 발생하는 문제를 해결했습니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 변경 피드에서 처리될 나머지 작업의 추정치를 얻을 수 있는 메서드가 추가되었습니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.14.1 이하와 호환 가능합니다.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.3.1](#1.3.1) |2018년 3월 13일 |--- |
| [1.2.0](#1.2.0) |2017년 10월 31일 |--- |
| [1.1.1](#1.1.1) |2017년 8월 29일 |--- |
| [1.1.0](#1.1.0) |2017년 8월 13일 |--- |
| [1.0.0](#1.0.0) |2017년 7월 7일 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 

