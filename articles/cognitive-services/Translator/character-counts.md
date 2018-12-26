---
title: 문자 수 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API에서 문자 수를 계산하는 방법
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649197"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Translator Text API에서 문자 수를 계산하는 방법

Translator Text API는 입력의 모든 문자 수를 계산합니다. 바이트가 아니라 유니코드 문자입니다. 유니코드 서로게이트는 두 문자로 계산됩니다. 공백과 태그는 문자로 계산됩니다. 응답 길이는 중요하지 않습니다.

Detect 및 BreakSentence 메서드 호출은 문자 사용에 계산되지 않습니다. 그러나 Detect 및 BreakSentence 메서드 호출은 개수에 포함되는 다른 함수의 사용과 적절한 비율을 유지해야 합니다. Microsoft는 Detect 및 BreakSentence 개수 계산을 시작할 수 있는 권리를 보유합니다.

문자 수에 대한 자세한 내용은 [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)를 참조하세요.
