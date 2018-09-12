---
title: 'Bing Custom Search: 사이트 검색 | Microsoft Docs'
description: 호스트된 UI를 구성하는 방법을 설명합니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7f2b97479ffcdb7ec8b3a1a635562d1fe68c3269
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158424"
---
# <a name="configure-your-hosted-ui-experience"></a>호스트된 UI 환경 구성
사용자 지정 검색 인스턴스를 구성한 후 Custom Search API를 호출하여 검색 결과를 가져오고 앱에 표시할 수 있습니다. 또는 앱이 웹앱인 경우 Custom Search에서 제공하는 호스트된 UI를 사용할 수 있습니다.   

## <a name="configure-custom-hosted-ui"></a>사용자 지정 호스트된 UI 구성
웹앱에 포함할 호스트된 UI를 구성하려면 다음 지침을 따르세요.
1.  Custom Search [포털](https://customsearch.ai)에 로그인합니다.
2.  Custom Search 인스턴스를 클릭합니다. 인스턴스를 만들려면 [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.
3.  **호스트된 UI** 탭을 클릭합니다.
4.  레이아웃을 선택합니다.
    - 검색 창과 결과(기본값) &mdash; 검색 상자와 검색 결과를 표시합니다.
    - 결과만 &mdash; 검색 상자를 표시하지 않고 결과만 표시합니다.
    - 팝오버 &mdash; 검색 상자를 표시하지 않고, 슬라이딩 오버레이에 결과만 표시합니다.
    
   > [!IMPORTANT]
   > **결과만** 레이아웃을 선택하는 경우 customConfig 쿼리 매개 변수를 포함해야 합니다. [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)를 참조하세요.

5.  **추가 구성** 아래에서 앱에 적합한 값을 제공합니다. 이러한 설정은 선택 사항입니다. 적용하거나 제거한 경우의 효과를 보려면 오른쪽에 있는 미리 보기 창을 확인합니다.  사용 가능한 구성 옵션은 다음과 같습니다.
    - 웹 검색 구성:
        - 웹 결과 사용 &mdash; 웹 검색 결과를 반환할지 여부를 결정합니다.
        - 자동 제안 사용 &mdash; 사용자 지정 자동 제안을 사용하도록 설정할지 여부를 결정합니다.
        - 페이지당 웹 결과 수 &mdash; 한 번에 표시할 웹 검색 결과 수입니다.
        - 이미지 캡션 &mdash; 검색 결과와 함께 이미지를 표시할지 여부를 결정합니다.
        - 단어 강조 표시 &mdash; 검색어를 굵게 강조하여 결과를 표시할지 여부를 결정합니다.
    - 이미지 검색 구성:
        - 이미지 결과 사용 &mdash; 이미지 검색 결과를 반환할지 여부를 결정합니다.
    - 기타 구성:
        - 페이지 제목 &mdash; 페이지 제목 영역에 표시되는 텍스트입니다.
        - 도구 모음 테마 &mdash; 페이지 제목 영역의 배경색을 결정합니다.
        - 검색 상자 텍스트 자리 표시자 &mdash; 입력 전에 검색 상자에 표시되는 텍스트입니다.
        - 제목 링크 URL &mdash; 제목 링크의 대상입니다.
        - 로고 URL &mdash; 제목 옆에 표시되는 이미지입니다. 
        - Favicon URL &mdash; 브라우저 제목 표시줄에 표시되는 아이콘입니다.

   > [!IMPORTANT]
   > 최소한 이미지 검색 또는 웹 검색 중 하나는 사용하도록 설정해야 합니다.

6.  검색 구독 키를 입력하거나, 드롭다운에서 키를 선택합니다. 드롭다운에는 계정의 Azure 구독 키가 채워져 있습니다. [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 참조하세요.
7.  자동 제안을 사용하도록 설정한 경우 자동 제안 구독 키를 입력하거나, 드롭다운에서 키를 선택합니다. 드롭다운에는 계정의 Azure 구독 키가 채워져 있습니다. Custom Autosuggest에는 특정 구독 계층이 필요합니다. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)를 참조하세요.

> [!NOTE]
> 사용자 지정 호스트된 UI 구성을 변경하면 오른쪽 창에 변경 내용에 대한 시각적 참조가 제공됩니다. 표시된 검색 결과는 인스턴스에 대한 실제 결과가 아닙니다.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>사용자 지정 UI 사용
호스트된 UI를 사용하려면 다음 중 하나를 수행합니다. 

- 웹 페이지에 스크립트 포함
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- 제공된 URL `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID` 사용

  > [!IMPORTANT]
  > 페이지에 개인정보처리방침이나 기타 고지 사항 및 사용 약관을 표시할 수 없습니다. 사용 적합성은 각기 다를 수 있습니다.  

사용자 지정 구성 ID를 포함하여 추가 정보를 보려면 **프로덕션** 탭 아래의 **엔드포인트**로 이동합니다.

## <a name="next-steps"></a>다음 단계
- [장식 표식을 사용하여 텍스트 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)