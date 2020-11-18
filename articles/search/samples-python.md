---
title: Python 샘플
titleSuffix: Azure Cognitive Search
description: Python 용 Azure .NET SDK 또는 REST를 사용 하는 Azure Cognitive Search demo Python 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686849"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 Python 코드 샘플

Azure Cognitive Search의 기능을 보여 주는 Python 코드 샘플에 대해 알아봅니다. 기본 리포지토리는 다음과 같습니다.

| 리포지토리 | 설명 |
|------------|-------------|
| [azure-s e r v e r-o s/s e r/a s/s e r/s i o/s i d/검색](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | SDK에서 Azure.Search.Documents client 라이브러리와 함께 제공 되는 Azure SDK 팀이 생성 한 샘플입니다. 클라이언트 라이브러리에 대 한 [단위 테스트](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) 를 검토 하 여 다양 한 api를 호출 하는 방법을 확인할 수도 있습니다. |
| [Azure-샘플/azure-검색-python-샘플](https://github.com/Azure-Samples/azure-search-python-samples) | [빠른 시작: Python에서 검색 인덱스 만들기](search-get-started-python.md)를 비롯 한 방법 문서와 함께 제공 되는 코드 샘플입니다.|

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=csharp&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="python-sdk-samples"></a>Python SDK 샘플

Python 용 Azure SDK에는 필수 구성 요소 및 패키지 설치를 포함 하는 다양 한 샘플 및 [시작 페이지가](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) 포함 되어 있습니다. 또한이 페이지에는 편의를 위해 여기에 나열 된 다음 샘플에 대 한 링크도 포함 되어 있습니다.

| 샘플 | Description |
|---------|-------------|
| [인증](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | 클라이언트를 구성 하 고 서비스에 인증 하는 방법을 보여 줍니다. | 
| [인덱스 만들기-읽기-업데이트-삭제 작업](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [인덱서 만들기-읽기-업데이트-삭제 작업](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | [인덱서](search-indexer-overview.md)를 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 다시 설정 하 고, 삭제 하는 방법을 보여 줍니다. |
| [검색 인덱서 데이터 원본](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | [지원 되는 Azure 데이터 원본의](search-indexer-overview.md#supported-data-sources)인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다. |
| [동의어](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | [동의어 맵을](search-synonyms.md)만들고, 업데이트 하 고, 가져오고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.  |
| [문서 로드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | [데이터 가져오기](search-what-is-data-import.md) 작업에서 문서를 인덱스에 업로드 하거나 병합 하는 방법을 보여 줍니다. |
| [단순 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | [기본 쿼리](search-query-overview.md)를 설정 하는 방법을 보여 줍니다. |
| [필터 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | [필터 식을](search-filters.md)설정 하는 방법을 보여 줍니다. |
| [패싯 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | [패싯을](search-filters-facets.md)사용 하는 방법을 보여 줍니다. |

## <a name="documentation-samples"></a>설명서 샘플

다음 샘플에는 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/)에 관련 된 문서가 있습니다.

| 샘플 | Description | 
|---------|-------------|
| [빠른](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | 빠른 시작에 대 한 소스 코드 [: Python에서 검색 인덱스를 만듭니다](search-get-started-python.md).  |
| [자습서-ai-보강](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 자습서의 소스 코드 [: Python 및 AI를 사용 하 여 Azure blob에서 검색 가능한 콘텐츠를 생성](cognitive-search-tutorial-blob-python.md)합니다.  |
| [AzureML-사용자 지정 기술](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 소스 코드 [예: Python을 사용 하 여 사용자 지정 기술 만들기](cognitive-search-custom-skill-python.md)  |