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
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: d73a63a8f58e14149121d0860268fc23930001bf
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226358"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성-텍스트에 대한 구문 목록

구문 목록을 사용하여 음성 서비스를 제공하여 음성 인식의 정확도를 높일 수 있습니다. 구문 목록은 오디오 데이터에서 개인의 이름 또는 특정 위치와 같은 알려진 구문을 식별하는 데 사용됩니다.

예를 들어 "이동" 명령과 음성 될 수 있는 "말"의 가능한 대상이 있는 경우 "앞으로 이동" 항목을 추가할 수 있습니다. 문구를 추가 하면 오디오가 인식 될 때 "이동"이 아니라 "이동" 하는 것으로 인식 될 확률이 높아집니다.

단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 정확히 일치 하는 항목이 오디오에 포함 되는 경우 문구 목록의 항목이 사용 됩니다. 이전 예제를 기반으로 하는 구문 목록에 "이동"이 포함 되 고 캡처된 구가 "천천히 이동" 하는 경우 인식 결과는 "느리게 이동" 됩니다.

>[!Note]
> 현재 문구 목록은 음성 텍스트에 대 한 영어만 지원 합니다.

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
