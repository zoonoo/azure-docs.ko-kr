---
title: '빠른 시작: REST API를 사용하여 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: 이 REST API 빠른 시작에서는 Postman 또는 Visual Studio Code를 사용하여 Azure Cognitive Search REST API를 호출하는 방법을 알아봅니다.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711400"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>빠른 시작: REST API를 사용하여 Azure Cognitive Search 인덱스 만들기

이 문서에서는 [Azure Cognitive Search REST API](/rest/api/searchservice) 및 요청을 보내고 받기 위한 API 클라이언트를 사용하여 REST API 요청을 대화형으로 작성하는 방법을 설명합니다. API 클라이언트와 이러한 지침을 사용하면 코드를 작성하기 전에 요청을 보내고 응답을 볼 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 핵심 작업을 수행하는 방법을 배웠으므로 인덱서 또는 인덱싱에 콘텐츠 변환을 추가하는 [보강 검색 파이프라인 설정](cognitive-search-tutorial-blob.md)과 같은 고급 기능에 대한 추가 REST API 호출을 진행할 수 있습니다. 다음 단계에서는 다음 링크를 권장합니다.

> [!div class="nextstepaction"]
> [자습서: REST 및 AI를 사용하여 Azure Blob에서 검색 가능한 콘텐츠 생성](cognitive-search-tutorial-blob.md)