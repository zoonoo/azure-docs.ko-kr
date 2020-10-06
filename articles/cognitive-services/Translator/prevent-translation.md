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
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742063"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>변환기를 사용 하 여 콘텐츠 번역을 방지 하는 방법

변환기를 사용 하면 번역 되지 않도록 콘텐츠에 태그를 지정할 수 있습니다. 예를 들어 지역화하면 의미가 맞지 않게 되는 코드, 브랜드 이름 또는 단어/구에 태그를 지정할 수 있습니다.

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

2. 콘텐츠에 `translate="no"`를 태그로 지정합니다. 입력 textType이 HTML로 설정 된 경우에만 작동 합니다.

   예제:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
