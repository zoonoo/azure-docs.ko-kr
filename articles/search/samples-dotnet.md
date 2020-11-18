---
title: .NET 샘플
titleSuffix: Azure Cognitive Search
description: '.NET 클라이언트 라이브러리를 사용 하는 Azure Cognitive Search demo c # 코드 샘플을 찾습니다.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687091"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 .NET (c #) 코드 샘플

Azure Cognitive Search의 기능을 보여 주는 c # 코드 샘플에 대해 알아봅니다. 기본 리포지토리는 다음과 같습니다.

| 리포지토리 | 설명 |
|------------|-------------|
| [azure-.net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | SDK에서 Azure.Search.Documents client 라이브러리와 함께 제공 되는 Azure SDK 팀이 생성 한 샘플입니다. 클라이언트 라이브러리에 대 한 [단위 테스트](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) 를 검토 하 여 다양 한 api를 호출 하는 방법을 확인할 수도 있습니다. |
| [Azure-샘플/azure-검색-dotnet-샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples) | [.Net 클라이언트 라이브러리를 사용 하는 방법을](search-howto-dotnet-sdk.md)비롯 하 여 설명서에서 방법 문서와 함께 제공 되는 샘플입니다.|
| [Azure-샘플/검색-dotnet-시작](https://github.com/Azure-Samples/search-dotnet-getting-started) | 설명서의 빠른 시작 및 자습서와 함께 제공 되는 샘플입니다.|

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=csharp&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="net-sdk-samples"></a>.NET SDK 샘플

.NET 용 Azure SDK에는 다양 한 샘플과 각각을 설명 하는 [샘플 추가](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) 정보가 포함 되어 있습니다. 이 목록은 사용자 편의를 위해 아래에 제공 됩니다.

| 샘플 | Description |
|---------|-------------|
| [동기적으로 "Hello 세계"](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 동기 메서드를 사용 하 여 클라이언트를 만들고 인증 하 고 오류를 처리 하는 방법을 보여 줍니다.|
| ["Hello 세계", 비동기적으로](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 비동기 메서드를 사용 하 여 클라이언트를 만들고 인증 하 고 오류를 처리 하는 방법을 보여 줍니다.  |
| [서비스 수준 작업](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 인덱스, 인덱서, 데이터 원본, 기술력과 및 동의어 맵을 만드는 방법을 보여 줍니다. 또한이 샘플에서는 서비스 통계를 가져오는 방법 및 인덱스를 쿼리 하는 방법을 보여 줍니다.  |
| [인덱스 작업](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 기존 인덱스에 대 한 작업을 수행 하는 방법을 보여 줍니다 .이 경우 인덱스에 저장 된 문서 수를 가져옵니다.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 지원 되지 않는 데이터 형식으로 작업 하는 방법을 보여 줍니다.  |
| [문서 인덱싱 (푸시 모델)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | JSON 페이로드를 서비스의 인덱스에 보내는 "푸시" 모델 인덱싱   |
| [암호화 키 샘플](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 고객이 관리 하는 암호화 키를 사용 하 여 중요 한 콘텐츠에 대 한 추가 보호 계층을 추가 하는 방법을 보여 줍니다.  |

## <a name="documentation-samples"></a>설명서 샘플

다음 샘플에는 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/)에 관련 된 문서가 있습니다.

| 샘플 | Description |
|---------|-------------|
| [빠른](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | 빠른 시작에 대 한 소스 코드 [: 검색 인덱스를 만듭니다 ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [.Net 클라이언트 라이브러리를 사용 하는 방법](search-howto-dotnet-sdk.md) 에 대 한 소스 코드 |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 동의어 목록은 인덱스 외부에 있는 같아야 용어를 제공 하는 쿼리 확장에 사용 됩니다. 이 샘플은 [c #에서 동의어 추가 예제](search-synonyms-tutorial-sdk.md)에 포함 되어 있습니다. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 다양 한 문서에서 인덱서 관련 조각 뒤의 소스 코드입니다. 이 예에서는 일정, 필드 매핑 및 매개 변수를 포함 하는 인덱서를 구성 하는 방법을 보여 줍니다.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [데이터 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법](search-security-manage-encryption-keys.md) 에 대 한 소스 코드 |
| [C에서 첫 번째 앱 만들기 #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [자습서: 첫 번째 검색 앱 만들기](tutorial-csharp-create-first-app.md)에 대 한 소스 코드입니다. 대부분의 샘플은 콘솔 응용 프로그램 이지만이 MVC 샘플은 웹 페이지를 사용 하 여 샘플 호텔 인덱스를 앞으로 사용 하 여 기본 검색, 페이지 매김, 자동 완성 및 제안 된 쿼리, 패싯 및 필터를 보여 줍니다. |
| [다중 데이터 원본](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | 자습서의 소스 코드 [: 여러 데이터 소스의 인덱스](tutorial-multiple-data-sources.md)입니다. |
|  [최적화-데이터 인덱싱](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | 자습서의 소스 코드 [: PUSH API를 사용 하 여 인덱싱을 최적화](tutorial-optimize-indexing-push-api.md)합니다.  |
| [자습서-ai-보강](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 자습서의 소스 코드 [: .NET SDK를 사용 하 여 Azure blob에서 AI 생성 검색 가능 콘텐츠](cognitive-search-tutorial-blob-dotnet.md)  |

## <a name="standalone-samples-and-solutions"></a>독립 실행형 샘플 및 솔루션

| 샘플 | Description |
|---------|-------------|
| [azure-검색-전력 기술](https://github.com/Azure-Samples/azure-search-power-skills)  | 원하는 솔루션에 통합할 수 있는 사용할 수 있는 사용자 지정 기술에 대 한 소스 코드입니다.  |
| [지식 마이닝 솔루션 가속기](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 에는 포괄적인 기술 자료 마이닝 솔루션을 프로토타입 하는 데 도움이 되는 템플릿, 지원 파일 및 분석 보고서가 포함 되어 있습니다.  |
| [Covid-19 검색 앱 리포지토리](https://github.com/liamca/covid19search) | Cognitive Search 기반 [Covid-19 검색 앱](https://covid19search.azurewebsites.net/) 에 대 한 소스 코드 리포지토리 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | [Jfk 솔루션](https://www.microsoft.com/ai/ai-lab-jfk-files)에 대해 자세히 알아보세요. |