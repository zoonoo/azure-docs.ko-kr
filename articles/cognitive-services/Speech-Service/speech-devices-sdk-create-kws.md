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
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 22d270d9bc337b9d7ad776baf5dd35f877c05eae
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856430"
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

디바이스에서 사용자 지정 절전 모드 해제 단어를 사용하려면, 먼저 Microsoft Custom Wake Word Generation 서비스를 사용하여 절전 모드 해제 단어를 만들어야 합니다. 절전 모드 해제 단어가 제공되면 이 서비스에서 개발 키트에 배포한 파일을 생성하여 디바이스에서 절전 모드 해제 단어를 사용하도록 설정합니다.

1. [Custom Speech Service 포털](https://cris.ai/)로 이동합니다.

1. Azure Active Directory에 대한 초대를 받은 이메일 주소를 사용하여 새 계정을 만듭니다.

    ![새 계정 만들기](media/speech-devices-sdk/wake-word-1.png)

1. 일반 사용자는 **사용자 지정 절전 모드 해제 단어** 페이지를 사용할 수 없으므로 이 페이지로 이동할 수 있는 직접 링크가 없습니다. Custom Speech 기능을 사용하려면 Azure 구독이 필요하지만 사용자 지정 절전 모드 해제 단어 기능은 필요하지 않습니다. **구독을 찾을 수 없습니다.** 오류 페이지가 표시되면, URL에서 **"Subscriptions?errorMessage=No%20Subscriptions%20found"** 를 "**customkws**"로 바꾸고 Enter 키를 누르기만 하면 됩니다. URL은 해당 지역의 위치에 따라 https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws 또는 https://northeurope.cris.ai/customkws 중 하나여야 합니다.

    ![숨겨져 있는 [사용자 지정 절전 모드 해제] 페이지](media/speech-devices-sdk/wake-word-4.png)

1. 선택한 절전 모드 해제 단어를 입력하고 **단어 제출**을 선택합니다.

    ![절전 모드 해제 단어 입력](media/speech-devices-sdk/wake-word-5.png)

1. 파일을 생성하는 데 몇 분 정도 걸릴 수 있습니다. 브라우저 창에 회전하는 원이 표시됩니다. 잠시 후에 .zip 파일을 다운로드하도록 요구하는 알림 표시줄이 표시됩니다.

    ![.zip 파일 받기](media/speech-devices-sdk/wake-word-6.png)

1. .zip 파일을 컴퓨터에 저장합니다. 이 파일은 사용자 지정 절전 모드 해제 단어를 개발 키트에 배포하는 데 필요합니다. 사용자 지정 절전 모드 해제 단어를 배포하려면 [Speech Devices SDK 시작](speech-devices-sdk-qsg.md)의 지침을 따릅니다.

1. **로그아웃**을 선택합니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [체험 Azure 계정](https://azure.microsoft.com/free/)을 얻고 Speech Devices SDK에 등록하세요.

> [!div class="nextstepaction"]
> [Speech Devices SDK에 등록](get-speech-devices-sdk.md)
