---
title: "Azure DocumentDB .NET SDK 및 리소스 | Microsoft Docs"
description: "릴리스 날짜, 사용 중지 날짜 및 DocumentDB .NET SDK의 각 버전 간의 변경 내용을 포함하는 .NET API 및 SDK에 대한 모든 것을 알아봅니다."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/29/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: c953615ef370dc89ed09bb8a32dc2caf7a7bcc3b
ms.lasthandoff: 03/30/2017


---
# <a name="documentdb-net-sdk-download-and-release-notes"></a>DocumentDB.NET SDK: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 다운로드**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**API 설명서**</td><td>[.NET API 참조 설명서](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**샘플**</td><td>[.NET 코드 샘플](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**시작**</td><td>[DocumentDB .NET SDK 시작](documentdb-get-started.md)</td></tr>

<tr><td>**웹앱 자습서**</td><td>[DocumentDB를 사용한 웹 응용 프로그램 개발](documentdb-dotnet-application.md)</td></tr>

<tr><td>**현재 지원되는 프레임워크**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name11311131httpswwwnugetorgpackagesmicrosoftazuredocumentdb1131"></a><a name="1.13.1"/>[1.13.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.13.1)
* ASP.NET 컨텍스트 내에서 사용할 경우 일부 비동기 API에서 교착 상태를 일으키는 문제를 해결했습니다.

### <a name="a-name11301130httpswwwnugetorgpackagesmicrosoftazuredocumentdb1130"></a><a name="1.13.0"/>[1.13.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.13.0)
* 특정 조건에서 자동 장애 조치(Failover)를 수행하도록 SDK를 좀 더 탄력적으로 만들었습니다.

### <a name="a-name11221122httpswwwnugetorgpackagesmicrosoftazuredocumentdb1122"></a><a name="1.12.2"/>[1.12.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.2)
* 문제를 수정하는 경우 때때로 WebException이 발생합니다. 원격 이름을 확인할 수 없습니다.
* ReadDocumentAsync API에 새 오버로드를 추가하여 형식화된 문서를 직접 읽기 위한 지원을 추가했습니다.

### <a name="a-name11211121httpswwwnugetorgpackagesmicrosoftazuredocumentdb1121"></a><a name="1.12.1"/>[1.12.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.1)
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 LINQ 지원이 추가되었습니다.
* 이벤트 처리기를 사용하여 발생한 ConnectionPolicy 개체의 메모리 누수 문제를 해결합니다.
* ETag를 사용할 때 UpsertAttachmentAsync가 작동하지 않는 문제를 해결합니다.
* 문자열 필드를 정렬할 때 파티션 간 order-by 쿼리 연속 작업이 작동하지 않는 문제를 해결합니다.

### <a name="a-name11201120httpswwwnugetorgpackagesmicrosoftazuredocumentdb1120"></a><a name="1.12.0"/>[1.12.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.0)
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다. [집계 지원](documentdb-sql-query.md#Aggregates)을 참조하세요.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.

### <a name="a-name11141114httpswwwnugetorgpackagesmicrosoftazuredocumentdb1114"></a><a name="1.11.4"/>[1.11.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.4)
* 32비트 호스트 프로세스에서 일부 파티션 간 쿼리가 실패하는 문제를 수정했습니다.
* 게이트웨이 모드에서 실패한 요청에 대한 토큰으로 세션 컨테이너를 업데이트하지 않는 문제를 수정했습니다.
* 경우에 따라 프로젝션에서 UDF 호출을 사용하는 쿼리가 실패하는 문제를 수정했습니다.
* 요청의 읽기 및 쓰기 처리량을 높이기 위해 클라이언트 쪽 성능을 수정했습니다.

### <a name="a-name11131113httpswwwnugetorgpackagesmicrosoftazuredocumentdb1113"></a><a name="1.11.3"/>[1.11.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.3)
* 실패한 요청에 대한 토큰으로 세션 컨테이너를 업데이트하지 않는 문제를 수정했습니다.
* 32비트 호스트 프로세스에서 작동하도록 SDK에 대한 지원을 추가했습니다. 파티션 간 쿼리를 사용하는 경우 향상된 성능을 위해 64비트 호스트를 처리하는 것이 좋습니다.
* IN 식에 많은 수의 파티션 키 값을 사용하는 쿼리와 관련된 시나리오의 성능이 향상되었습니다.
* PopulateQuotaInfo 요청 옵션을 설정하는 경우 문서 컬렉션 읽기 요청에 대한 ResourceResponse에서 다양한 리소스 할당량 통계가 채워집니다.

### <a name="a-name11111111httpswwwnugetorgpackagesmicrosoftazuredocumentdb1111"></a><a name="1.11.1"/>[1.11.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.1)
* 1.11.0에 도입된 CreateDocumentCollectionIfNotExistsAsync API 성능이 약간 향상되었습니다.
* 높은 수준의 동시 요청이 개입되는 시나리오를 위한 SDK의 성능 픽스입니다.

### <a name="a-name11101110httpswwwnugetorgpackagesmicrosoftazuredocumentdb1110"></a><a name="1.11.0"/>[1.11.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.0)
* 컬렉션 내 문서의 [피드 변경](documentdb-change-feed.md) 프로세스에 대한 새 클래스 및 메서드 지원
* 파티션 간 쿼리 연속 및 파티션 간 쿼리에 대한 일부 성능 향상 지원
* CreateDatabaseIfNotExistsAsync 및 CreateDocumentCollectionIfNotExistsAsync 메서드 추가
* 시스템 함수에 대한 LINQ 지원: IsDefined, IsNull 및 IsPrimitive
* project.json 도구를 포함한 프로젝트의 NuGet 패키지를 사용할 때 Microsoft.Azure.Documents.ServiceInterop.dll 및 DocumentDB.Spatial.Sql.dll 어셈블리가 자동으로 응용 프로그램의 휴지통에 들어가는 현상 수정
* 시나리오를 디버깅하는 데 도움이 될 수 있는 클라이언트 쪽 ETW 추적 내보내기 지원

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)
* 분할된 컬렉션에 대한 직접 연결 지원이 추가되었습니다.
* 제한된 부실 일관성 수준에 대한 성능이 향상되었습니다.
* 지역 펜싱 공간 쿼리에 대한 컬렉션 인덱싱 정책을 지정하는 동안 Polygon 및 LineString 데이터 형식이 추가되었습니다.
* 조건자를 변환하는 동안 StringEnumConverter, IsoDateTimeConverter 및 UnixDateTimeConverter에 대한 LINQ 지원이 추가되었습니다.
* 다양한 SDK 버그가 수정되었습니다.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)
* 입력 세션 토큰에 대해 읽기 세션을 사용할 수 없다는 NotFoundException을 발생시키는 문제를 해결했습니다. 이 예외는 지리적으로 분산된 계정의 읽기 영역을 쿼리할 때 가끔 발생합니다.
* 응답에서 내부 스트림에 직접 액세스할 수 있도록 하는 ResourceResponse 클래스의 ResponseStream 속성이 노출되었습니다.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)
* ResourceResponse, FeedResponse, StoredProcedureResponse 및 MediaResponse 클래스가 해당하는 공용 인터페이스를 구현하도록 수정되어 테스트 지향 배포(TDD)를 위해 모의 수행할 수 있도록 합니다.
* 데이터 직렬화를 위해 사용자 지정 JsonSerializerSettings 개체를 사용할 때 잘못된 파티션 키 헤더가 발생하는 문제가 수정되었습니다.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
* 오래 실행하는 쿼리가 오류와 함께 실패하게 한 문제를 해결했습니다: 인증 토큰은 현재 시간에 올바르지 않습니다.
* 크로스 파티션 위쪽/order-by 쿼리에서 원래 SqlParameterCollection을 제거하는 문제를 해결했습니다.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
* 분할된 컬렉션에 대한 병렬 쿼리에 지원을 추가했습니다.
* 분할된 컬렉션에 대한 파티션 간 Order By 및 TOP 쿼리에 대한 지원을 추가했습니다.
* DocumentDB Nuget 패키지에 대한 참조와 함께 DocumentDB 프로젝트를 참조할 때 필요한 DocumentDB.Spatial.Sql.dll 및 Microsoft.Azure.Documents.ServiceInterop.dll에 대한 누락된 참조를 수정했습니다.
* LINQ에서 사용자 정의 함수를 사용할 때 여러 유형의 매개 변수를 사용하도록 기능을 수정했습니다. 
* 쓰기 위치 대신 읽기 위치에 Upsert 호출을 전달하는 전역적으로 복제된 계정에 대한 버그를 수정했습니다.
* IDocumentClient 인터페이스에 누락된 다음 메서드를 추가했습니다. 
  * mediaStream 및 options를 매개 변수로 사용하는 UpsertAttachmentAsync 메서드
  * options를 매개 변수로 사용하는 CreateAttachmentAsync 메서드
  * querySpec을 매개 변수로 사용하는 CreateOfferQuery 메서드
* IDocumentClient 인터페이스에 노출된 공용 클래스를 공개했습니다.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.
* 정제된 요청에 대한 재시도 지원이 추가되었습니다.  사용자가 ConnectionPolicy.RetryOptions 속성을 구성하여 재시도 횟수와 최대 대기 시간을 지정할 수 있습니다.
* 모든 DocumentClient 속성 및 메서드의 시그니처를 정의하는 새로운 IDocumentClient 인터페이스가 추가되었습니다.  이러한 변경의 일환으로 DocumentClient 클래스 자체에서 IQueryable과 IOrderedQueryable을 메서드로 만드는 확장 메서드도 변경되었습니다.
* 특정 DocumentDB 끝점 Uri에 대해 ServicePoint.ConnectionLimit를 설정하는 구성 옵션이 추가되었습니다.  ConnectionPolicy.MaxConnectionLimit를 사용하여 기본값(50)을 변경합니다.
* IPartitionResolver와 해당 구현의 사용이 중단되었습니다.  IPartitionResolver 지원이 중단되었습니다. 보다 큰 저장소 및 처리량에는 파티션된 컬렉션을 사용하는 것이 좋습니다.

### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
* RequestOptions를 매개 변수로 사용하는 Uri 기반 ExecuteStoredProcedureAsync 메서드에 오버로드가 추가되었습니다.

### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
* 문서에 대한 TTL(Time to Live) 지원이 추가되었습니다.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
* Azure 클라우드 서비스 솔루션의 일부로 패키징에 대한 .NET SDK의 Nuget 패키징에서 버그가 수정되었습니다.

### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
* [분할된 컬렉션](documentdb-partition-data.md) 및 [사용자 정의 성능 수준](documentdb-performance-levels.md)이 구현되었습니다. 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
* **[수정됨]** DocumentDB 끝점을 쿼리하면 'System.Net.Http.HttpRequestException: 스트림에 콘텐츠를 복사하는 중 오류가 발생했습니다.'가 나타납니다.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
* 페이징, 조건식 및 범위 비교에 대한 새 연산자를 포함하는 확장 LINQ 지원입니다.
  * LINQ에서 SELECT TOP 동작을 사용하도록 설정하는 Take 연산자
  * 문자열 범위 비교를 사용하도록 설정하는 CompareTo 연산자
  * 조건부 (?) 및 병합 연산자 (??)
* **[수정됨]** 모델 프로젝션을 linq 쿼리의 Where-In과 결합할 때의 ArgumentOutOfRangeException이 수정되었습니다.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
* **[수정됨]** Select가 마지막 식이 아니면 LINQ 공급자는 프로젝션이 없다고 가정하고 SELECT *를 잘못 생성했습니다.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
* Upsert가 구현되고 UpsertXXXAsync 메서드가 추가됨
* 모든 요청에 대한 성능 향상
* 문자열의 조건부, 병합 및 CompareTo 메서드에 대한 LINQ 공급자 지원
* **[수정됨]** LINQ 공급자 --> 목록에 Contains 메서드를 구현하여 IEnumerable 및 배열과 동일한 SQL을 생성합니다.
* **[수정됨]** BackoffRetryUtility가 다시 시도할 때 새로 만드는 대신 동일한 HttpRequestMessage를 다시 사용합니다.
* **[사용되지 않음]** UriFactory.CreateCollection --> 이제 UriFactory.CreateDocumentCollection을 사용해야 합니다.

### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
* **[수정됨]** nl-NL 등과 같은 en 이외의 문화권 정보를 사용할 때의 지역화 문제 

### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
* ID 기반 라우팅
  * ID 기반 리소스 링크 생성을 지원하기 위한 새 UriFactory 도우미
  * URI에서 수행할 DocumentClient의 새 오버로드
* LINQ에서 지리 공간에 대한 IsValid() 및 IsValidDetailed() 추가됨
* LINQ 공급자 지원 향상됨
  * **수학** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **문자열** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **배열** - Concat, Contains, Count
  * **IN** 연산자

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
* 인덱싱 정책 수정 지원 추가됨
  * DocumentClient의 새로운 ReplaceDocumentCollectionAsync 메서드
  * 인덱스 정책 변경 진행률을 추적하기 위한 ResourceResponse<T>의 새로운 IndexTransformationProgress 속성
  * 이제 DocumentCollection.IndexingPolicy를 변경할 수 있음
* 공간 인덱싱 및 쿼리 지원 추가됨
  * 점 및 다각형과 같은 공간 형식을 직렬화/역직렬화하기 위한 새로운 Microsoft.Azure.Documents.Spatial 네임스페이스
  * DocumentDB에 저장된 GeoJSON 데이터를 인덱싱하기 위한 새로운 SpatialIndex 클래스
* **[수정됨]** : linq 식에서 잘못된 SQL 쿼리가 생성됨 [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
* Newtonsoft.Json v5.0.7에 대한 종속성 
* Order By를 지원하기 위한 변경 내용
  
  * OrderBy() 또는 OrderByDescending()에 대한 LINQ 공급자 지원
  * Order By를 지원하기 위한 IndexingPolicy 
    
    **NB: 가능한 새로운 변경** 
    
    사용자 지정 인덱싱 정책을 사용하여 컬렉션을 프로비전하는 기존 코드가 있다면 기존 코드는 새 IndexingPolicy 클래스를 지원하도록 업데이트되어야 합니다. 사용자 지정 인덱싱 정책이 없다면 이 변경 사항은 영향을 주지 않습니다.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
* 새로운 HashPartitionResolver 및 RangePartitionResolver 클래스와 IPartitionResolver를 사용한 데이터 분할 지원
* DataContract 직렬화
* LINQ 공급자의 Guid 지원
* LINQ의 UDF 지원

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
* GA SDK

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.13.1](#1.13.1) |2017년 3월 29일 |--- |
| [1.13.0](#1.13.0) |2017년 3월 24일 |--- |
| [1.12.2](#1.12.2) |2017년 3월 20일 |--- |
| [1.12.1](#1.12.1) |2017년 3월 14일 |--- |
| [1.12.0](#1.12.0) |2017년 2월 15일 |--- |
| [1.11.4](#1.11.4) |2017년 2월 6일 |--- |
| [1.11.3](#1.11.3) |2017년 1월 26일 |--- |
| [1.11.1](#1.11.1) |2016년 12월 21일 |--- |
| [1.11.0](#1.11.0) |2016년 12월 8일 |--- |
| [1.10.0](#1.10.0) |2016년 9월 27일 |--- |
| [1.9.5](#1.9.5) |2016년 9월 1일 |--- |
| [1.9.4](#1.9.4) |2016년 8월 24일 |--- |
| [1.9.3](#1.9.3) |2016년 8월 15일 |--- |
| [1.9.2](#1.9.2) |2016년 7월 23일 |--- |
| [1.8.0](#1.8.0) |2016년 6월 14일 |--- |
| [1.7.1](#1.7.1) |2016년 5월 6일 |--- |
| [1.7.0](#1.7.0) |2016년 4월 26일 |--- |
| [1.6.3](#1.6.3) |2016년 4월 8일 |--- |
| [1.6.2](#1.6.2) |2016년 3월 29일 |--- |
| [1.5.3](#1.5.3) |2016년 2월 19일 |--- |
| [1.5.2](#1.5.2) |2015년 12월 14일 |--- |
| [1.5.1](#1.5.1) |2015년 11월 23일 |--- |
| [1.5.0](#1.5.0) |2015년 10월 5일 |--- |
| [1.4.1](#1.4.1) |2015년 8월 25일 |--- |
| [1.4.0](#1.4.0) |2015년 8월 13일 |--- |
| [1.3.0](#1.3.0) |2015년 8월 5일 |--- |
| [1.2.0](#1.2.0) |2015년 7월 6일 |--- |
| [1.1.0](#1.1.0) |2015년 4월 30일 |--- |
| [1.0.0](#1.0.0) |2015년 4월 8일 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요. 


