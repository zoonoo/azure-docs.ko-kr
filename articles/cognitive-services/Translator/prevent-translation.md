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
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326767"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API를 사용하여 콘텐츠 번역을 방지하는 방법

콘텐츠가 번역되지 않도록 Translator Text API를 사용하여 콘텐츠에 태그를 지정할 수 있습니다. 예를 들어 번역하면 끝이 통하지 않는 코드, 브랜드 이름, 단어/문구 등에 태그를 지정할 수 있습니다.

## <a name="methods-for-preventing-translation"></a>번역을 방지하는 메서드
1. Twitter 태그 @somethingtopassthrough 또는 #somethingtopassthrough를 이스케이프합니다. 번역 후 이스케이프를 취소합니다. 유효한 twitter 태그에 대 한 정규식입니다. `\B@[A-Za-z]+[A-Za-z0-9_]+)`. 태그는 "@" 기호, 문자, 문자, 숫자 또는 밑줄 하나 이상으로 시작 해야 합니다. 태그를 짧게 유지 하 고 여는 태그 앞에 공백이와 야 하는 것이 좋습니다.

2. 콘텐츠에 `notranslate`를 태그로 지정합니다. 이는 입력 textType이 HTML로 설정 된 경우에만 작동 하도록 설계 된 것입니다.

   예제:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. [동적 사전](dynamic-dictionary.md)을 사용하여 특정 번역을 규정합니다.

4. 문자열을 Translator Text API로 보내 번역하지 마세요.

5. 사용자 지정 변환기: [사용자 지정 변환기에서 사전을](custom-translator/what-is-dictionary.md) 사용 하 여 100% 확률의 구에 대 한 변환을 규정 합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Translator API 호출에서 번역 방지](reference/v3-0-translate.md)
