---
title: 콘텐츠 변환 방지-변환기
titleSuffix: Azure Cognitive Services
description: 변환기를 사용 하 여 콘텐츠를 변환 하지 않습니다. 변환기를 사용 하면 번역 되지 않도록 콘텐츠에 태그를 지정할 수 있습니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 33939976a0824ce8afeb2e6f6fb19e7033098683
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592698"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>변환기를 사용 하 여 콘텐츠 번역을 방지 하는 방법

변환기를 사용 하면 번역 되지 않도록 콘텐츠에 태그를 지정할 수 있습니다. 예를 들어 번역하면 끝이 통하지 않는 코드, 브랜드 이름, 단어/문구 등에 태그를 지정할 수 있습니다.

## <a name="methods-for-preventing-translation"></a>번역을 방지하는 메서드

1. 콘텐츠에 `notranslate`를 태그로 지정합니다. 이는 입력 textType이 HTML로 설정 된 경우에만 작동 하도록 설계 된 것입니다.

   예제:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. [동적 사전](dynamic-dictionary.md)을 사용하여 특정 번역을 규정합니다.

3. 번역을 위해 문자열을 변환기에 전달 하지 마세요.

4. 사용자 지정 변환기: [사용자 지정 변환기에서 사전을](custom-translator/what-is-dictionary.md) 사용 하 여 100% 확률의 구에 대 한 변환을 규정 합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [변환기 호출에서 번역 방지](reference/v3-0-translate.md)
