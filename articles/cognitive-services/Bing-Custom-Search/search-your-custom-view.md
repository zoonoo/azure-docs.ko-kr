---
title: 'Bing Custom Search: 사용자 지정 뷰 검색 | Microsoft Docs'
description: 웹의 사용자 지정 뷰를 검색하는 방법을 설명합니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374695"
---
# <a name="call-your-custom-search"></a>사용자 지정 검색 호출
인스턴스에 대한 검색 결과를 가져오기 위해 Custom Search API를 처음으로 호출하기 전에 Cognitive Services 구독 키를 받아야 합니다. Custom Search API에 대한 키를 받으려면 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)를 참조하세요.

> [!NOTE]
> 2017년 10월 15일 또는 그 이전에 프로비전된 미리 보기 키를 보유한 기존 Bing Custom Search 고객은 2017년 11월 30일까지 또는 허용되는 최대 쿼리 수를 모두 사용할 때까지 해당 키를 사용할 수 있습니다. 그 후에는 Azure의 일반 공급 버전으로 마이그레이션해야 합니다.

## <a name="try-it-out"></a>체험
사용자 지정 검색 환경을 구성한 후 Custom Search 포털 내에서 구성을 테스트할 수 있습니다. [Custom Search](https://customsearch.ai)에 로그인하고 Custom Search 인스턴스를 클릭한 다음, **프로덕션** 탭을 클릭합니다. **끝점** 탭이 표시됩니다. 구독에 따라 시도할 수 있는 끝점이 결정됩니다. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)를 참조하세요. 끝점을 테스트하려면 드롭다운에서 선택하고 관련된 구성 옵션을 설정합니다. 

다음은 사용 가능한 옵션입니다.

- **쿼리**: 검색할 검색어입니다. Web, Image 및 Autosuggest 끝점에만 사용할 수 있습니다.
- **사용자 지정 구성 ID**: 선택한 Custom Search 인스턴스의 구성 ID입니다. 이 필드는 읽기 전용입니다.
- **지역/국가**: 결과가 나오는 지역/국가입니다. Web, Image 및 Hosted UI 끝점에만 사용할 수 있습니다.
- **구독 키**: 테스트에 사용할 구독 키입니다. 드롭다운에서 키를 선택하거나 수동으로 입력할 수 있습니다.
- **유해 정보 차단**: 성인 콘텐츠용 웹 페이지를 필터링하는 데 사용되는 필터입니다. Web, Image 및 Hosted UI 끝점에만 사용할 수 있습니다.
- **개수**: 응답에서 반환할 검색 결과 수입니다. Web 및 Image 끝점에만 사용할 수 있습니다.
- **오프셋**: 응답에서 반환할 검색 결과 수입니다. Web 및 Image 끝점에만 사용할 수 있습니다.

Web, Image 또는 Autosuggest에 대한 필수 옵션을 모두 지정한 후 **호출**을 클릭하여 오른쪽 창에서 JSON 응답을 확인합니다. 

Hosted UI 끝점을 선택한 경우 오른쪽 창에서 검색 환경을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [C#을 사용하여 사용자 지정 뷰 호출](./call-endpoint-csharp.md)
- [Java를 사용하여 사용자 지정 뷰 호출](./call-endpoint-java.md)
- [NodeJs를 사용하여 사용자 지정 뷰 호출](./call-endpoint-nodejs.md)
- [Python을 사용하여 사용자 지정 뷰 호출](./call-endpoint-python.md)