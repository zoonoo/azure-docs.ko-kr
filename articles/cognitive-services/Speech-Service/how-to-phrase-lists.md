---
title: 구 목록 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: '`PhraseListGrammar` 개체를 사용하여 음성-텍스트 인식 결과를 개선하는 구 목록으로 음성 서비스를 제공하는 방법을 알아봅니다.'
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560850"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성-텍스트에 대한 구 목록

음성 서비스에 구문 목록을 제공하여 음성 인식의 정확도를 향상시킬 수 있습니다. 구 목록은 사용자의 이름이나 특정 위치와 같은 오디오 데이터에서 알려진 구를 식별하는 데 사용됩니다.

예를 들어,로 이동"이라는 명령과 "와드로 이동"이라는 명령이 있는 경우 "와드로 이동"의 항목을 추가할 수 있습니다. 구를 추가하면 오디오가 인식될 때 "앞으로 이동"이 아니라 "와드로 이동"이 인식될 확률이 높아집니다.

단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 하는 동안 전체 구에 대 한 정확한 일치 가 별도 구로 오디오에 포함 된 경우 구 목록의 항목이 사용 됩니다. 구와 정확히 일치하는 단어를 찾을 수 없는 경우 인식은 지원되지 않습니다.

>[!Note]
> 현재 구 목록은 음성-텍스트에 대한 영어만 지원합니다.

## <a name="how-to-use-phrase-lists"></a>구 목록 사용 방법

아래 샘플에서는 `PhraseListGrammar` 개체를 사용하여 구 목록을 작성하는 방법을 보여 줍니다.

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
> Speech 서비스가 음성과 일치하는 데 사용할 구 목록의 최대 수는 1024개입니다.

clear()를 `PhraseListGrammar` 호출하여 와 연관된 구를 지울 수도 있습니다.

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
> 개체에 `PhraseListGrammar` 대한 변경 사항은 다음 인식 또는 음성 서비스에 대한 다시 연결에 적용됩니다.

## <a name="next-steps"></a>다음 단계

* [음성 SDK 참조 문서](speech-sdk.md)
