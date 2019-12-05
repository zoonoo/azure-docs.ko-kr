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
ms.openlocfilehash: 052e02ef562da0637b6b5b9683120f0c397dbfd5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805878"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성-텍스트에 대한 구문 목록

음성 서비스에 구 목록을 제공 하 여 음성 인식의 정확도를 향상 시킬 수 있습니다. 구문 목록은 오디오 데이터에서 개인의 이름 또는 특정 위치와 같은 알려진 구문을 식별하는 데 사용됩니다.

예를 들어, "Move to" 명령과 말할 수 있는 가능한 "Ward"라는 목적지가 있는 경우, "Move to Ward" 항목을 추가할 수 있습니다. 구문을 추가하면 오디오가 "Move toward" 대신 "Move to Ward"를 인식할 확률이 증가하게 됩니다.

단일 단어 또는 전체 구문을 구문 목록에 추가할 수 있습니다. 인식하는 동안 구문 목록의 항목은 오디오에서 정확한 일치가 포함되어 있으면 사용됩니다. 이전 예제를 기반으로 하는 구문 목록에 "문서에 이동"이 포함 되어 있고 오디오가 캡처된 소리를 "이동" 하 고 "이동" 하는 것과 비슷한 방식으로 표시 되는 경우 인식 결과는 "느리게 이동"으로 인식 될 가능성이 높습니다.

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
> 음성 서비스에서 음성을 일치 시키는 데 사용할 수 있는 최대 문구 목록은 1024 구입니다.

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
> `PhraseListGrammar` 개체에 대 한 변경 내용은 다음 인식에 적용 되거나 음성 서비스에 다시 연결 됩니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
