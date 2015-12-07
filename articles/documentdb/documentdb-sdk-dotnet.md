<properties 
	pageTitle="DocumentDB .NET SDK | Microsoft Azure" 
	description="릴리스 날짜, 사용 중지 날짜 및 DocumentDB .NET SDK의 각 버전 간의 변경 내용을 포함하는 .NET SDK에 대한 모든 것을 알아봅니다." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB .NET SDK

<table> <tr><td>**다운로드**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr> <tr><td>**설명서**</td><td>[.NET SDK 참조 설명서](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr> <tr><td>**샘플**</td><td>[.NET 코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr> <tr><td>**시작**</td><td>[DocumentDB .NET SDK 시작](documentdb-get-started.md)</td></tr> <tr><td>**현재 지원되는 프레임워크**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr> </table></br>

## 릴리스 정보

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[수정됨]** Select가 마지막 식이 아니면 LINQ 공급자는 프로젝션이 없다고 가정하고 SELECT *를 잘못 생성했습니다. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert가 구현되고 UpsertXXXAsync 메서드가 추가됨
 - 모든 요청에 대한 성능 향상
 - 문자열의 조건부, 병합 및 CompareTo 메서드에 대한 LINQ 공급자 지원
 - **[수정됨]** LINQ 공급자 --> 목록에 Contains 메서드를 구현하여 IEnumerable 및 배열과 동일한 SQL을 생성합니다.
 - **[수정됨]** BackoffRetryUtility가 다시 시도할 때 새로 만드는 대신 동일한 HttpRequestMessage를 다시 사용합니다.
 - **[사용되지 않음]** UriFactory.CreateCollection --> 이제 UriFactory.CreateDocumentCollection을 사용해야 합니다.
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[수정됨]** nl-NL 등과 같은 en 이외의 문화권 정보를 사용할 때의 지역화 문제 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID 기반 라우팅
    - ID 기반 리소스 링크 생성을 지원하기 위한 새 UriFactory 도우미
    - URI에서 수행할 DocumentClient의 새 오버로드
  - LINQ에서 지리 공간에 대한 IsValid() 및 IsValidDetailed() 추가됨
  - LINQ 공급자 지원 향상됨
    - **수학** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **문자열** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **배열** - Concat, Contains, Count
    - **IN** 연산자

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - 인덱싱 정책 수정 지원 추가됨
    - DocumentClient의 새로운 ReplaceDocumentCollectionAsync 메서드
    - 인덱스 정책 변경 진행률을 추적하기 위한 ResourceResponse<T>의 새로운 IndexTransformationProgress 속성
    - 이제 DocumentCollection.IndexingPolicy를 변경할 수 있음
  - 공간 인덱싱 및 쿼리 지원 추가됨
    - 점 및 다각형과 같은 공간 형식을 직렬화/역직렬화하기 위한 새로운 Microsoft.Azure.Documents.Spatial 네임스페이스
    - DocumentDB에 저장된 GeoJSON 데이터를 인덱싱하기 위한 새로운 SpatialIndex 클래스
  - **[수정됨]**: linq 식에서 잘못된 SQL 쿼리가 생성됨 [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Newtonsoft.Json v5.0.7에 대한 종속성 
- Order By를 지원하기 위한 변경 내용
  - OrderBy() 또는 OrderByDescending()에 대한 LINQ 공급자 지원
  - Order By를 지원하기 위한 IndexingPolicy 
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- 새로운 HashPartitionResolver 및 RangePartitionResolver 클래스와 IPartitionResolver를 사용한 데이터 분할 지원
- DataContract 직렬화
- LINQ 공급자의 Guid 지원
- LINQ의 UDF 지원

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]미리 보기와 GA 사이에 NuGet 패키지 이름이 변경되었습니다. **Microsoft.Azure.Documents.Client**에서 **Microsoft.Azure.DocumentDB**로 변경되었습니다. <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Preview SDK[사용되지 않음]

## 릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 매끄럽게 전환할 수 있도록 SDK를 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

> [AZURE.WARNING]**1.0.0** 이전 버전의 .NET에 대한 모든 버전의 Azure DocumentDB SDK는 **2016년 2월 29일**에 사용 중지됩니다.
 
<br/>
 
| 버전 | 릴리스 날짜 | 사용 중지 날짜 
| ---	  | ---	         | ---
| [1\.5.1](#1.5.1) | 2015년 11월 23일 |--| [1\.5.0](#1.5.0) | 2015년 10월 5일 |--| [1\.4.1](#1.4.1) | 2015년 8월 25일 |--| [1\.4.0](#1.4.0) | 2015년 8월 13일 |--| [1\.3.0](#1.3.0) | 2015년 8월 5일 |--| [1\.2.0](#1.2.0) | 2015년 7월 6일 |--| [1\.1.0](#1.1.0) | 2015년 4월 30일 |--| [1\.0.0](#1.0.0) | 2015년 4월 8일 |--| [0\.9.3-prelease](#0.9.x-preview) | 2015년 3월 12일 | 2016년 2월 29일 | [0\.9.2-prelease](#0.9.x-preview) | 2015년 1월 | 2016년 2월 29일 | [.9.1-prelease](#0.9.x-preview) | 2014년 10월 13일 | 2016년 2월 29일 | [0\.9.0-prelease](#0.9.x-preview) | 2014년 8월 21일 | 2016년 2월 29일

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 참고 항목

DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요.

<!---HONumber=AcomDC_1125_2015-->