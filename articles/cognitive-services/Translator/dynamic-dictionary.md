---
title: 동적 사전 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure 인지 서비스 번역기 텍스트 API의 동적 사전 기능을 사용하는 방법을 설명합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446705"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>동적 사전을 사용하는 방법

단어나 구에 적용할 번역을 이미 알고 있는 경우 요청 내에 태그로 제공할 수 있습니다. 동적 사전은 적절한 이름 및 제품 이름과 같은 복합 명사에만 안전합니다.

**구문:**

<mstrans:사전 번역 ="문구의 번역">문구< /mstrans:사전>

**요구 사항:**

* 및 `From` `To` 언어에는 영어와 지원되는 다른 언어가 포함되어야 합니다. 
* 자동 감지 `From` 기능을 사용하는 대신 API 번역 요청에 매개 변수를 포함해야 합니다. 

**예제: en-de:**

소스 입력:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

대상 출력:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

이 기능은 HTML 모드를 사용할 때와 그렇지 않을 때 같은 결과를 가져옵니다.

이 기능을 아껴서 사용하십시오. 번역을 사용자 지정하는 더 좋은 방법은 사용자 지정 번역기를 사용하는 것입니다. Custom Translator는 컨텍스트 및 통계적 확률을 최대한 활용합니다. 컨텍스트에 따라 사용자 작업 또는 구를 보여 주는 학습 데이터를 보유하고 있거나 이러한 데이터를 만들 수 있으면 훨씬 더 나은 결과를 얻을 수 있습니다. 사용자 지정 번역기에 대한 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)자세한 내용은 에서 찾을 수 있습니다.
