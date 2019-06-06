---
title: 구 목록-음성 서비스
titlesuffix: Azure Cognitive Services
description: 사용 하 여 구 목록 음성 서비스를 제공 하는 방법을 알아봅니다는 `PhraseListGrammar` 음성-텍스트 인식 결과 개선 하는 개체입니다.
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
# <a name="phrase-lists-for-speech-to-text"></a>음성-텍스트에 대 한 구 목록

구 목록을 사용 하 여 음성 서비스를 제공 하 여 음성 인식 정확도 높일 수 있습니다. 구 목록 오디오 데이터에서 개인의 이름 또는 특정 위치와 같은 알려진된 구를 식별 하는 데 사용 됩니다.

예를 들어 명령이 있는 경우 "이동" 하 고 가능한 통용 될 수 있습니다 "Ward" 대상 "Ward에 이동" 항목을 추가할 수 있습니다. 구를 추가 확률이 증가 하는 경우 "이동" 대신 "Ward에 이동"를 인식할 수는 오디오는 인식 합니다.

단일 단어 또는 구를 전체 구 목록에 추가할 수 있습니다. 인식 하는 동안 구 목록 항목에 정확히 일치 하는 오디오에 포함 되어 있으면 됩니다. "이동 방향으로 느린"는 "이동 하려면 Ward", 및 캡처된 구 구 목록에 포함 된 경우 이전 예제에서 구축 되 고 인식 결과 "이동 Ward 느린" 됩니다.

## <a name="how-to-use-phrase-lists"></a>구 나열을 사용 하는 방법

아래 샘플에는 사용 하 여 구 목록 작성 하는 방법을 설명 합니다 `PhraseListGrammar` 개체입니다.

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
> 구 목록 음성 서비스는 음성 일치를 사용 하는 최대 수는 1024 구입니다.

연결 된 구를 지울 수도 있습니다는 `PhraseListGrammar` 호출 clear ()에서.

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
> 변경 된 `PhraseListGrammar` 적용 다음 인식 또는 음성 서비스에 다시 연결한 다음 개체입니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
