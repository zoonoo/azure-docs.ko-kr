---
title: 사용자 지정 절전 모드 해제 단어 만들기
description: Speech Devices SDK에 대한 사용자 지정 절전 모드 해제 단어를 만듭니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 615a901c70fff92141442699ea6e4b8fce1c9ace
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282576"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Speech Service를 사용하여 사용자 지정 절전 모드 해제 단어 만들기

장치는 항상 절전 모드 해제 단어(또는 구)를 수신 대기합니다. 예를 들어, "Hey Cortana"는 Cortana 비서에 대한 절전 모드 해제 단어입니다. 사용자가 절전 모드 해제 단어를 말하면 장치는 사용자가 그 말을 더 이상 하지 않을 때까지 클라우드로 모든 후속 오디오를 보내기 시작합니다. 절전 모드 해제 단어를 사용자 지정하는 것은 장치를 구별하고 브랜드를 강화하는 효과적인 방법입니다.

이 문서에서는 장치를 위한 사용자 지정 절전 모드 해제 단어를 만드는 방법을 설명합니다.

## <a name="choosing-an-effective-wake-word"></a>효과적인 절전 모드 해제 단어 선택

절전 모드 해제 단어를 선택할 때는 다음 지침을 고려합니다.

* 절전 모드 해제 단어는 영어 단어 또는 구여야 합니다. 말하는 데 2초 이상 걸리지 않아야 합니다.

* 4-7 음절의 단어가 가장 적합합니다. 예를 들어, "Hey, Computer"는 좋은 절전 모드 해제 단어이지만 그냥 "Hey"라고만 하는 것은 좋지 않습니다.

* 절전 모드 해제 단어는 일반적인 영어 발음 규칙을 따라야 합니다.

* 일반적인 영어 발음 규칙을 따르는 고유한 단어 또는 합성 단어는 거짓 긍정을 줄일 수 있습니다. 예를 들어 "computerama"는 좋은 절전 모드 해제 단어일 수 있습니다.

* 일반적인 단어는 선택하지 않도록 합니다. 예를 들어, "eat"과 “go”는 사람들이 평상시 대화에서 자주 말하는 단어입니다. 따라서 장치를 잘못 트리거할 수 있습니다.

* 대체 발음이 있을 수 있는 절전 모드 해제 단어는 사용하지 않도록 합니다. 사용자는 장치의 반응을 얻기 위해 "올바른" 발음을 알고 있어야 합니다. 예를 들어 "509"는 "five zero nine", "five oh nine" 또는 "five hundred and nine"으로 발음될 수 있습니다. "R.E.I." 는 "R E I" 또는 "Ray"로 발음될 수 있습니다. "Live"는 [līv] 또는 [liv]로 발음될 수 있습니다.

* 특수 문자, 기호 또는 숫자는 사용하지 않도록 합니다. 예를 들어, "Go #" 및 "20 + cats"는 좋은 절전 모드 해제 단어가 아닙니다. 그러나 "go sharp" 또는 "twenty plus cats"는 사용할 수 있습니다. 브랜딩에서 기호를 사용할 수 있고, 마케팅 및 설명서를 사용하여 적절한 발음을 강화할 수 있습니다.

> [!NOTE]
> 등록 상표 단어를 절전 모드 해제 단어로 선택하는 경우 해당 등록 상표가 본인 소유인지 또는 해당 등록 상표를 사용할 수 있도록 등록 상표 소유자의 허가를 받았는지 확인합니다. Microsoft는 사용자가 선택한 절전 모드 해제 단어로 인한 어떤 법적 문제도 책임지지 않습니다.

## <a name="creating-your-wake-word"></a>절전 모드 해제 단어 만들기

장치에서 사용자 지정 절전 모드 해제 단어를 사용하려면 Microsoft Custom Wake Word Generation 서비스를 사용하여 만들어야 합니다. 절전 모드 해제 단어를 제공하면 이 서비스는 파일을 생성합니다. 그러면 사용자는 이 파일을 개발자 키트에 배포하여 장치에서 절전 모드 해제 단어를 사용하도록 설정합니다.

1. [Custom Speech Service 포털](https://cris.ai/)로 이동합니다.

2. Azure Active Directory에 대한 초대를 받은 전자 메일 주소를 사용하여 새 계정을 만듭니다. 

    ![새 계정 만들기](media/speech-devices-sdk/wake-word-1.png)
 
3.  일단 로그인한 후에 양식을 채우고 **경험 시작**을 클릭합니다.

    ![성공적으로 로그인됨](media/speech-devices-sdk/wake-word-3.png)
 
4. **사용자 지정 절전 모드 해제 단어** 페이지는 공개되지 않으므로 해당 페이지로 이동할 수 있는 링크가 없습니다. 대신 https://cris.ai/customkws 링크를 클릭하거나 붙여 넣습니다.

    ![숨겨진 페이지](media/speech-devices-sdk/wake-word-4.png)
 
6. 선택한 절전 모드 해제 단어를 입력하고 **제출**합니다.

    ![절전 모드 해제 단어 입력](media/speech-devices-sdk/wake-word-5.png)
 
7. 파일이 생성되는 데 몇 분 정도 걸릴 수 있습니다. 브라우저의 탭에 회전하는 원이 표시됩니다. 잠시 후 알림 표시줄이 나타나면서 `.zip` 파일을 다운로드할지 묻는 메시지를 표시합니다.

    ![.zip 파일 수신](media/speech-devices-sdk/wake-word-6.png)

8. `.zip` 파일을 컴퓨터에 저장합니다. 사용자 지정 절전 모드 해제 단어를 개발 키트를 배포하고 [Speech Devices SDK 시작](speech-devices-sdk-qsg.md)의 지침을 따르려면 이 파일이 필요합니다.

9. 이제 **로그아웃**해도 됩니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [체험 Azure 계정](https://azure.microsoft.com/free/)을 얻고 Speech Devices SDK에 등록하세요.

> [!div class="nextstepaction"]
> [Speech Devices SDK에 등록](get-speech-devices-sdk.md)

