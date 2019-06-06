---
title: 문자 수 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API에서 문자 수를 계산하는 방법
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: eccd0c47c1bfc071f43a6329406cd14f704d019e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514296"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Translator Text API에서 문자 수를 계산하는 방법

Translator Text API는 입력된 텍스트의 모든 유니코드 코드 포인트를 문자로 계산합니다. 여러 언어로 번역되는 단일 API 호출에서 요청이 이루어진 경우라도 언어로의 각 텍스트 번역은 별도의 번역으로 계산됩니다. 응답 길이는 중요하지 않습니다.

중요한 항목은 다음과 같습니다.

* 요청 본문에서 Translator Text API로 전달되는 텍스트
   * `Text`: 번역, 변환 및 사전 조회 메서드를 사용하는 경우
   * `Text` 및 `Translation`: 사전 예제 메서드를 사용하는 경우
* 모두 태그: 요청 본문의 텍스트 필드 내 HTML, XML 태그 등. 요청을 빌드하는 데 사용되는 JSON 표기법(예: “텍스트:”)은 계산되지 않습니다.
* 개별 문자
* 문장 부호
* 공백, 탭, 태그 및 모든 종류의 공백 문자
* 유니코드에 정의된 모든 코드 포인트
* 이전에 동일한 텍스트를 번역한 경우라도 반복되는 번역

중국어 및 일본어 간지와 같은 표의 문자를 기반으로 하는 스크립트의 경우 Translator Text API는 유니코드 코드 포인트의 수를 표의 문자당 한 자로 계산합니다. 예외: 유니코드 서로게이트는 두 문자로 계산됩니다.

요청, 단어, 바이트 또는 문장의 수는 문자 수와 관련이 없습니다.

Detect 및 BreakSentence 메서드 호출은 문자 사용에 계산되지 않습니다. 그러나 Detect 및 BreakSentence 메서드 호출은 개수에 포함되는 다른 함수의 사용과 적절한 비율을 유지해야 합니다. 사용자가 호출한 Detect 또는 BreakSentence 수가 다른 메서드의 수를 100배 이상 초과하는 경우 Microsoft는 사용자의 Detect 및 BreakSentence 메서드의 사용을 제한할 권리가 있습니다.


문자 수에 대한 자세한 내용은 [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)를 참조하세요.
