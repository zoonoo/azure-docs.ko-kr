---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76122816"
---
## <a name="change-the-model-update-frequency"></a>모델 업데이트 빈도 변경

Azure Portal의 **구성** 페이지에 있는 Personalizer 리소스에서 **모델 업데이트 빈도**를 10초로 변경합니다. 이 짧은 기간 동안 서비스가 빠르게 학습되어 각 반복에 대한 상위 작업이 변경되는 상태를 확인할 수 있습니다.

![모델 업데이트 빈도 변경](../media/settings/configure-model-update-frequency-settings.png)

Personalizer 루프가 처음 인스턴스화되면 학습을 위한 Reward API 호출이 없으므로 모델이 없습니다. 순위 호출은 각 항목에 대해 동일한 확률을 반환합니다. 애플리케이션은 RewardActionId의 출력을 사용하여 항상 콘텐츠의 순위를 매겨야 합니다.