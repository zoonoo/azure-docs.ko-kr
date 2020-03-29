---
title: 콘텐츠 번역 방지 - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API를 사용하여 콘텐츠 번역을 방지합니다. 콘텐츠가 번역되지 않도록 Translator Text API를 사용하여 콘텐츠에 태그를 지정할 수 있습니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052480"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API를 사용하여 콘텐츠 번역을 방지하는 방법

콘텐츠가 번역되지 않도록 Translator Text API를 사용하여 콘텐츠에 태그를 지정할 수 있습니다. 예를 들어 번역하면 끝이 통하지 않는 코드, 브랜드 이름, 단어/문구 등에 태그를 지정할 수 있습니다.

## <a name="methods-for-preventing-translation"></a>번역을 방지하는 메서드

1. 콘텐츠에 `notranslate`를 태그로 지정합니다. 입력 텍스트Type이 HTML로 설정된 경우에만 작동하도록 설계되어 있습니다.

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

3. 문자열을 Translator Text API로 보내 번역하지 마세요.

4. 사용자 지정 번역기: [사용자 지정 번역기에서 사전을](custom-translator/what-is-dictionary.md) 사용하여 100% 확률로 구의 번역을 규정합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Translator API 호출에서 번역 방지](reference/v3-0-translate.md)
