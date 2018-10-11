---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888699"
---
예상 값과 기본 상한 및 하한으로 데이터가 반환됩니다. 실제로 [sensitivity] 매개 변수를 정의한 다음, (ExpectedValue + sensitivity * UpperMargin)을 상한, (ExpectedValue - sensitivity * LowerMargin)을 하한으로 사용하여 변칙 요소를 직접 조정할 수 있습니다. [sensitivity] 값은 1보다 커야 합니다. 다음은 조정을 위한 몇 가지 다이어그램입니다.

> [!NOTE]
> 다이어그램은 응용 프로그램 예제가 아니라 별도의 도구에서 응용 프로그램 예제를 사용하여 생성된 것입니다.

![조정: sensitivity = 1.0](../media/sensitivity_1.png)
![조정: sensitivity = 1.5](../media/sensitivity_1.5.png)
![조정: sensitivity = 2](../media/sensitivity_2.png)
![조정: sensitivity = 3.5](../media/sensitivity_3.5.png)