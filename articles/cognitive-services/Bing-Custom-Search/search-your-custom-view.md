---
title: 사용자 지정 보기 검색 - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: 웹의 사용자 지정 보기 검색 방법을 설명합니다.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 15c5b3c58c4f3617111707ed82d031b67b6ad4c1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465138"
---
# <a name="call-your-custom-search"></a>사용자 지정 검색 호출

인스턴스에 대한 검색 결과를 가져오기 위해 Custom Search API를 처음으로 호출하기 전에 Cognitive Services 구독 키를 받아야 합니다. Custom Search API에 대한 키를 받으려면 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)를 참조하세요.


## <a name="try-it-out"></a>체험

사용자 지정 검색 환경을 구성한 후 Custom Search 포털 내에서 구성을 테스트할 수 있습니다. 

1. [Custom Search](https://customsearch.ai)에 로그인합니다.
2. 인스턴스 목록에서 Custom Search 인스턴스를 클릭합니다.
3. **프로덕션** 탭을 클릭합니다. 
4. **엔드포인트** 탭에서 엔드포인트(예: Web API)를 선택합니다. 구독에서 어떤 엔드포인트가 표시되는지 확인합니다(구독 옵션은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) 참조). 
5. 매개 변수의 값을 지정합니다. 

    다음은 설정할 수 있는 가능한 매개 변수입니다(실제 목록은 선택한 엔드포인트에 따라 다름). 이러한 매개 변수에 대한 자세한 내용은 [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) 참조를 확인하세요.

    - **쿼리**: 검색할 검색어입니다. 웹, 이미지, 비디오 및 자동 제안 엔드포인트에만 사용할 수 있습니다.
    - **사용자 지정 구성 ID**: 선택한 Custom Search 인스턴스의 구성 ID입니다. 이 필드는 읽기 전용입니다.
    - **지역/국가**: 결과가 나오는 지역/국가입니다. 웹, 이미지, 비디오 및 호스트된 UI 엔드포인트에만 사용할 수 있습니다.
    - **구독 키**: 테스트에 사용할 구독 키입니다. 드롭다운 목록에서 키를 선택하거나 수동으로 입력할 수 있습니다.  
      
    **추가 매개 변수**를 클릭하면 다음 매개 변수가 표시됩니다.  
      
    - **유해 정보 차단**: 성인 콘텐츠용 웹 페이지를 필터링하는 데 사용되는 필터입니다. 웹, 이미지, 비디오 및 호스트된 UI 엔드포인트에만 사용할 수 있습니다.
    - **사용자 인터페이스 언어**: 사용자 인터페이스 문자열에 사용되는 언어입니다. 예를 들어, 호스트된 UI에서 이미지 및 비디오를 사용하도록 설정한 경우 **이미지** 및 **비디오** 탭에서 지정된 언어를 사용합니다.
    - **개수**: 응답에서 반환할 검색 결과 수입니다. 웹, 이미지 및 비디오 엔드포인트에서만 사용할 수 있습니다.
    - **오프셋**: 결과를 반환하기 전에 건너뛸 검색 결과의 수입니다. 웹, 이미지 및 비디오 엔드포인트에서만 사용할 수 있습니다.

6. 필수 옵션을 모두 지정한 후 **호출**을 클릭하여 오른쪽 창에서 JSON 응답을 확인합니다. 

호스트된 UI 엔드포인트를 선택한 경우 아래쪽 창에서 검색 환경을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [C#을 사용하여 사용자 지정 뷰 호출](./call-endpoint-csharp.md)
- [Java를 사용하여 사용자 지정 뷰 호출](./call-endpoint-java.md)
- [NodeJs를 사용하여 사용자 지정 뷰 호출](./call-endpoint-nodejs.md)
- [Python을 사용하여 사용자 지정 뷰 호출](./call-endpoint-python.md)

- [C# SDK를 사용하여 사용자 지정 뷰 호출](./sdk-csharp-quick-start.md)