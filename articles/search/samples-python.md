---
title: Python 샘플
titleSuffix: Azure Cognitive Search
description: Python 용 Azure .NET SDK 또는 REST를 사용 하는 Azure Cognitive Search demo Python 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955125"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 Python 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 Python 코드 샘플에 대해 알아봅니다. 이러한 샘플은 [**Python 용 AZURE SDK**](/azure/developer/python/)에 대 한 [**azure Cognitive Search 클라이언트 라이브러리**](/python/api/overview/azure/search-documents-readme) 를 사용 하 여 다음 링크를 탐색할 수 있습니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API 참조 | [azure-search-documents](/python/api/azure-search-documents)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서는 API 사용을 보여 줍니다. 이러한 샘플은 GitHub의 [**azure-sdk-python/tree/master/sdk/search/azure-검색-문서/샘플**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) 에서 찾을 수 있습니다.

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

## <a name="doc-samples"></a>Doc 샘플

Cognitive Search 팀의 코드 샘플에서는 기능 및 워크플로를 보여 줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조 됩니다. 이러한 샘플은 GitHub의 [**azure-samples/azure-search-python 샘플**](https://github.com/Azure-Samples/azure-search-python-samples) 에서 찾을 수 있습니다.

| 샘플 | 기사 |
|---------|---------|
| [빠른](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | 빠른 시작에 대 한 소스 코드 [: Python에서 검색 인덱스를 만듭니다](search-get-started-python.md). 이 문서에서는 샘플 데이터를 사용 하 여 검색 인덱스를 만들고 로드 하 고 쿼리 하는 기본 워크플로를 다룹니다. |
| [자습서-ai-보강](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 자습서의 소스 코드 [: Python 및 AI를 사용 하 여 Azure blob에서 검색 가능한 콘텐츠를 생성](cognitive-search-tutorial-blob-python.md)합니다. 이 문서에서는 기술를 사용 하 여 blob 인덱서를 만드는 방법을 보여 줍니다. 여기서 기술는 원시 콘텐츠를 만들고 변환 하 여 검색 하거나 사용할 수 있도록 합니다. |
| [AzureML-사용자 지정 기술](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 소스 코드 [예: Python을 사용 하 여 사용자 지정 기술 만들기](cognitive-search-custom-skill-python.md) 이 문서에서는 Azure Machine Learning의 심층 학습 모델과의 인덱서 및 기술 통합을 보여 줍니다. |

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=python&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.