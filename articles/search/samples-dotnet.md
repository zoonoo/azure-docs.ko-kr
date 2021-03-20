---
title: .NET 샘플
titleSuffix: Azure Cognitive Search
description: '.NET 클라이언트 라이브러리를 사용 하는 Azure Cognitive Search demo c # 코드 샘플을 찾습니다.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99218165"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 .NET (c #) 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 c # 코드 샘플에 대해 알아봅니다. 이러한 샘플은 azure [**SDK for .net**](/dotnet/azure/)에 대 한 [**azure Cognitive Search 클라이언트 라이브러리**](/dotnet/api/overview/azure/search) 를 사용 하 여 다음 링크를 탐색할 수 있습니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API 참조 | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/테스트](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서는 API 사용을 보여 줍니다. 이러한 샘플은 GitHub의 [**azure/azure-uments/tree/master/sdk/search/Azure.Search.Doc/샘플**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) 에서 찾을 수 있습니다.

| 샘플 | Description |
|---------|-------------|
| [동기적으로 "Hello 세계"](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 동기 메서드를 사용 하 여 클라이언트를 만들고 인증 하 고 오류를 처리 하는 방법을 보여 줍니다.|
| ["Hello 세계", 비동기적으로](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 비동기 메서드를 사용 하 여 클라이언트를 만들고 인증 하 고 오류를 처리 하는 방법을 보여 줍니다.  |
| [서비스 수준 작업](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 인덱스, 인덱서, 데이터 원본, 기술력과 및 동의어 맵을 만드는 방법을 보여 줍니다. 또한이 샘플에서는 서비스 통계를 가져오는 방법 및 인덱스를 쿼리 하는 방법을 보여 줍니다.  |
| [인덱스 작업](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 기존 인덱스에 대 한 작업을 수행 하는 방법을 보여 줍니다 .이 경우 인덱스에 저장 된 문서 수를 가져옵니다.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 지원 되지 않는 데이터 형식으로 작업 하는 방법을 보여 줍니다.  |
| [문서 인덱싱 (푸시 모델)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | JSON 페이로드를 서비스의 인덱스에 보내는 "푸시" 모델 인덱싱   |
| [암호화 키 샘플](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 고객이 관리 하는 암호화 키를 사용 하 여 중요 한 콘텐츠에 대 한 추가 보호 계층을 추가 하는 방법을 보여 줍니다.  |

## <a name="doc-samples"></a>Doc 샘플

Cognitive Search 팀의 코드 샘플에서는 기능 및 워크플로를 보여 줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조 됩니다. 이러한 샘플은 [**azure-samples/azure-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) 및 GitHub의 [**azure-samples/search-dotnet-시작**](https://github.com/Azure-Samples/search-dotnet-getting-started/) 에서 찾을 수 있습니다.

| 샘플 | 아티클  |
|---------|-------------|
| [빠른](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | 빠른 시작에 대 한 소스 코드 [: 검색 인덱스를 만듭니다 ](search-get-started-dotnet.md). 이 문서에서는 샘플 데이터를 사용 하 여 검색 인덱스를 만들고 로드 하 고 쿼리 하는 기본 워크플로를 다룹니다. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [.Net 클라이언트 라이브러리를 사용 하는 방법](search-howto-dotnet-sdk.md)에 대 한 소스 코드입니다. 이 문서에서는 기본 워크플로를 단계별로 안내 하지만 API 사용에 대해 자세히 설명 합니다.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 소스 코드 [예: c #에서 동의어 추가](search-synonyms-tutorial-sdk.md) 동의어 목록은 인덱스 외부에 있는 같아야 용어를 제공 하는 쿼리 확장에 사용 됩니다. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 자습서의 소스 코드 [: .NET SDK를 사용 하 여 AZURE SQL 데이터를 인덱싱합니다](search-indexer-tutorial.md). 이 문서에서는 일정, 필드 매핑 및 매개 변수를 포함 하는 Azure SQL 인덱서를 구성 하는 방법을 보여 줍니다.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [데이터 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법](search-security-manage-encryption-keys.md)에 대 한 소스 코드입니다. |
| [C에서 첫 번째 앱 만들기 #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [자습서: 첫 번째 검색 앱 만들기](tutorial-csharp-create-first-app.md)에 대 한 소스 코드입니다. 대부분의 샘플은 콘솔 응용 프로그램 이지만이 MVC 샘플은 웹 페이지를 사용 하 여 샘플 호텔 인덱스를 앞으로 사용 하 여 기본 검색, 페이지 매김, 자동 완성 및 제안 된 쿼리, 패싯 및 필터를 보여 줍니다. |
| [다중 데이터 원본](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | 자습서의 소스 코드 [: 여러 데이터 소스의 인덱스](tutorial-multiple-data-sources.md)입니다. |
|  [최적화-데이터 인덱싱](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | 자습서의 소스 코드 [: PUSH API를 사용 하 여 인덱싱을 최적화](tutorial-optimize-indexing-push-api.md)합니다.  |
| [자습서-ai-보강](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 자습서의 소스 코드 [: .NET SDK를 사용 하 여 Azure blob에서 AI 생성 검색 가능 콘텐츠](cognitive-search-tutorial-blob-dotnet.md)  |

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=csharp&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플도 Cognitive Search 팀에서 게시 하지만 설명서에서 참조 되지 않습니다. 관련 된 추가 정보 파일은 사용 지침을 제공 합니다.

| 샘플 | Description |
|---------|-------------|
| [azure-검색-전력 기술](https://github.com/Azure-Samples/azure-search-power-skills)  | 원하는 솔루션에 통합할 수 있는 사용할 수 있는 사용자 지정 기술에 대 한 소스 코드입니다.  |
| [지식 마이닝 솔루션 가속기](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 에는 포괄적인 기술 자료 마이닝 솔루션을 프로토타입 하는 데 도움이 되는 템플릿, 지원 파일 및 분석 보고서가 포함 되어 있습니다.  |
| [Covid-19 검색 앱 리포지토리](https://github.com/liamca/covid19search) | Cognitive Search 기반 [Covid-19 검색 앱](https://covid19search.azurewebsites.net/) 에 대 한 소스 코드 리포지토리 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | [Jfk 솔루션](https://www.microsoft.com/ai/ai-lab-jfk-files)에 대해 자세히 알아보세요. |
| [검색 + QnA Maker 액셀러레이터 키](https://github.com/Azure-Samples/search-qna-maker-accelerator) | 검색 기능과 QnA Maker의 기능을 결합 하는 [솔루션](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) 입니다. 라이브 [데모 사이트](https://aka.ms/qnaWithAzureSearchDemo)를 참조 하세요. |