---
title: 구 목록-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 인식 결과를 개선 하기 위해 `PhraseListGrammar` 개체를 사용 하 여 문구 목록과 함께 음성 서비스를 제공 하는 방법을 알아봅니다.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2ceb53b50810aef501278710ae990c57fc45030c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971675"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성-텍스트에 대한 구문 목록

음성 서비스에 구 목록을 제공 하 여 음성 인식의 정확도를 향상 시킬 수 있습니다. 구문 목록은 오디오 데이터에서 개인의 이름 또는 특정 위치와 같은 알려진 구문을 식별하는 데 사용됩니다.

예를 들어, "Move to" 명령과 말할 수 있는 가능한 "Ward"라는 목적지가 있는 경우, "Move to Ward" 항목을 추가할 수 있습니다. 구문을 추가하면 오디오가 "Move toward" 대신 "Move to Ward"를 인식할 확률이 증가하게 됩니다.

단일 단어 또는 전체 구문을 구문 목록에 추가할 수 있습니다. 인식 하는 동안 전체 구와 정확히 일치 하는 구가 오디오에 별도의 구로 포함 되는 경우 구 목록의 항목이 사용 됩니다. 구와 정확히 일치 하는 항목을 찾을 수 없는 경우 인식이 지원 되지 않습니다.

>[!Note]
> 현재 문구 목록은 음성 텍스트에 대 한 영어만 지원 합니다.

## <a name="how-to-use-phrase-lists"></a>구문 목록을 사용하는 방법

아래 예제는 `PhraseListGrammar` 개체를 사용하여 구문 목록을 작성하는 방법을 설명합니다.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 음성 서비스에서 음성을 일치 시키는 데 사용할 수 있는 최대 문구 목록은 1024 구입니다.

clear()를 호출하여 `PhraseListGrammar`과 연결된 구문을 지울 수도 있습니다.

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> `PhraseListGrammar` 개체에 대 한 변경 내용은 다음 인식에 적용 되거나 음성 서비스에 다시 연결 됩니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
