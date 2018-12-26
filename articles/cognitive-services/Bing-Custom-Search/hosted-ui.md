---
title: 사이트 검색, 호스티드 UI Bing Custom Search 사용
titlesuffix: Azure Cognitive Services
description: Bing Custom Search 호스티트 UI 구성 방법을 설명합니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: c71597cf540cca67b9558ce28d20ce1d21ae0243
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424989"
---
# <a name="configure-your-hosted-ui-experience"></a>호스트된 UI 환경 구성

사용자 지정 검색 인스턴스를 구성한 후 Custom Search API를 호출하여 검색 결과를 가져오고 앱에 표시할 수 있습니다. 또는 앱이 웹앱인 경우 Custom Search에서 제공하는 호스트된 UI를 사용할 수 있습니다.   

## <a name="configure-custom-hosted-ui"></a>사용자 지정 호스트된 UI 구성

웹앱에 대한 호스트된 UI를 구성하려면 다음 단계를 수행합니다.

1. Custom Search [포털](https://customsearch.ai)에 로그인합니다.  
  
2. Custom Search 인스턴스를 클릭합니다. 인스턴스를 만들려면 [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.  

3. **호스트된 UI** 탭을 클릭합니다.  
  
4. 레이아웃을 선택합니다.
  
  - 검색 표시줄 및 결과(기본값) &mdash; 이 레이아웃은 검색 상자 및 검색 결과를 사용한 기존 검색 페이지입니다.
  - 결과만 &mdash; 이 레이아웃은 검색 결과만 표시합니다. 이 레이아웃은 검색 상자를 표시하지 않습니다. JavaScript 코드 조각 또는 HTML 엔드포인트 링크에서 요청 URL에 쿼리 매개 변수(&q=\<query string>)를 추가하여 검색 쿼리를 제공해야 합니다.
  - 팝오버 &mdash; 이 레이아웃은 검색 상자를 제공하고 슬라이딩 오버레이로 검색 결과를 표시합니다.
      
5. 색 테마를 선택합니다. 가능한 테마는 다음과 같습니다. 
  
  - 클래식
  - 어둡게
  - 하늘색

  각 테마를 클릭하여 사용자의 웹앱에 어떤 테마가 가장 적합한지 확인합니다. 웹앱과 더 효율적으로 통합되도록 색 테마를 미세 조정해야 하는 경우 **사용자 지정 테마**를 클릭합니다. 레이아웃 테마에는 일부 색 구성만 적용됩니다. 색을 변경하려면 해당 텍스트 상자에 색의 RGB HEX 값(예: #366eb8)을 입력합니다. 또는 색 단추를 클릭한 다음, 적합한 음영을 클릭합니다. 
  
  색을 변경한 후 오른쪽의 미리 보기 예제에 변경 내용이 어떻게 적용되는지 확인합니다. 언제든지 **기본값으로 다시 설정**을 클릭하여 선택한 테마에 대한 기본 색으로 다시 돌아갈 수 있습니다.

  > [!NOTE]
  > 색 테마를 변경하면 색을 선택할 때 접근성을 고려합니다.

5. **추가 구성** 아래에서 앱에 적합한 값을 제공합니다. 이러한 설정은 선택 사항입니다. 적용하거나 제거한 경우의 효과를 보려면 오른쪽에 있는 미리 보기 창을 확인합니다. 사용 가능한 구성 옵션은 다음과 같습니다.  
  
  - 웹 검색 구성:
    - 활성화된 웹 결과 &mdash; 웹 검색이 사용하도록 설정되었는지 여부를 결정합니다(페이지의 맨 위에 있는 웹 탭을 참조).
    - 자동 제안 사용 &mdash; 사용자 지정 자동 제안이 사용하도록 설정되었는지 여부를 결정합니다(추가 비용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) 참조).
    - 페이지당 웹 결과 &mdash; 한 번에 표시할 웹 검색 결과의 수입니다(최댓값은 페이지당 50개 결과임).
    - 이미지 캡션 &mdash; 이미지가 검색 결과와 함께 표시되는지 여부를 결정합니다.
  
    **고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  
  
    - 단어 강조 표시 &mdash; 검색어를 굵게 강조하여 결과를 표시할지 여부를 결정합니다. 
    - 링크 대상 &mdash; 사용자가 검색 결과를 클릭할 때 웹 페이지를 새 브라우저 탭(비어 있음) 또는 동일한 브라우저 탭(자체)에서 열지 여부를 결정합니다. 

  - 이미지 검색 구성:
    - 활성화된 이미지 결과 &mdash; 이미지 검색이 사용하도록 설정되었는지 여부를 결정합니다(페이지의 맨 위에 있는 이미지 탭을 참조).   
    - 페이지당 이미지 결과 &mdash; 한 번에 표시할 이미지 검색 결과의 수입니다(최댓값은 페이지당 150개 결과임).  
  
    **고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  
  
    - 필터를 사용하도록 설정 &mdash; Bing이 반환하는 이미지를 필터링하는 데 사용자가 사용할 수 있는 필터를 추가합니다. 예를 들어 사용자는 애니메이션 효과가 적용된 GIF에 대한 결과만 필터링할 수 있습니다.

  - 비디오 검색 구성:
    - 활성화된 비디오 결과 &mdash; 비디오 검색이 사용하도록 설정되었는지 여부를 결정합니다(페이지의 맨 위에 있는 비디오 탭을 참조).  
    - 페이지당 비디오 결과 &mdash; 한 번에 표시할 비디오 검색 결과의 수입니다(최댓값은 페이지당 150개 결과임).
  
    **고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  
  
    - 필터를 사용하도록 설정 &mdash; Bing이 반환하는 비디오를 필터링하는 데 사용자가 사용할 수 있는 필터를 추가합니다. 예를 들어, 사용자가 특정 해상도의 비디오 또는 지난 24시간 동안 검색된 비디오에 대해 결과를 필터링할 수 있습니다.

  - 기타 구성:
    - 페이지 제목 &mdash; 검색 결과 페이지의 제목 영역에 표시되는 텍스트입니다(팝오버 레이아웃용 아님).
    - 도구 모음 테마 &mdash; 검색 결과 페이지에서 제목 영역의 배경색을 결정합니다.  
  
    **고급 구성 표시**를 클릭하면 다음 구성이 표시됩니다.  
  
    - 검색 상자 텍스트 자리 표시자 &mdash; 입력 전에 검색 상자에 표시되는 텍스트입니다.
    - 제목 링크 URL &mdash; 제목 링크의 대상입니다.
    - 로고 URL &mdash; 제목 옆에 표시되는 이미지입니다. 
    - 즐겨찾기 아이콘 URL &mdash; 브라우저 제목 표시줄에 표시되는 아이콘입니다.  

    다음 구성은 HTML 엔드포인트를 통해 호스트된 UI를 사용하는 경우에만 적용됩니다(JavaScript 코드 조각을 사용하는 경우에는 적용되지 않음).
    
    - 페이지 제목
    - 도구 모음 테마
    - 제목 링크 URL
    - 로고 URL
    - 즐겨찾기 아이콘 URL  
  
6. 검색 구독 키를 입력하거나, 드롭다운 목록에서 키를 선택합니다. 드롭다운 목록은 Azure 계정의 구독에 있는 키로 채워집니다. [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 참조하세요.  

7. 자동 제안을 사용하도록 설정한 경우 자동 제안 구독 키를 입력하거나, 드롭다운 목록에서 키를 선택합니다. 드롭다운 목록은 Azure 계정의 구독에 있는 키로 채워집니다. 사용자 지정 자동 제안에는 특정 구독 계층이 필요합니다. [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)을 참조하세요.

> [!NOTE]
> 사용자 지정 호스트된 UI 구성을 변경하면 오른쪽 창에 변경 내용에 대한 시각적 참조가 제공됩니다. 표시된 검색 결과는 인스턴스에 대한 실제 결과가 아닙니다.

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
  > 필요에 따라 URL에 다음 쿼리 매개 변수를 추가합니다. 이러한 매개 변수에 대한 자세한 내용은 [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) 참조를 확인하세요.
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > 페이지에 개인정보처리방침이나 기타 고지 사항 및 사용 약관을 표시할 수 없습니다. 사용 적합성은 각기 다를 수 있습니다.  

사용자 지정 구성 ID를 포함하여 추가 정보를 보려면 **프로덕션** 탭 아래의 **엔드포인트**로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)
