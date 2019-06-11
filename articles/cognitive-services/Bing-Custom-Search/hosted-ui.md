---
title: Bing Custom Search에 대해 호스트된 UI 구성 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 이 문서를 사용하여 Bing Custom Search에 대해 호스트된 UI를 구성하고 통합합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 17c0d19ad0c4a846d5f75905fe9072f2fe0e0f78
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386761"
---
# <a name="configure-your-hosted-ui-experience"></a>호스트된 UI 환경 구성

Bing Custom Search는 JavaScript 코드 조각으로 웹 페이지 및 웹 애플리케이션에 쉽게 통합할 수 있는 호스트된 UI를 제공합니다. Bing Custom Search 포털을 사용하여 UI의 레이아웃, 색 및 검색 옵션을 구성할 수 있습니다.



## <a name="configure-the-custom-hosted-ui"></a>사용자 지정 호스트된 UI 구성

웹 애플리케이션에 대한 호스트된 UI를 구성하려면 다음 단계를 수행합니다. 변경 내용을 적용하면 오른쪽 창에 UI의 미리 보기가 제공됩니다. 표시된 검색 결과는 인스턴스에 대한 실제 결과가 아닙니다.

1. Bing Custom Search [포털](https://customsearch.ai)에 로그인합니다.  
  
2. Bing Custom Search 인스턴스를 선택합니다.

3. **호스트된 UI** 탭을 클릭합니다.  
  
4. 레이아웃을 선택합니다.

    |  |  |
    |---------|---------|
    |검색 창 및 결과(기본값)    | 아래 검색 결과와 함께 검색 상자를 표시합니다.         |
    |결과만     | 검색 상자 없이 검색 결과만 표시합니다. 이 레이아웃을 사용하는 경우 검색 쿼리를 제공해야 합니다(`&q=<query string>`). JavaScript 코드 조각 또는 HTML 엔드포인트 링크로 요청 URL에 쿼리 매개 변수를 추가 합니다.        |
    |팝오버     | 검색 상자를 제공하고 슬라이딩 오버레이로 검색 결과를 표시합니다.        |
    
5. 색 테마를 선택합니다. **사용자 지정 테마**를 클릭하여 애플리케이션에 맞게 색을 사용자 지정할 수 있습니다. 색을 변경하려면 색의 RGB HEX 값(예: `#366eb8`)을 입력하거나 색 미리 보기를 클릭합니다.

   포털의 오른쪽에서 변경 내용을 미리 볼 수 있습니다. **기본값으로 다시 설정**을 클릭하여 변경 내용을 선택한 테마에 대한 기본 색으로 되돌릴 수 있습니다.

   > [!NOTE]
   > 색을 선택할 때 접근성을 고려합니다.

6. **추가 구성** 아래에서 앱에 적합한 값을 제공합니다. 이러한 설정은 선택 사항입니다. 적용하거나 제거한 경우의 효과를 보려면 오른쪽에 있는 미리 보기 창을 확인합니다. 사용 가능한 구성 옵션은 다음과 같습니다.  

7. 검색 구독 키를 입력하거나, 드롭다운 목록에서 키를 선택합니다. 드롭다운 목록은 Azure 계정의 구독에 있는 키로 채워집니다. [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 참조하세요.  

8. 자동 제안을 사용하도록 설정한 경우 자동 제안 구독 키를 입력하거나, 드롭다운 목록에서 키를 선택합니다. 드롭다운 목록은 Azure 계정의 구독에 있는 키로 채워집니다. 사용자 지정 자동 제안에는 특정 구독 계층이 필요합니다. [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)을 참조하세요.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>사용자 지정 UI 사용

호스트된 UI를 사용하려면 다음 중 하나를 수행합니다. 

- 웹 페이지에 스크립트 포함  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- 또는 웹 브라우저에서 다음 URL을 사용합니다.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > 필요에 따라 URL에 다음 쿼리 매개 변수를 추가합니다. 이러한 매개 변수에 대한 자세한 내용은 [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) 참조를 확인하세요.
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > 페이지에 개인정보처리방침이나 기타 고지 사항 및 사용 약관을 표시할 수 없습니다. 사용 적합성은 각기 다를 수 있습니다.  

사용자 지정 구성 ID를 포함하여 추가 정보를 보려면 **프로덕션** 탭 아래의 **엔드포인트**로 이동합니다.

## <a name="configuration-options"></a>구성 옵션

**추가 구성**을 클릭하고 값을 제공하여 호스트된 UI의 동작을 구성할 수 있습니다. 이러한 설정은 선택 사항입니다. 적용하거나 제거한 경우의 효과를 보려면 오른쪽에 있는 미리 보기 창을 확인합니다. 

### <a name="web-search-configurations"></a>웹 검색 구성

|  |  |
|---------|---------|
|활성화된 웹 결과    | 웹 검색이 사용하도록 설정되었는지 여부를 결정합니다(페이지의 맨 위에 있는 [웹] 탭을 참조).        |
|자동 제안 사용     | 사용자 지정 자동 제안이 사용하도록 설정되었는지 여부를 결정합니다(추가 비용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) 참조).        |
|페이지당 웹 결과    | 한 번에 표시할 웹 검색 결과의 수입니다(최댓값은 페이지당 50개 결과임).        |
|이미지 캡션   | 이미지가 검색 결과와 함께 표시되는지 여부를 결정합니다.|


**고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.


|  | |
|---------|---------|
|단어 강조 표시     | 검색어를 굵게 강조하여 결과를 표시할지 여부를 결정합니다.         |
|링크 대상    |  사용자가 검색 결과를 클릭할 때 웹 페이지를 새 브라우저 탭(비어 있음) 또는 동일한 브라우저 탭(자체)에서 열지 여부를 결정합니다.        |

### <a name="image-search-configurations"></a>이미지 검색 구성

| | |
|---------|---------|
|활성화된 이미지 결과     | 이미지 검색이 사용하도록 설정되었는지 여부를 결정합니다(페이지의 맨 위에 있는 이미지 탭을 참조).            |
|페이지당 이미지 결과     | 한 번에 표시할 이미지 검색 결과의 수입니다(최댓값은 페이지당 150개 결과임).          |

**고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  
  
| | |
|---------|---------|
| 필터를 사용하도록 설정     | Bing이 반환하는 이미지를 필터링하는 데 사용자가 사용할 수 있는 필터를 추가합니다. 예를 들어 사용자는 애니메이션 효과가 적용된 GIF에 대한 결과만 필터링할 수 있습니다.|

### <a name="video-search-configurations"></a>비디오 검색 구성

|  | |
|---------|---------|
|활성화된 비디오 결과     | 비디오 검색이 사용하도록 설정되었는지 여부를 결정합니다(페이지의 맨 위에 있는 비디오 탭을 참조).           |
|페이지당 비디오 결과   | 한 번에 표시할 비디오 검색 결과의 수입니다(최댓값은 페이지당 150개 결과임).        |

**고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  
  
|  | |
|---------|---------|
|필터를 사용하도록 설정    | Bing이 반환하는 비디오를 필터링하는 데 사용자가 사용할 수 있는 필터를 추가합니다. 예를 들어, 사용자가 특정 해상도의 비디오 또는 지난 24시간 동안 검색된 비디오에 대해 결과를 필터링할 수 있습니다.          |

### <a name="miscellaneous-configurations"></a>기타 구성


| |  |
|---------|---------|
|페이지 제목   | 검색 결과 페이지의 제목 영역에 표시되는 텍스트입니다(팝오버 레이아웃용 아님).        |
|도구 모음 테마    | 검색 결과 페이지에서 제목 영역의 배경색을 결정합니다. |

**고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  

|열1  |열2  |
|---------|---------|
|검색 상자 텍스트 자리 표시자   | 입력 전에 검색 상자에 표시되는 텍스트입니다.        |
|제목 링크 URL    |제목 링크의 대상입니다.         |
|로고 URL     | 제목 옆에 표시되는 이미지입니다.         |
|즐겨찾기 아이콘    | 브라우저 제목 표시줄에 표시되는 아이콘입니다.          |

다음 구성은 HTML 엔드포인트를 통해 호스트된 UI를 사용하는 경우에만 적용됩니다(JavaScript 코드 조각을 사용하는 경우에는 적용되지 않음).

- 페이지 제목
- 도구 모음 테마
- 제목 링크 URL
- 로고 URL
- 즐겨찾기 아이콘 URL  

## <a name="next-steps"></a>다음 단계

- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)
