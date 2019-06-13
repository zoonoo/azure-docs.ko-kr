---
title: 콘텐츠 번역 방지 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API를 사용하여 콘텐츠 번역을 방지합니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: 32e5ddecc336175443fc5e8743b19b480de71c6b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515252"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API를 사용하여 콘텐츠 번역을 방지하는 방법

콘텐츠가 번역되지 않도록 Translator Text API를 사용하여 콘텐츠에 태그를 지정할 수 있습니다. 예를 들어 번역하면 끝이 통하지 않는 코드, 브랜드 이름, 단어/문구 등에 태그를 지정할 수 있습니다.

## <a name="methods-for-preventing-translation"></a>번역을 방지하는 메서드
1. Twitter 태그 @somethingtopassthrough 또는 #somethingtopassthrough를 이스케이프합니다. 번역 후 이스케이프를 취소합니다.

2. 콘텐츠에 `notranslate`를 태그로 지정합니다.

   예제:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. [동적 사전](dynamic-dictionary.md)을 사용하여 특정 번역을 규정합니다.

4. 문자열을 Translator Text API로 보내 번역하지 마세요.

5. Custom Translator: [Custom Translator의 사전](custom-translator/what-is-dictionary.md)을 사용하여 확률 100%인 구의 번역을 규정합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Translator API 호출에서 번역 방지](reference/v3-0-translate.md)
