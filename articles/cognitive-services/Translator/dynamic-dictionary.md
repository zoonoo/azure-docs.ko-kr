---
title: Microsoft Translator Text API 동적 사전 | Microsoft Docs
description: Microsoft Translator Text API의 동적 사전 기능을 사용하는 방법입니다.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382872"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Microsoft Translator Text API의 동적 사전 기능을 사용하는 방법

단어나 구에 적용할 번역을 이미 알고 있는 경우 요청 내에 태그로 제공할 수 있습니다. 동적 사전은 적절한 이름 및 제품 이름과 같은 복합 명사에 사용할 때만 안전합니다. 

**구문:** 

<mstrans:dictionary translation=”구 번역”>구</mstrans:dictionary>

**예제: en-de:**

원본 입력: 단어 <mstrans:dictionary translation=\"wordomatic\">단어 또는 구</mstrans:dictionary>는 사전 항목입니다.

대상 출력: Das Wort "wordomatic" ist ein Wörterbucheintrag.

이 기능은 HTML 모드를 사용할 때와 그렇지 않을 때 같은 결과를 가져옵니다. 

이 기능은 자주 사용하지 않는 것이 좋습니다. 번역을 사용자 지정하는 보다 적절하고 훨씬 더 나은 방법은 Microsoft Translator Hub를 사용하는 것입니다. 이 Hub는 컨텍스트 및 통계적 확률을 최대한 활용합니다. 컨텍스트에 따라 사용자 작업 또는 구를 보여 주는 학습 데이터를 보유하고 있거나 이러한 데이터를 만들 수 있는 경제적 여유가 있으면 훨씬 더 나은 결과를 얻을 수 있습니다. 이 Hub에 대한 자세한 내용은 [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com)에서 찾을 수 있습니다.

