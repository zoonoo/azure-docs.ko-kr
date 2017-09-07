---
title: "Azure DocumentDB .NET 변경 피드 프로세서 SDK 및 리소스 | Microsoft Docs"
description: "릴리스 날짜, 사용 중지 날짜 및 DocumentDB .NET 변경 피드 프로세서 SDK의 각 버전 간 변경 내용을 포함하여 변경 피드 프로세서 API 및 SDK에 대한 모든 것을 알아봅니다."
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
ms.date: 08/29/2017
ms.author: maquaran
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 239b590a1e3a83fe0205dd8169697db745d7f75e
ms.contentlocale: ko-kr
ms.lasthandoff: 08/29/2017

---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>DocumentDB .NET 변경 피드 프로세서 SDK: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET 변경 피드](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 다운로드**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)</td></tr>

<tr><td>**API 설명서**</td><td>[피드 프로세서 라이브러리 API 참조 문서 변경](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)</td></tr>

<tr><td>**시작**</td><td>[DocumentDB 변경 피드 프로세서 .NET SDK 시작](change-feed.md)</td></tr>

<tr><td>**현재 지원되는 프레임워크**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 변경 피드가 비어 있거나 보류 중인 작업이 없을 때 예상되는 남은 작업을 계산할 때 발생하는 문제를 해결했습니다.
* [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 변경 피드에서 처리될 나머지 작업의 추정치를 얻을 수 있는 메서드가 추가되었습니다.
* [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 버전 1.14.1 이하와 호환 가능

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.1.1](#1.1.1) |2017년 8월 29일 |--- |
| [1.1.0](#1.1.0) |2017년 8월 13일 |--- |
| [1.0.0](#1.0.0) |2017년 7월 7일 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 


