---
title: 사용자 지정 절전 모드 해제 단어 만들기 - Speech Service
titleSuffix: Azure Cognitive Services
description: 디바이스는 항상 절전 모드 해제 단어(또는 구)를 수신 대기합니다. 사용자가 절전 모드 해제 단어를 말하면 사용자가 말하기를 중지할 때까지 디바이스에서 모든 후속 오디오를 클라우드로 보냅니다. 절전 모드 해제 단어를 사용자 지정하는 것은 디바이스를 구별하고 브랜드를 강화하는 효과적인 방법입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020533"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Speech Service를 사용하여 사용자 지정 절전 모드 해제 단어 만들기

디바이스는 항상 절전 모드 해제 단어(또는 구)를 수신 대기합니다. 예를 들어, "Hey Cortana"는 Cortana 비서에 대한 절전 모드 해제 단어입니다. 사용자가 절전 모드 해제 단어를 말하면 사용자가 말하기를 중지할 때까지 디바이스에서 모든 후속 오디오를 클라우드로 보냅니다. 절전 모드 해제 단어를 사용자 지정하는 것은 디바이스를 구별하고 브랜드를 강화하는 효과적인 방법입니다.

이 문서에서는 디바이스를 위한 사용자 지정 절전 모드 해제 단어를 만드는 방법을 설명합니다.

## <a name="choose-an-effective-wake-word"></a>효과적인 절전 모드 해제 단어 선택

절전 모드 해제 단어를 선택하는 경우 다음 지침을 고려합니다.

* 절전 모드 해제 단어는 영어 단어 또는 구여야 합니다. 말하는 데 2초 이상 걸리지 않아야 합니다.

* 4-7개 음절의 단어가 가장 적합합니다. 예를 들어 "Hey, Computer"는 좋은 절전 모드 해제 단어이지만, 그냥 "Hey"만 사용하는 것은 그렇지 않습니다.

* 절전 모드 해제 단어는 일반적인 영어 발음 규칙을 따라야 합니다.

* 일반적인 영어 발음 규칙을 따르는 고유하거나 심지어 합성된 단어는 거짓 긍정을 줄일 수 있습니다. 예를 들어 "computerama"는 좋은 절전 모드 해제 단어일 수 있습니다.

* 일반적인 단어는 선택하지 않도록 합니다. 예를 들어, "eat"과 “go”는 사람들이 평상시 대화에서 자주 말하는 단어입니다. 이러한 단어는 디바이스를 잘못 트리거할 수 있습니다.

* 대체 발음이 있을 수 있는 절전 모드 해제 단어는 사용하지 않습니다. 사용자는 디바이스의 반응을 얻기 위해 "올바른" 발음을 알고 있어야 합니다. 예를 들어 "509"는 "five zero nine", "five oh nine" 또는 "five hundred and nine"으로 발음할 수 있습니다. "R.E.I." "R E I" 또는 "Ray"로, "Live"는 "/līv/" 또는 "/liv/"로 발음할 수 있습니다.

* 특수 문자, 기호 또는 숫자는 사용하지 않도록 합니다. 예를 들어, "Go #" 및 "20 + cats"는 좋은 절전 모드 해제 단어가 아닙니다. 그러나 "go sharp" 또는 "twenty plus cats"는 적합할 수 있습니다. 여전히 브랜딩에 기호를 사용하고, 마케팅 및 설명서를 사용하여 적절한 발음을 강화할 수 있습니다.

> [!NOTE]
> 등록 상표 단어를 절전 모드 해제 단어로 선택하는 경우 해당 등록 상표의 소유자인지 또는 등록 상표 소유자의 허가를 통해 해당 단어를 사용할 수 있는지 확인합니다. Microsoft는 사용자가 선택한 절전 모드 해제 단어로 인해 발생할 수 있는 모든 법적 문제에 대해 책임을 지지 않습니다.

## <a name="create-your-wake-word"></a>절전 모드 해제 단어 만들기

장치를 사용 하 여 사용자 지정 절전 모드 해제 단어를 사용 하려면 먼저 Microsoft 사용자 지정 Wake 단어 생성 서비스를 사용 하 여 절전 모드 해제 단어를 만들려고 해야 합니다. 한 후 장치에서 절전 모드 해제 단어를 사용 하도록 설정 하려면 개발 키트를 배포 하는 파일 서비스 생성 절전 모드 해제 단어를 제공 합니다.

1. 로 이동 합니다 [사용자 지정 음성 서비스 포털](https://aka.ms/sdsdk-speechportal) 및 **로그인** 선택 하는 음성 구독이 없는 경우 또는 [ **구독 만들기**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![사용자 지정 음성 서비스 포털](media/speech-devices-sdk/wake-word-4.png)

1. 에 [단어를 절전 모드를 해제 하는 사용자 지정](https://aka.ms/sdsdk-wakewordportal) 페이지에 선택 및 클릭의 절전 모드 해제 단어 입력 **추가 절전 모드 해제 단어**. 고객 들 [지침](#choose-an-effective-wake-word) 는 유효한 키워드를 선택할 수 있습니다. 현재 EN-US 언어만 지원합니다.

    ![절전 모드 해제 단어 입력](media/speech-devices-sdk/wake-word-5.png)

1. 절전 모드 해제 word의 세 가지 대체 발음 만들어질 수 있습니다. 원하는 모든 발음을 선택할 수 있습니다. 선택한 **제출** 절전 모드 해제 단어를 생성 합니다. 변경 하려면 절전 모드 해제 단어 하세요 제거 기존 먼저 발음 줄에 올리면 삭제 아이콘 표시 됩니다.

    ![절전 모드 해제 word를 검토 합니다.](media/speech-devices-sdk/wake-word-6.png)

1. 생성할 모델에 대 일 분 정도 걸릴 수 있습니다. 파일을 다운로드 하 라는 메시지가 표시 됩니다.

    ![절전 모드 해제 word 다운로드](media/speech-devices-sdk/wake-word-7.png)

1. .zip 파일을 컴퓨터에 저장합니다. 이 파일을 사용자 지정 절전 모드 해제 word 개발 키트 배포 해야 합니다.

## <a name="next-steps"></a>다음 단계

테스트에 사용자 지정 절전 모드 해제 단어 [음성 장치 SDK 퀵 스타트](https://aka.ms/sdsdk-quickstart)합니다.
