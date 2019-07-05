---
title: 장식 표식을 사용하여 텍스트 강조 표시 - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: 검색 응답에서 텍스트 장식을 사용하도록 설정하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 42b30b14e561fd3851a41701d2ecb8d98d5a02ed
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445562"
---
# <a name="using-decoration-markers-to-highlight-text"></a>장식 표식을 사용하여 텍스트를 강조 표시

Bing은 일부 응답의 표시 문자열에 쿼리 용어(또는 Bing이 찾은 다른 관련 용어)를 표시하는 적중 항목 강조 표시를 지원합니다. 예를 들어 웹 페이지의 `name`, `displayUrl` 및 `snippet` 필드에 쿼리 용어가 표시될 수 있습니다.

기본적으로 Bing은 표시 문자열에 강조 표시 마커를 포함하지 않습니다. 마커를 포함하려면 요청에 `textDecorations` 쿼리 매개 변수를 포함하고 **true**로 설정합니다. Bing은 용어의 시작과 끝을 표시하는 E000 및 E001 유니코드 문자를 사용하여 쿼리 용어를 표시합니다. 예를 들어 쿼리 용어가 Sailing Dinghy이고 두 용어 중 하나가 필드에 있는 경우 다음 예제와 같이 해당 용어가 적중 항목 강조 문자로 묶입니다.  
  
![적중 항목 강조 표시](./media/bing-hit-highlighting.PNG) 

사용자 인터페이스에 문자열을 표시하기 전에 유니코드 문자를 표시 형식에 적합한 문자로 바꿉니다. 예를 들어 텍스트를 HTML로 표시하는 경우 E000을 <b\>, E001을 </b\>로 바꿔서 쿼리 용어를 강조 표시할 수 있습니다. 형식을 적용하지 않으려면 문자열에서 마커를 제거합니다. 

Bing은 유니코드 문자 또는 HTML 태그를 마커로 사용하는 옵션을 제공합니다. 사용할 마커를 지정하려면 `textFormat` 쿼리 매개 변수를 포함합니다. 유니코드 문자를 사용하여 콘텐츠를 표시하려면 `textFormat`을 Raw(기본값)로 설정하고, HTML 태그를 사용하여 콘텐츠를 표시하려면 `textFormat`을 HTML로 설정합니다. 
  
`textDecorations`이 **true**이면 Bing이 답변의 표시 문자열에 다음 마커를 포함할 수 있습니다. HTML에 해당하는 요소가 없으면 HTML 테이블 셀이 비어 있습니다.

|유니코드|HTML|설명
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

다음 예제는 log(2) 쿼리 용어에 대한 아래 첨자 마커가 포함된 `Computation` 답변을 표시합니다. `expression` 필드는 `textDecoration`이 **true**인 경우에만 마커를 포함합니다.

![계산 마커](./media/bing-markers-computation.PNG) 

요청에서 장식을 요청하지 않으면 식은 log10(2)입니다. 
  
