---
title: 구 목록-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 인식 결과를 개선 하기 위해 `PhraseListGrammar` 개체를 사용 하 여 문구 목록에 음성 서비스를 제공 하는 방법을 알아봅니다.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 61d3e4d2de6b8707ee7433815f8002e5d5e3e3d6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464533"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성 텍스트에 대 한 구 목록

음성 서비스에 구 목록을 제공 하 여 음성 인식의 정확도를 향상 시킬 수 있습니다. 구 목록은 사람의 이름 또는 특정 위치와 같은 오디오 데이터에서 알려진 문구를 식별 하는 데 사용 됩니다.

예를 들어 "이동" 명령과 음성 될 수 있는 "말"의 가능한 대상이 있는 경우 "앞으로 이동" 항목을 추가할 수 있습니다. 문구를 추가 하면 오디오가 인식 될 때 "이동"이 아니라 "이동" 하는 것으로 인식 될 확률이 높아집니다.

단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 정확히 일치 하는 항목이 오디오에 포함 되는 경우 문구 목록의 항목이 사용 됩니다. 이전 예제를 기반으로 하는 구문 목록에 "문서에 이동"이 포함 되어 있고 오디오가 캡처된 소리를 "이동" 하 고 "이동" 하는 것과 비슷한 방식으로 표시 되는 경우 인식 결과는 "느리게 이동"으로 인식 될 가능성이 높습니다.

>[!Note]
> 현재 문구 목록은 음성 텍스트에 대 한 영어만 지원 합니다.

## <a name="how-to-use-phrase-lists"></a>구 목록을 사용 하는 방법

아래 샘플은 `PhraseListGrammar` 개체를 사용 하 여 구 목록을 작성 하는 방법을 보여 줍니다.

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
> 음성 서비스에서 음성을 일치 시키는 데 사용할 수 있는 최대 문구 목록은 1024 구입니다.

Clear ()를 호출 하 여 `PhraseListGrammar` 관련 된 구를 지울 수도 있습니다.

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
> `PhraseListGrammar` 개체에 대 한 변경 내용은 다음 인식 또는 음성 서비스에 다시 연결 된 후에 적용 됩니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
