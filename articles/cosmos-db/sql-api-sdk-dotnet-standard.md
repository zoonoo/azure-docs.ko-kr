---
title: Azure Cosmos DB는 SQL .NET Standard API, SDK & 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB .NET SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 및 .NET SDK에 대 한 모든 것을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663804"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET Standard SDK for SQL API: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행자-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API 설명서**|[.NET API 참조 설명서](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**샘플**|[.NET 코드 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**시작**|[Azure Cosmos DB .NET SDK 시작](sql-api-get-started.md)|
|**웹앱 자습서**|[Azure Cosmos DB를 사용한 웹 애플리케이션 개발](sql-api-dotnet-application.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>릴리스 정보
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>추가됨
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) 클라이언트 및 쿼리 옵션에 일관성 수준 추가
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) LINQ에 대 한 연속 토큰 지원을 추가 했습니다.
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) 항목 요청 옵션에 트리거 옵션 추가 됨
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) 선택적 설정을 사용 하 여 기본 JSON.net serializer 추가
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) CreateContainerIfNotExistsAsync에 대 한 파티션 키 유효성 검사가 추가 됨
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) LINQ to QueryDefinition API 추가
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) 컨테이너 작성기에 CreateIfNotExistsAsync 추가 됨
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) ResponseMessage에 연속 토큰 속성을 추가 했습니다.
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) LINQ ToStreamIterator 확장 메서드를 추가 했습니다.

#### <a name="fixed"></a>고정
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) CosmosException ()에서 잘못 된 형식의 메시지를 수정 했습니다.
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) LocationCache ConcurrentDict 잠금 경합 수정
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable는 이제 null 쿼리와 함께 작동 합니다.
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) 쿼리가 여러 언어 문화권을 올바르게 처리 합니다.
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) 예기치 않은 예외의 쿼리 구문 분석에 실패 하는 경우 빈 오류 메시지를 수정 함
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) 쿼리가 입력을 스트림으로 올바르게 serialize 합니다.

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* .NET SDK의 [버전 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 일반 공급
* .NET framework 4.6.1 + 및 .NET Core 2.0 +를 지 원하는 2.0 .NET Standard 대상
* 관련 데이터베이스 및 컨테이너 클래스 간에 분할 된 최상위 CosmosClient 및 메서드가 있는 새 개체 모델
* 뛰어난 성능의 새로운 스트림 Api
* 변경 피드 프로세서 Api에 대 한 기본 제공 지원
* CosmosClient, 컨테이너 및 변경 피드 프로세서인 흐름 builder Api
* 자연 스러운 처리량 관리 Api
* 데이터베이스, 컨테이너, 항목, 쿼리 및 처리량 요청에 대 한 세분화 된 RequestOptions 및 ResponseTypes
* 분할 되지 않은 컨테이너를 확장 하는 기능 
* 확장 가능 하 고 사용자 지정 가능한 직렬 변환기
* 사용자 지정 처리기를 지 원하는 확장 가능한 요청 파이프라인


## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Azure Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 년 7 월 29 일 |--- |
| [3.0.0](#3.0.0) |2019 년 7 월 15 일 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고자료
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 

