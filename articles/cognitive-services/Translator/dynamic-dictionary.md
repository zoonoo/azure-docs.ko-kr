---
title: 동적 사전 - Translator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Translator의 동적 사전 기능을 사용하는 방법을 설명합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: ebfccb6fed46db65f6a70937516552f5280c8016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897767"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>동적 사전을 사용하는 방법

단어나 구에 적용할 번역을 이미 알고 있는 경우 요청 내에 태그로 제공할 수 있습니다. 동적 사전은 적절한 이름 및 제품 이름과 같은 복합 명사에 사용할 때만 안전합니다.

**구문:**

<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>

**요구 사항:**

* `From` 및 `To` 언어에는 영어 및 기타 지원 언어가 포함되어야 합니다. 
* 자동 검색 기능을 사용하지 않고 API 번역 요청에 `From` 매개 변수를 포함해야 합니다. 

**예제: en-de:**

원본 입력: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

대상 출력: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

이 기능은 HTML 모드를 사용할 때와 그렇지 않을 때 같은 결과를 가져옵니다.

이 기능은 자주 사용하지 마세요. 번역을 사용자 지정하려면 Custom Translator를 사용하는 것이 더 좋습니다. Custom Translator는 컨텍스트 및 통계적 확률을 최대한 활용합니다. 컨텍스트에 따라 사용자 작업 또는 구를 보여 주는 학습 데이터를 보유하고 있거나 이러한 데이터를 만들 수 있으면 훨씬 더 나은 결과를 얻을 수 있습니다. 사용자 지정 변환기에 대한 자세한 내용은 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)에서 찾을 수 있습니다.