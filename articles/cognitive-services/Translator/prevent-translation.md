---
title: 콘텐츠 번역 방지 - Translator
titleSuffix: Azure Cognitive Services
description: Translator를 사용하여 콘텐츠 번역을 방지합니다. Translator를 사용하면 번역되지 않도록 콘텐츠에 태그를 지정할 수 있습니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897479"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Translator를 사용하여 콘텐츠가 번역되지 않도록 하는 방법

Translator를 사용하면 번역되지 않도록 콘텐츠에 태그를 지정할 수 있습니다. 예를 들어 지역화하면 의미가 맞지 않게 되는 코드, 브랜드 이름 또는 단어/구에 태그를 지정할 수 있습니다.

## <a name="methods-for-preventing-translation"></a>번역을 방지하는 메서드

1. 콘텐츠에 `notranslate`를 태그로 지정합니다. 입력 textType이 HTML로 설정된 경우에만 작동하도록 설계되었습니다.

   예:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. 콘텐츠에 `translate="no"`를 태그로 지정합니다. 입력 textType이 HTML로 설정된 경우에만 작동합니다.

   예:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. [동적 사전](dynamic-dictionary.md)을 사용하여 특정 번역을 규정합니다.

4. 번역을 위해 Translator에 문자열을 전달하지 마세요.

5. Custom Translator: [Custom Translator의 사전](custom-translator/what-is-dictionary.md)을 사용하여 확률 100%인 구의 번역을 규정합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [번역 작업을 사용하여 텍스트 번역](reference/v3-0-translate.md)
