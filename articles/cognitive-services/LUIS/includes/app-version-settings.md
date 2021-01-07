---
title: 포함 파일
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83653157"
---
정규화의 [개념](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)과 [버전](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API를 사용하여 이러한 설정을 업데이트하거나 LUIS 포털의 **관리** 섹션, **설정** 페이지를 사용하는 방법에 대해 알아봅니다.


|UI 설정|API 설정|정보|
|--|--|--|
|비결정론적 학습 사용|`UseAllTrainingData`|학습은 음수 샘플링(negative sampling)의 작은 비율을 사용합니다. 작은 음수 샘플링 대신 모든 데이터를 사용하려면 `true`로 설정합니다. |
|분음 부호 정규화|`NormalizeDiacritics`|분음 부호 정규화는 문자를 발화의 분음 부호에서 일반 문자로 대체합니다. 이 설정은 분음 부호를 지원하는 [언어](../luis-reference-application-settings.md#diacritics-normalization)에서만 사용할 수 있습니다.|
|문장 부호 정규화|`NormalizePunctuation`|문장 부호 정규화는 모델이 학습되고 엔드포인트 쿼리가 예측되기 전에 발화에서 문장 부호가 제거됨을 의미합니다.|
|단어 양식 정규화|`NormalizeWordForm`|루트를 벗어난 단어 양식은 무시합니다.|
