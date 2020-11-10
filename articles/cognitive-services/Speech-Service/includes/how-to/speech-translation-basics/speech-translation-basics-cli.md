---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424793"
---
음성 서비스의 핵심 기능 중 하나는 휴먼 음성을 인식 하 고 다른 언어로 번역 하는 기능입니다. 이 빠른 시작에서는 앱 및 제품에서 음성 SDK를 사용 하 여 고품질 음성 번역을 수행 하는 방법에 대해 알아봅니다. 이 빠른 시작은 마이크의 음성을 다른 언어로 된 텍스트로 변환 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>원본 및 대상 언어 설정

이 명령은 음성을 음성에서 프랑스어로 변환 하기 위해 Speech CLI를 호출 합니다.

```shell
 spx translate --microphone --source it-IT --target fr
```