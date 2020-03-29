---
title: 키워드 이름 지정 지침 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 효과적인 키워드를 만드는 것은 기기가 일관되고 정확하게 응답할 수 있도록 하는 데 매우 중요합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370681"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>효과적인 키워드 를 만들기 위한 지침

효과적인 키워드를 만드는 것은 기기가 일관되고 정확하게 응답할 수 있도록 하는 데 매우 중요합니다. 키워드를 사용자 지정하면 기기를 차별화하고 브랜딩을 강화할 수 있습니다. 이 문서에서는 효과적인 키워드를 만들기 위한 몇 가지 기본 원칙을 알아봅니다.

## <a name="choose-an-effective-keyword"></a>효과적인 키워드 선택

키워드를 선택할 때 다음 지침을 고려하십시오.

> [!div class="checklist"]
> * 키워드는 영어 단어 나 구가어야합니다.
> * 말하는 데 2초 이상 걸리지 않아야 합니다.
> * 4-7개 음절의 단어가 가장 적합합니다. 예를 들어 "이봐, 컴퓨터"는 좋은 키워드입니다. 그냥 "Hey"만 사용하는 것은 그렇지 않습니다.
> * 키워드는 일반적인 영어 발음 규칙을 따라야 합니다.
> * 일반적인 영어 발음 규칙을 따르는 고유하거나 심지어 합성된 단어는 거짓 긍정을 줄일 수 있습니다. 예를 들어 "computerama"는 좋은 키워드일 수 있습니다.
> * 일반적인 단어는 선택하지 않도록 합니다. 예를 들어, "eat"과 “go”는 사람들이 평상시 대화에서 자주 말하는 단어입니다. 이러한 단어는 디바이스를 잘못 트리거할 수 있습니다.
> * 대체 발음이 있을 수 있는 키워드를 사용하지 마십시오. 사용자는 디바이스의 반응을 얻기 위해 "올바른" 발음을 알고 있어야 합니다. 예를 들어 "509"는 "five zero nine", "five oh nine" 또는 "five hundred and nine"으로 발음할 수 있습니다. "R.E.I." "R E I" 또는 "Ray"로, "Live"는 "/līv/" 또는 "/liv/"로 발음할 수 있습니다.
> * 특수 문자, 기호 또는 숫자는 사용하지 않도록 합니다. 예를 들어 'Go#' 및 '20+고양이'는 문제가 있는 키워드일 수 있습니다. 그러나 "go sharp" 또는 "twenty plus cats"는 적합할 수 있습니다. 여전히 브랜딩에 기호를 사용하고, 마케팅 및 설명서를 사용하여 적절한 발음을 강화할 수 있습니다.

> [!NOTE]
> 상표권 단어를 키워드로 선택한 경우 해당 상표를 소유하고 있거나 상표 소유자로부터 해당 단어를 사용할 수 있는 권한이 있는지 확인하십시오. Microsoft는 키워드 선택으로 인해 발생할 수 있는 법적 문제에 대해 책임을 지지 않습니다.

## <a name="next-steps"></a>다음 단계

[Speech Studio를 사용하여 사용자 지정 키워드를 만드는](speech-devices-sdk-create-kws.md)방법에 대해 알아봅니다.
