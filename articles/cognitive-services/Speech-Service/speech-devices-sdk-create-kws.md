---
title: 사용자 지정 키워드-음성 서비스 만들기
titleSuffix: Azure Cognitive Services
description: 장치는 항상 키워드 (또는 구)를 수신 대기 합니다. 사용자가 키워드를 표시 하면 장치는 사용자가 말하기를 중단할 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정 하는 것은 장치를 차별화 하 고 브랜딩을 강화 하는 효과적인 방법입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 9a5b5de71ee290b39603968cf4309171689e22e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184839"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>음성 서비스를 사용 하 여 사용자 지정 키워드 만들기

장치는 항상 키워드 (또는 구)를 수신 대기 합니다. 예를 들어 "안녕하세요 Cortana"는 Cortana 길잡이에 대 한 키워드입니다. 사용자가 키워드를 표시 하면 장치는 사용자가 말하기를 중단할 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정 하는 것은 장치를 차별화 하 고 브랜딩을 강화 하는 효과적인 방법입니다.

이 문서에서는 장치에 대 한 사용자 지정 키워드를 만드는 방법에 대해 알아봅니다.

## <a name="choose-an-effective-keyword"></a>유효 키워드 선택

키워드를 선택 하는 경우 다음 지침을 고려 하십시오.

* 키워드는 영어 단어 또는 구 여야 합니다. 말하는 데 2초 이상 걸리지 않아야 합니다.

* 4-7개 음절의 단어가 가장 적합합니다. 예를 들어 "안녕하세요, Computer"는 좋은 키워드입니다. 그냥 "Hey"만 사용하는 것은 그렇지 않습니다.

* 키워드는 일반적인 영어 발음 규칙을 따라야 합니다.

* 일반적인 영어 발음 규칙을 따르는 고유하거나 심지어 합성된 단어는 거짓 긍정을 줄일 수 있습니다. 예를 들어 "computerama"은 좋은 키워드가 될 수 있습니다.

* 일반적인 단어는 선택하지 않도록 합니다. 예를 들어, "eat"과 “go”는 사람들이 평상시 대화에서 자주 말하는 단어입니다. 이러한 단어는 디바이스를 잘못 트리거할 수 있습니다.

* 대체 발음 될 수 있는 키워드를 사용 하지 마십시오. 사용자는 디바이스의 반응을 얻기 위해 "올바른" 발음을 알고 있어야 합니다. 예를 들어 "509"는 "five zero nine", "five oh nine" 또는 "five hundred and nine"으로 발음할 수 있습니다. "R.E.I." "R E I" 또는 "Ray"로, "Live"는 "/līv/" 또는 "/liv/"로 발음할 수 있습니다.

* 특수 문자, 기호 또는 숫자는 사용하지 않도록 합니다. 예를 들어 "Go #" 및 "20 + cats"는 좋은 키워드가 아닙니다. 그러나 "go sharp" 또는 "twenty plus cats"는 적합할 수 있습니다. 여전히 브랜딩에 기호를 사용하고, 마케팅 및 설명서를 사용하여 적절한 발음을 강화할 수 있습니다.

> [!NOTE]
> 키워드로 상표 단어를 선택 하는 경우 해당 상표를 소유 하 고 있거나 해당 단어를 사용할 수 있는 상표 소유자의 권한이 있는지를 알고 있어야 합니다. Microsoft는 키워드를 선택 하 여 발생할 수 있는 법적 문제에 대 한 책임을 지지 않습니다.

## <a name="create-your-keyword"></a>키워드 만들기

사용자 지정 키워드를 사용 하려면 [Speech Studio](https://aka.ms/sdsdk-speechportal)에서 [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지를 사용 하 여 키워드를 만들어야 합니다. 키워드를 제공 하면 장치에 배포 하는 파일이 생성 됩니다.

1. [Speech Studio](https://aka.ms/sdsdk-speechportal) 로 이동 하 여 **로그인** 하거나, 아직 음성 구독이 없는 경우 [**구독 만들기**](https://go.microsoft.com/fwlink/?linkid=2086754)를 선택 합니다.

1. [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지에서 원하는 키워드를 입력 하 고 **키워드 추가**를 클릭 합니다. 효과적인 키워드를 선택 하는 데 도움이 되는 몇 가지 [지침이](#choose-an-effective-keyword) 있습니다. 지원은 현재 en-us 언어로 제한 됩니다.

    ![키워드 입력](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. 이제 포털에서 키워드에 대 한 후보 발음을 만듭니다. 재생 단추를 클릭 하 여 각 후보를 수신 하 고 잘못 된 발음 옆의 검사를 제거 합니다. 올바른 발음만 선택 되 면 **제출** 을 선택 하 여 키워드 생성을 시작 합니다. 키워드를 변경 하려면 먼저 행의 오른쪽에 있는 삭제 단추를 클릭 하 여 기존 항목을 제거 합니다.

    ![키워드 검토](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. 모델을 생성 하는 데 최대 1 분 정도 걸릴 수 있습니다. 그러면 파일을 다운로드 하 라는 메시지가 표시 됩니다.

    ![키워드 다운로드](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. .zip 파일을 컴퓨터에 저장합니다. 사용자 지정 키워드를 장치에 배포 하려면이 파일이 필요 합니다.

## <a name="next-steps"></a>다음 단계

[음성 장치 SDK 퀵 스타트](https://aka.ms/sdsdk-quickstart)를 사용 하 여 사용자 지정 키워드를 테스트 합니다.
