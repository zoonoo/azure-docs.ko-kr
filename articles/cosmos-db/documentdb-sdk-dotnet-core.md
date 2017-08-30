---
title: "Azure Cosmos DB .NET Core API, SDK 및 리소스 | Microsoft Docs"
description: "릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB .NET Core SDK의 각 버전 간 변경 내용을 포함하여 .NET Core API 및 SDK에 대한 모든 것을 알아봅니다."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/11/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a7ce4d771e9c655687f72f4b46c7405cf64aeb74
ms.contentlocale: ko-kr
ms.lasthandoff: 08/12/2017

---
# <a name="azure-cosmos-db-net-core-sdk-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK: 릴리스 정보 및 리소스
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

<tr><td>**SDK 다운로드**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API 설명서**</td><td>[.NET API 참조 설명서](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**샘플**</td><td>[.NET 코드 샘플](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**시작**</td><td>[Azure Cosmos DB .NET Core SDK 시작](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**웹앱 자습서**</td><td>[Azure Cosmos DB를 사용한 웹 응용 프로그램 개발](documentdb-dotnet-application.md)</td></tr>

<tr><td>**현재 지원되는 프레임워크**</td><td>[.NET Standard 1.6 및 .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

Azure Cosmos DB .NET Core SDK에는 [Azure Cosmos DB .NET SDK](documentdb-sdk-dotnet.md)의 최신 버전에 대응하는 기능이 있습니다.

> [!NOTE] 
> Azure Cosmos DB .NET Core SDK는 UWP(유니버설 Windows 플랫폼) 앱과 호환되지 않습니다. UWP 앱을 지원하는 .NET Core SDK에 관심이 있는 경우 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)(으)로 전자 메일을 보내세요.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* 특정 파티션 키 범위 값으로 쿼리 결과의 범위를 지정하기 위해 PartitionKeyRangeId에 대한 지원이 FeedOption으로 추가되었습니다. 
* 해당 시간 이후에 변경 검색을 시작할 수 있도록 StartTime에 대한 지원이 ChangeFeedOption으로 추가되었습니다. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   스택 오버플로 예외를 일으킬 수 있는 JsonSerializable 클래스의 문제가 수정되었습니다.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) 인스턴스를 인스턴스화하는 동안 사용자 지정 JsonSerializerSettings를 지정하기 위한 지원을 추가하였습니다.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   대상 프레임워크 중 하나로 .NET Standard 1.5를 지원합니다.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   SSE4 명령을 지원하지 않고 Azure Cosmos DB 쿼리를 실행할 때 SEHException을 throw하는 X64 컴퓨터에 영향을 준 문제가 해결되었습니다.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   ConsistentPrefix라는 새로운 일관성 수준에 대한 지원이 추가되었습니다.
*   개별 파티션의 쿼리 메트릭에 대한 지원이 추가되었습니다.
*   쿼리의 연속 토큰 크기 제한에 대한 지원이 추가되었습니다.
*   실패한 요청의 자세한 추적에 대한 지원이 추가되었습니다.
*   SDK의 성능이 약간 향상되었습니다.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* 집계 쿼리에 대한 FeedOptions에 제공된 PartitionKey 값을 무시하던 문제를 해결했습니다.
* 플라이트 중간의 파티션 간 Order By 쿼리를 실행하는 동안 파티션 관리의 투명한 처리 문제를 해결했습니다.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* ASP.NET 컨텍스트 내에서 사용할 경우 일부 비동기 API에서 교착 상태를 일으키는 문제를 해결했습니다.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 특정 조건에서 자동 장애 조치(Failover)를 수행하도록 SDK를 좀 더 탄력적으로 만들었습니다.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* 문제를 수정하는 경우 때때로 WebException이 발생합니다. 원격 이름을 확인할 수 없습니다.
* ReadDocumentAsync API에 새 오버로드를 추가하여 형식화된 문서를 직접 읽기 위한 지원을 추가했습니다.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 LINQ 지원이 추가되었습니다.
* 이벤트 처리기를 사용하여 발생한 ConnectionPolicy 개체의 메모리 누수 문제를 해결합니다.
* ETag를 사용할 때 UpsertAttachmentAsync가 작동하지 않는 문제를 해결합니다.
* 문자열 필드를 정렬할 때 파티션 간 order-by 쿼리 연속 작업이 작동하지 않는 문제를 해결합니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다. [집계 지원](documentdb-sql-query.md#Aggregates)을 참조하세요.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK를 사용하면 Windows, Mac 및 Linux에서 실행하는 빠른 플랫폼 간 [ASP.NET Core](https://www.asp.net/core) 및 [.NET Core](https://www.microsoft.com/net/core#windows) 앱을 빌드할 수 있습니다. Azure Cosmos DB .NET Core SDK 최신 릴리스는 [Xamarin](https://www.xamarin.com)과 완벽하게 호환되며 iOS, Android 및 Mono(Linux)를 대상으로 하는 응용 프로그램을 빌드하는 데 사용됩니다.  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Azure Cosmos DB .NET Core Preview SDK를 사용하면 Windows, Mac 및 Linux에서 실행하는 빠른 플랫폼 간 [ASP.NET Core](https://www.asp.net/core) 및 [.NET Core](https://www.microsoft.com/net/core#windows) 앱을 빌드할 수 있습니다.

Azure Cosmos DB .NET Core Preview SDK에는 [Azure Cosmos DB .NET SDK](documentdb-sdk-dotnet.md)의 최신 버전에 대응하는 기능이 있으며 다음이 지원됩니다.
* 모든 [연결 모드](performance-tips.md#networking): 게이트웨이 모드, Direct TCP 및 Direct HTTP 
* 모든 [일관성 수준](consistency-levels.md): 강함, 세션, 제한된 부실, 최종
* [분할된 컬렉션](partition-data.md) 
* [다중 하위 지역 데이터베이스 계정 및 지역에서 복제](distribute-data-globally.md)

이 SDK와 관련된 질문이 있으면 [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)에 게시하거나 [github 저장소](https://github.com/Azure/azure-documentdb-dotnet/issues)에서 문제를 제기하세요. 

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.5.0](#1.5.0) |2017년 8월 10일 |--- | 
| [1.4.1](#1.4.1) |2017년 8월 7일 |--- |
| [1.4.0](#1.4.0) |2017년 8월 2일 |--- |
| [1.3.2](#1.3.2) |2017년 6월 12일 |--- |
| [1.3.1](#1.3.1) |2017년 5월 23일 |--- |
| [1.3.0](#1.3.0) |2017년 5월 10일 |--- |
| [1.2.2](#1.2.2) |2017년 4월 19일 |--- |
| [1.2.1](#1.2.1) |2017년 3월 29일 |--- |
| [1.2.0](#1.2.0) |2017년 3월 25일 |--- |
| [1.1.2](#1.1.2) |2017년 3월 20일 |--- |
| [1.1.1](#1.1.1) |2017년 3월 14일 |--- |
| [1.1.0](#1.1.0) |2017년 2월 16일 |--- |
| [1.0.0](#1.0.0) |2016년 12월 21일 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016년 11월 15일 |2015년 12월 31일 |

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 


