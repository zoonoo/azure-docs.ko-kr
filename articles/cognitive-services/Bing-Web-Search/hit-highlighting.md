---
title: 장식 표식을 사용하여 텍스트를 강조 표시하는 방법 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API를 사용하여 텍스트 장식을 사용하여 검색 결과에서 강조 표시를 적중하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854046"
---
# <a name="using-decoration-markers-to-highlight-text"></a>장식 표식을 사용하여 텍스트를 강조 표시

Bing은 일부 답변의 표시 문자열에서 쿼리 용어(또는 Bing이 관련성이 있다고 판단하는 다른 용어)를 표시하는 히트 강조 표시를 지원합니다. 예를 들어 웹 페이지 결과의 `name` `displayUrl`및 `snippet` 필드에 표시된 쿼리 용어가 포함될 수 있습니다. 

기본적으로 Bing은 표시 문자열에 강조 표시 마커를 포함하지 않습니다. 마커를 사용하려면 요청에 `textDecorations` 쿼리 매개 변수를 `true`포함하고 로 설정합니다.

## <a name="hit-highlighting-example"></a>히트 하이라이트 예제

다음 예제에서는 에 대한 `Sailing Dinghy`웹 결과를 보여 주며 있습니다. Bing은 E000 및 E001 유니코드 문자를 사용하여 쿼리 용어의 시작과 끝을 표시했습니다.
  
![적중 항목 강조 표시](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

사용자 인터페이스에 결과를 표시하기 전에 유니코드 문자를 표시 형식에 적합한 문자로 바꿉니다.

## <a name="marker-formatting"></a>마커 서식 지정

Bing은 유니코드 문자 또는 HTML 태그를 마커로 사용하는 옵션을 제공합니다. 사용할 마커를 지정하려면 [textFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 쿼리 매개 변수를 포함합니다. 

| 값             | 표식                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | 유니코드 문자(기본값) |
| `textFormat=HTML` | HTML 문자              |

## <a name="additional-text-decorations"></a>추가 텍스트 장식

Bing은 여러 가지 텍스트 장식을 반환할 수 있습니다. 예를 들어 `Computation` 응답필드에 쿼리 용어에 `log(2)` 대한 하위 스크립트 `expression` 마커가 포함될 수 있습니다.

![계산 마커](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

요청이 장식을 지정하지 않은 `expression` 경우 `log10(2)`필드에 는 .가 포함됩니다. 

있는 `textDecorations` `true`경우 Bing은 응답의 표시 문자열에 다음 마커를 포함할 수 있습니다. 동등한 HTML 태그가 없는 경우 테이블 셀이 비어 있습니다.

|유니코드(Unicode)|HTML|설명
|-|-|-
|U+E000|\<b>|쿼리 용어의 시작을 표시(적중 항목 강조 표시)
|U+E001|\</b>|쿼리 용어의 끝을 표시
|U+E002|\<i>|기울임꼴로 표시된 콘텐츠의 시작을 표시 
|U+E003|\</i>|기울임꼴로 표시된 콘텐츠의 끝을 표시
|U+E004|\<br/>|줄 바꿈을 표시
|U+E005||전화 번호의 시작을 표시
|U+E006||전화 번호의 끝을 표시
|U+E007||주소의 시작을 표시
|U+E008||주소의 끝을 표시
|U+E009|\&nbsp;|줄 바꿈하지 않는 공백을 표시
|U+E00C|\<strong>|굵게 표시된 콘텐츠의 시작을 표시
|U+E00D|\</strong>|굵게 표시된 콘텐츠의 끝을 표시
|U+E00E||배경이 주변 배경보다 밝아야 하는 콘텐츠의 시작을 표시
|U+E00F||배경이 주변 배경보다 밝아야 하는 콘텐츠의 끝을 표시
|U+E010||배경이 주변 배경보다 어두워야 하는 콘텐츠의 시작을 표시
|U+E011||배경이 주변 배경보다 어두워야 하는 콘텐츠의 끝을 표시
|U+E012|\<del>|취소선을 그어야 하는 콘텐츠의 시작을 표시
|U+E013|\</del>|취소선을 그어야 하는 콘텐츠의 끝을 표시
|U+E016|\<sub>|아래 첨자 콘텐츠의 시작을 표시
|U+E017|\</sub>|아래 첨자 콘텐츠의 끝을 표시
|U+E018|\<sup>|위 첨자 콘텐츠의 시작을 표시
|U+E019|\</sup>|위 첨자 콘텐츠의 끝을 표시

## <a name="next-steps"></a>다음 단계

* [Bing Web Search API란?](overview.md) 
* [크기 조정 및 자르기 썸네일](resize-and-crop-thumbnails.md)