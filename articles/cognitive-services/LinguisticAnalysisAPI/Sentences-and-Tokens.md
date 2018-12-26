---
title: 문장 및 토큰 - Linguistic Analysis API
titlesuffix: Azure Cognitive Services
description: Linguistic Analysis API에서 수행하는 문장 분리 및 토큰화에 대해 알아봅니다.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 289cab4999276cbfb1fa558f558ebafa8e4e3a30
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237877"
---
# <a name="sentence-separation-and-tokenization"></a>문장 분리 및 토큰화

> [!IMPORTANT]
> Linguistic Analysis 미리 보기는 2018년 8월 9일부로 서비스 해제되었습니다. 텍스트 처리 및 분석에는 [Azure Machine Learning 텍스트 분석 모듈](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)을 사용하는 것이 좋습니다.

## <a name="background-and-motivation"></a>배경 및 동기

텍스트 본문의 경우 언어 분석의 첫 번째 단계는 문장과 토큰으로 분리하는 것입니다.

### <a name="sentence-separation"></a>문장 분리

언뜻 보기에는 텍스트를 쉽게 나눌 수 있을 것 같습니다. 문장 마침 표식을 찾아 거기서 문장을 끊습니다.
하지만 이러한 표식은 종종 복잡하고 모호합니다.

다음 예제 텍스트를 잘 살펴보세요.

> What did you say?!? I didn't hear about the director's "new proposal." It's important to Mr. and Mrs. Smith.

이 텍스트에는 다음과 같은 3개의 문장이 포함되어 있습니다.

- What did you say?!?
- I didn't hear about the director's "new proposal."
- It's important to Mr. and Mrs. Smith.

문장의 끝이 매우 다른 방법으로 표시되었습니다.
첫 번째는 물음표와 느낌표(감탄 부호라고도 함)의 조합으로 끝납니다.
두 번째는 마침표로 끝나지만, 이어지는 큰 따옴표를 앞 문장에 넣어야 합니다.
세 번째 문장에서는 똑같은 마침표를 사용하여 약어를 표시하는 것을 볼 수 있습니다.
문장 부호만 봐도 대충 알 수 있지만, 진짜 문장 경계를 식별하려면 추가 작업이 필요합니다.

### <a name="tokenization"></a>토큰화

그 다음 작업은 문장을 토큰으로 나누는 것입니다.
대부분의 경우 영어 토큰은 공백으로 구분됩니다.
(영어는 단어 사이에 공백을 거의 사용하지 않는 중국어보다 토큰 또는 단어를 찾기 훨씬 쉽습니다.
첫 번째 문장을 "Whatdidyousay?"로 쓸 수도 있습니다.)

몇 가지 어려운 사례가 있습니다.
첫째, 문장 부호는 종종(항상 그렇지는 않지만) 주변 컨텍스트와 분리해야 합니다.
둘째, 영어에는 "didn't" 또는 "it's"처럼 단어를 압축하고 줄여 쓰는 *축약형*이 있습니다.
토크나이저의 목표는 문자 시퀀스를 단어로 나누는 것입니다.

위의 예제 문장으로 돌아가 봅시다.
고유한 토큰 사이에 "가운데 점"(&middot;)을 배치했습니다.

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs. &middot; Smith &middot; .

사전에서 찾는 대부분의 토큰이 단어입니다(예: *important*, *director*).
나머지는 문장 부호로만 구성됩니다.
마지막으로, *not*을 줄인 *n't*, 소유격의 *'s* 같은 축약형을 나타내는 흔치 않은 토큰이 있습니다.
이 토큰화를 사용하면 *didn't*라는 단어와 *did not*이라는 구를 보다 일관적인 방식으로 처리할 수 있습니다.

## <a name="specification"></a>사양

문장 및 토큰 구성을 일관적으로 결정하는 것이 중요합니다.
현재, [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42)의 기준을 참조하고 있습니다(일부 추가 세부 사항은 ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html 참조).
