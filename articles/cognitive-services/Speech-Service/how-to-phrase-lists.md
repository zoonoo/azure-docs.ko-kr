---
title: 구문 목록-음성 서비스
titlesuffix: Azure Cognitive Services
description: 음성-텍스트 인식 결과를 개선하기 위해 `PhraseListGrammar` 개체를 사용하여 구문 목록으로 음성 서비스를 제공하는 방법을 알아봅니다.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: a3be5d28ebe394771a2d8b492f1f6a9c8a82fb9e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515317"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성-텍스트에 대한 구문 목록

구문 목록을 사용하여 음성 서비스를 제공하여 음성 인식의 정확도를 높일 수 있습니다. 구문 목록은 오디오 데이터에서 개인의 이름 또는 특정 위치와 같은 알려진 구문을 식별하는 데 사용됩니다.

예를 들어, "Move to" 명령과 말할 수 있는 가능한 "Ward"라는 목적지가 있는 경우, "Move to Ward" 항목을 추가할 수 있습니다. 구문을 추가하면 오디오가 "Move toward" 대신 "Move to Ward"를 인식할 확률이 증가하게 됩니다.

단일 단어 또는 전체 구문을 구문 목록에 추가할 수 있습니다. 인식하는 동안 구문 목록의 항목은 오디오에서 정확한 일치가 포함되어 있으면 사용됩니다. 이전 예제를 좀 더 발전시키자면, 구문 목록에 "Move to Ward"가 포함되어 있고 "Move toward slowly" 구문이 캡처된 경우, 인식 결과는 "Move to Ward slowly"가 될 것입니다.

## <a name="how-to-use-phrase-lists"></a>구문 목록을 사용하는 방법

아래 예제는 `PhraseListGrammar` 개체를 사용하여 구문 목록을 작성하는 방법을 설명합니다.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> 음성 서비스가 음성 일치에 사용할 수 있는 구문 목록의 최대 수는 1024개 구문입니다.

clear()를 호출하여 `PhraseListGrammar`과 연결된 구문을 지울 수도 있습니다.

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar`개체의 변경은 다음 인식 또는 음성 서비스로의 다음 재연결에 영향을 미칩니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
