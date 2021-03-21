---
title: REST 샘플
titleSuffix: Azure Cognitive Search
description: 검색 또는 관리 REST Api를 사용 하는 Azure Cognitive Search demo REST 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956529"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 REST 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 REST API 샘플에 대해 알아봅니다. 이러한 샘플은 [**검색 REST api**](/rest/api/searchservice)를 사용 합니다.

REST는 Azure Cognitive Search에 대 한 결정적인 프로그래밍 인터페이스 이며, 프로그래밍 방식으로 호출할 수 있는 모든 작업은 먼저 REST에서 사용 가능 하 고 Sdk에서 사용할 수 있습니다. 이러한 이유로 설명서의 대부분의 예제에서는 REST Api를 활용 하 여 중요 한 개념을 설명 하거나 설명 합니다.

REST 샘플은 일반적으로 Postman에서 개발 되 고 테스트 되지만 HTTP 호출을 지 원하는 모든 클라이언트를 사용할 수 있습니다.

+ 빠른 시작: HTTP 호출을 작성 하는 데 도움이 되는 [REST api를 사용 하 여 Azure Cognitive Search 인덱스를 만듭니다](search-get-started-rest.md) .
+ Visual Studio Code 작업 하는 경우 [Azure Cognitive Search에 대 한 Visual Studio Code 확장](search-get-started-vs-code.md)(현재 미리 보기 상태)을 사용해 보세요.

## <a name="doc-samples"></a>Doc 샘플

Cognitive Search 팀의 코드 샘플에서는 기능 및 워크플로를 보여 줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조 됩니다. 이러한 샘플은 GitHub의 [**azure-samples/azure-search-postman-샘플**](https://github.com/Azure-Samples/azure-search-postman-samples) 에서 찾을 수 있습니다.

| 샘플 | 아티클 |
|---------|---------|
| [빠른 시작](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | 빠른 시작에 대 한 소스 코드 [: REST api를 사용 하 여 검색 인덱스를 만듭니다](search-get-started-rest.md). 이 문서에서는 샘플 데이터를 사용 하 여 검색 인덱스를 만들고 로드 하 고 쿼리 하는 기본 워크플로를 다룹니다. |
| [자습서](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | 자습서의 소스 코드 [: REST 및 AI를 사용 하 여 Azure blob에서 검색 가능한 콘텐츠를 생성](cognitive-search-tutorial-blob.md)합니다. 이 문서에서는 정보를 추출 하 고 구조를 유추 하기 위해 Azure blob을 반복 하는 기술를 만드는 방법을 보여 줍니다.|
| [디버그-세션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | 자습서의 소스 코드 [: 기술에 대 한 변경 내용을 진단, 복구 및 커밋합니다](cognitive-search-tutorial-debug-sessions.md). 이 문서에서는 Azure Portal에서 기술 디버그 세션을 사용 하는 방법을 보여 줍니다. REST는 디버그 하는 동안 사용 되는 개체를 만드는 데 사용 됩니다.|
| [사용자 지정-분석기](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | 자습서의 소스 코드 [: 전화 번호에 대 한 사용자 지정 분석기를 만듭니다](tutorial-create-custom-analyzer.md). 이 문서에서는 분석기를 사용 하 여 검색 가능한 콘텐츠에서 패턴 및 특수 문자를 유지 하는 방법을 설명 합니다.|
| [기술 자료-스토어](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | [REST 및 Postman을 사용 하 여 기술 자료 저장소 만들기](knowledge-store-create-rest.md)에 대 한 소스 코드입니다. 이 문서에서는 지식 마이닝 워크플로에 사용 되는 기술 자료 저장소를 채우는 데 필요한 단계를 설명 합니다. |
| [프로젝션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | [강화를 모양 및 내보내는 방법](knowledge-store-projections-examples.md)에 대 한 소스 코드입니다. 이 문서에서는 기술 자료 저장소에서 물리적 데이터 구조를 지정 하는 방법을 설명 합니다.|
| [인덱스 암호화-blob](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | [Blob 인덱서 및 기술력과를 사용 하 여 암호화 된 blob을 인덱싱하는 방법](search-howto-index-encrypted-blobs.md)에 대 한 소스 코드입니다. 이 문서에서는 Azure Key Vault를 사용 하 여 이전에 암호화 된 Azure Blob storage의 문서를 인덱싱하는 방법을 보여 줍니다. |

> [!Tip]
> [샘플 브라우저](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) 에서 제품, 서비스 및 언어를 기준으로 필터링 된 Microsoft 코드 샘플을 검색 해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플도 Cognitive Search 팀에서 게시 하지만 설명서에서 참조 되지 않습니다. 관련 된 추가 정보 파일은 사용 지침을 제공 합니다.

| 샘플 | Description |
|---------|-------------|
| [쿼리-예제](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | 유사 항목 검색, RegEx 및 와일드 카드 검색, 자동 완성 등을 비롯 한 다양 한 쿼리 기법을 보여 주는 postman 컬렉션. |