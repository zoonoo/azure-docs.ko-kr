---
title: 사용자 지정 보기 검색 - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: 사용자 지정 검색 환경을 구성한 후 Bing Custom Search 포털 내에서 구성을 테스트할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983115"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>포털에서 Bing Custom Search 인스턴스 호출

사용자 지정 검색 환경을 구성한 후 Bing Custom Search [포털](https://customsearch.ai) 내에서 구성을 테스트할 수 있습니다. 

![Bing 사용자 지정 검색 포털의 스크린샷](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>검색 쿼리 만들기 

Bing 사용자 지정 검색 [포털에](https://customsearch.ai)로그인한 후 검색 인스턴스를 선택하고 **프로덕션** 탭을 클릭합니다. **끝점에서**API 끝점(예: 웹 API)을 선택합니다. 사용자 구독에 따라 표시되는 엔드포인트가 달라집니다.

검색 쿼리를 만들려면 엔드포인트에 대한 매개 변수 값을 입력합니다. 포털에 표시되는 매개 변수는 선택한 엔드포인트에 따라 변경될 수 있습니다. 자세한 내용은 [사용자 지정 검색 API 참조를](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) 참조하십시오. 검색 인스턴스에서 사용하는 구독을 변경하려면 적절한 구독 키를 추가하고 적절한 시장 및/또는 언어 매개 변수를 업데이트합니다.

몇 가지 중요한 매개 변수는 다음과 같습니다.


|매개 변수  |설명  |
|---------|---------|
|쿼리     | 검색할 검색어입니다. 웹, 이미지, 비디오 및 자동 제안 엔드포인트에만 사용할 수 있습니다. |
|사용자 지정 구성 ID | 선택한 Custom Search 인스턴스의 구성 ID입니다. 이 필드는 읽기 전용입니다. |
|시장     | 결과가 나오는 시장입니다. 웹, 이미지, 비디오 및 호스트된 UI 엔드포인트에만 사용할 수 있습니다.        |
|구독 키 | 테스트에 사용할 구독 키입니다. 드롭다운 목록에서 키를 선택하거나 수동으로 입력할 수 있습니다.          |

**추가 매개 변수**를 클릭하면 다음 매개 변수가 표시됩니다.  

|매개 변수  |설명  |
|---------|---------|
|안전 검색     | 성인 콘텐츠용 웹 페이지를 필터링하는 데 사용되는 필터입니다. 웹, 이미지, 비디오 및 호스트된 UI 엔드포인트에만 사용할 수 있습니다. Bing 사용자 지정 비디오 검색은 `moderate` 두 `strict`개의 값만 지원합니다.        |
|사용자 인터페이스 언어    | 사용자 인터페이스 문자열에 사용된 언어입니다. 예를 들어, 호스트된 UI에서 이미지 및 비디오를 사용하도록 설정한 경우 **이미지** 및 **비디오** 탭에서 지정된 언어를 사용합니다.        |
|개수     | 응답에서 반환할 검색 결과 수입니다. 웹, 이미지 및 비디오 엔드포인트에서만 사용할 수 있습니다.         |
|Offset    | 결과를 반환하기 전에 건너뛸 검색 결과의 수입니다. 웹, 이미지 및 비디오 엔드포인트에서만 사용할 수 있습니다.        |
    
필수 옵션을 모두 지정한 후 **호출**을 클릭하여 오른쪽 창에서 JSON 응답을 확인합니다. 호스트된 UI 엔드포인트를 선택한 경우 아래쪽 창에서 검색 환경을 테스트할 수 있습니다.

## <a name="change-your-bing-custom-search-subscription"></a>Bing 사용자 지정 검색 구독 변경

새 인스턴스를 만들지 않고 Bing 사용자 지정 검색 인스턴스와 연결된 구독을 변경할 수 있습니다. API 호출을 보내고 새 구독으로 청구하려면 Azure Portal에서 새 Bing 사용자 지정 검색 리소스를 만듭니다. 인스턴스의 사용자 지정 구성 ID와 함께 API 요청의 새 구독 키를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [C#을 사용하여 사용자 지정 뷰 호출](./call-endpoint-csharp.md)
- [Java를 사용하여 사용자 지정 뷰 호출](./call-endpoint-java.md)
- [NodeJs를 사용하여 사용자 지정 뷰 호출](./call-endpoint-nodejs.md)
- [Python을 사용하여 사용자 지정 뷰 호출](./call-endpoint-python.md)

- [C# SDK를 사용하여 사용자 지정 뷰 호출](./sdk-csharp-quick-start.md)
