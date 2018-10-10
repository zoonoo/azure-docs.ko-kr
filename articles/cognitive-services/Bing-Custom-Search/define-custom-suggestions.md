---
title: 'Bing Custom Search: Custom Autosuggest 제안 정의 | Microsoft Docs'
description: 사용자 지정 제안으로 Custom Autosuggest를 구성하는 방법을 설명합니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 11d3b1c2d98caa8d6527c52bec1cc65ba22c6c3b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958750"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Custom Autosuggest 환경 구성

Custom Autosuggest는 검색 환경과 관련된 검색 쿼리 제안 문자열 목록을 반환합니다. 제안된 쿼리 문자열은 사용자가 검색 상자에 제공하는 부분 쿼리 문자열을 기반으로 합니다. 목록에는 최대 10개의 제안이 포함됩니다. 

사용자 지정 제안만 반환할지 또는 Bing 제안도 포함할지를 지정합니다. Bing 제안을 포함하는 경우 사용자 지정 제안이 Bing 제안보다 먼저 표시됩니다. 관련 제안을 충분히 제공하면, 반환된 제안 목록에 Bing 제안이 포함되지 않을 수도 있습니다. Bing 제안은 항상 Custom Search 인스턴스의 컨텍스트에 있습니다. 

인스턴스에 대한 검색 쿼리 제안을 구성하려면 **Autosuggest** 탭을 클릭합니다.  

> [!NOTE]
> 이 기능을 사용하려면 적절한 수준([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) 참조)에서 Custom Search를 구독해야 합니다.

제안이 서비스 엔드포인트(API 또는 호스트된 UI)에 반영되는 데 최대 24시간이 소요될 수 있습니다.

## <a name="enable-bing-suggestions"></a>Bing 제안 사용

Bing 제안을 사용하려면 **자동 Bing 제안** 슬라이더를 켬 위치로 전환합니다. 슬라이더가 파란색이 됩니다.

## <a name="add-your-own-suggestions"></a>자신의 제안 추가

나만의 쿼리 문자열 제안을 추가하려면 **사용자 정의 제안** 아래 목록에 해당 항목을 추가합니다. 목록에 제안을 추가한 다음, Enter 키를 누르거나 **+** 아이콘을 클릭합니다. 제안은 어떠한 언어로든 지정할 수 있습니다. 쿼리 문자열 제안은 최대 5,000개까지 추가할 수 있습니다.

## <a name="upload-suggestions"></a>제안 업로드

필요에 따라, 파일에서 제안 목록을 업로드할 수 있습니다. 파일에는 한 줄에 하나씩 검색 쿼리 문자열이 있어야 합니다. 파일을 업로드하려면 업로드 아이콘을 클릭하고 업로드할 파일을 선택합니다. 서비스가 파일에서 제안 사항을 추출하여 목록에 추가합니다.

## <a name="remove-suggestions"></a>제안 제거

쿼리 문자열 제안을 제거하려면 제거할 제안 옆에 있는 제거 아이콘을 클릭합니다.

## <a name="block-suggestions"></a>제안 차단

Bing 제안을 포함시키면 Bing에서 반환하지 않을 검색 쿼리 문자열 목록을 추가할 수 있습니다. 차단된 쿼리 문자열을 추가하려면 **Show blocked suggestions**(차단된 제안 표시)를 클릭합니다. 쿼리 문자열을 목록에 추가하고 Enter 키를 누르거나 **+** 아이콘을 클릭합니다. 차단된 쿼리 문자열은 최대 50개까지 추가할 수 있습니다.



[!INCLUDE[publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Custom Autosuggest 구성 변경 내용을 적용하는 데 최대 24시간이 걸릴 수 있습니다.


## <a name="enabling-autosuggest-in-hosted-ui"></a>호스트된 UI에서 Autosuggest를 사용하도록 설정

호스트된 UI에 쿼리 문자열 제안을 사용하도록 설정하려면 **Hosted UI**(호스트된 UI)를 클릭합니다. **추가 구성** 섹션까지 아래로 스크롤합니다. **웹 검색**에서 **Enable autosuggest**(자동 제안 사용)에 대해 **On**(켬)을 선택합니다. Autosuggest를 사용하도록 설정하려면 검색 상자가 포함된 레이아웃을 선택해야 합니다.


## <a name="calling-the-autosuggest-api"></a>Autosuggest API 호출

Bing Custom Search API를 사용하여 제안된 쿼리 문자열을 가져오려면 `GET` 요청을 다음 엔드포인트에 전송합니다.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

응답에는 제안된 쿼리 문자열이 포함된 `SearchAction` 개체 목록이 포함됩니다.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

각 제안에는 `displayText` 및 `query` 필드가 포함됩니다. `displayText` 필드에는 검색 상자의 드롭다운 목록을 채우는 데 사용하는 제안된 쿼리 문자열이 포함됩니다.

사용자가 드롭다운 목록에서 제안된 쿼리 문자열을 선택하는 경우 [Bing Custom Search API](overview.md)를 호출할 때 `query` 필드의 쿼리 문자열을 사용합니다.


## <a name="next-steps"></a>다음 단계

- [사용자 지정 제안 가져오기](./get-custom-suggestions.md)
- [사용자 지정 인스턴스 검색](./search-your-custom-view.md)
- [사용자 지정 호스트된 UI 구성 및 사용](./hosted-ui.md)