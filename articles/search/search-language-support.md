---
title: 영어 이외의 검색 쿼리에 대한 다국어 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search는 56개 언어를 지원하며 Microsoft의 Lucene 및 자연어 처리 기술의 언어 분석기를 활용합니다.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793583"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Azure Cognitive Search에서 여러 언어에 대한 인덱스를 만드는 방법

인덱스에는 여러 언어의 콘텐츠가 포함된 필드(예: 언어별 문자열에 대한 개별 필드 만들기)가 포함될 수 있습니다. 인덱싱 및 쿼리 중에 최상의 결과를 얻으려면 적절한 언어 규칙을 제공하는 언어 분석기를 할당합니다. 

Azure Cognitive Search는 분석기 속성을 사용하여 개별 필드에 할당할 수 있는 Lucene 및 Microsoft의 다양한 언어 분석기를 제공합니다. 이 문서에 설명된 대로 포털에서 언어 분석기를 지정할 수도 있습니다.

## <a name="add-analyzers-to-fields"></a>필드에 분석기 추가

필드를 만들 때 언어 분석기지정됩니다. 분석기를 기존 필드 정의에 추가하려면 인덱스를 덮어쓰고 다시 로드하거나 원본과 동일한 새 필드를 만들어야 하지만 분석기 할당이 있습니다. 그런 다음 사용자의 편의를 위해 사용하지 않는 필드를 삭제할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com) 로그인하여 검색 서비스를 찾습니다.
1. 서비스 대시보드 위쪽의 명령 모음에 있는 **인덱스 추가** 를 클릭하여 새 인덱스를 시작하거나, 기존 인덱스를 여러 기존 인덱스에 추가하는 새 필드에 대해 분석기를 설정합니다.
1. 이름을 제공하여 필드 정의를 시작합니다.
1. Edm.String 데이터 형식을 선택합니다. 문자열 필드만 전체 텍스트를 검색할 수 있습니다.
1. 검색 **가능한** 특성을 설정하여 분석기 속성을 사용하도록 설정합니다. 언어 분석기를 사용하려면 필드가 텍스트 기반이어야 합니다.
1. 사용 가능한 분석기 중 하나를 선택합니다. 

![필드 정의 중에 언어 분석기 할당](media/search-language-support/select-analyzer.png "필드 정의 중에 언어 분석기 할당")

기본적으로 모든 검색 가능한 필드는 언어에 구애받지 않는 [표준 Lucene 분석기를](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) 사용합니다. 지원되는 분석기의 전체 목록을 보려면 [Azure Cognitive Search 인덱스에 언어 분석기 추가](index-add-language-analyzers.md)를 참조하십시오.

포털에서 분석기는 있는 대로 사용됩니다. 사용자 지정 또는 필터 및 토큰화기의 특정 구성이 필요한 경우 코드에서 [사용자 지정 분석기를 만들어야](index-add-custom-analyzers.md) 합니다. 포털은 사용자 지정 분석기 의 선택 또는 구성을 지원하지 않습니다.

## <a name="query-language-specific-fields"></a>언어별 필드 쿼리

필드에 대해 언어 분석기를 선택한 후에는 해당 필드의 모든 인덱싱 및 검색 요청에 해당 분석기를 사용합니다. 서로 다른 분석기를 사용하여 여러 필드에 대해 쿼리가 발급되면 각 필드에 대해 할당된 분석기에서 쿼리가 독립적으로 처리됩니다.

쿼리를 실행한 에이전트의 언어를 아는 경우 검색 요청의 범위를 **searchFields** 쿼리 매개 변수를 사용하여 특정 필드로 지정할 수 있습니다. 다음 쿼리는 폴란드어로 된 설명에 대해서만 실행됩니다.

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

[**검색 탐색기를**](search-explorer.md) 사용하여 포털에서 인덱스를 쿼리하여 위에 표시된 것과 유사한 쿼리에 붙여넣을 수 있습니다.

## <a name="boost-language-specific-fields"></a>언어별 필드 향상

간혹 쿼리를 실행한 에이전트의 언어를 알지 못할 수 있습니다. 이 경우 모든 필드에 대해 동시에 쿼리를 실행할 수 있습니다. 필요한 경우 [평가 프로필](index-add-scoring-profiles.md)을 사용하여 특정 언어로 된 결과에 대한 우선 순위를 정의할 수 있습니다. 아래 예에서는 영어 설명에서 찾은 일치 항목이 폴란드 및 프랑스어 항목 일치보다 상대적으로 높은 점수를 받게 됩니다.

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>다음 단계

.NET 개발자인 경우 [Azure 인지 검색 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) 및 [분석기](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 속성을 사용하여 언어 분석기를 구성할 수 있습니다. 