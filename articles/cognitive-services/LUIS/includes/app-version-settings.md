---
title: 포함 파일
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590992"
---
|Level|UI 설정|API 설정|정보|
|--|--|--|--|
|앱|엔드포인트를 공개로 설정|`Public`|예측 키가 있고 앱 ID를 알고 있으면 누구나 공용 앱에 액세스할 수 있습니다. |
|버전|비결정론적 학습 사용|`UseAllTrainingData`|학습은 음수 샘플링(negative sampling)의 작은 비율을 사용합니다. 작은 음수 샘플링 대신 모든 데이터를 사용하려면 `true`로 설정합니다. |
|버전|분음 부호 정규화|`NormalizeDiacritics`|분음 부호 정규화는 문자를 발화의 분음 부호에서 일반 문자로 대체합니다.|
|버전|문장 부호 정규화|`NormalizePunctuation`|문장 부호 정규화는 모델이 학습되고 엔드포인트 쿼리가 예측되기 전에 발화에서 문장 부호가 제거됨을 의미합니다.|
|버전|단어 양식 정규화|`NormalizeWordForm`|루트를 벗어난 단어 양식은 무시합니다.|

정규화의 [개념](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)과 [앱](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) 및 [버전](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API를 사용하여 이러한 설정을 업데이트하거나 LUIS 포털의 **관리** 섹션, **애플리케이션 설정** 페이지를 사용하는 방법에 대해 알아봅니다.