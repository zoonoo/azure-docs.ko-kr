---
title: 텍스트 음성 변환 출력-음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech SDK에서 로깅을 사용 하도록 설정 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562754"
---
# <a name="fine-tune-text-to-speech-output"></a>텍스트 음성 변환 출력 미세 조정

Azure Speech Services를 사용 하면 [SSML (음성 합성 마크업) 언어](speech-synthesis-markup.md)를 사용 하 여 텍스트 음성 변환의 속도, 발음, 볼륨, 피치 및 컨투어를 조정할 수 있습니다. SSML은 태그를 사용 하 여 튜닝이 필요한 기능을 서비스에 알리는 XML 기반 태그 언어입니다. 그런 다음이 SSML 메시지는 각 요청의 본문에서 텍스트 음성 변환 서비스로 전송 됩니다. 사용자 지정 프로세스를 간소화 하기 위해 이제 음성 서비스는 텍스트 음성 변환 출력을 실시간으로 시각적으로 검사 하 고 미세 조정할 수 있는 [음성 조정](https://aka.ms/voicetuning) 도구를 제공 합니다.

음성 조정 도구는 Microsoft의 [표준](language-support.md#standard-voices), [신경망](language-support.md#text-to-speech)및 [사용자 지정 음성을](how-to-customize-voice-font.md)지원 합니다.

## <a name="get-started-with-the-voice-tuning-tool"></a>음성 조정 도구 시작

음성 조정 도구를 사용 하 여 텍스트 음성 변환 출력을 세밀 하 게 조정 하려면 먼저 다음 단계를 완료 해야 합니다.

1. 아직 없는 경우 [무료 Microsoft 계정](https://account.microsoft.com/account) 를 만듭니다.
2. 아직 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/) 을 만듭니다. **무료 시작**을 클릭 하 고 Microsoft 계정를 사용 하 여 새 Azure 계정을 만듭니다.

3. Azure Portal에서 음성 서비스 구독을 만듭니다. [음성 리소스를 만드는 방법](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) 에 대 한 단계별 지침을 제공 합니다.
   >[!NOTE]
   >Azure Portal에서 음성 리소스를 만들 때 Azure 위치 정보는 TTS 음성 영역과 일치 해야 합니다. 신경망은 Azure 위치의 하위 집합을 지원 합니다. 전체 지원 목록은 [지역](regions.md#text-to-speech)을 참조 하세요.

   >[!NOTE]
   >서비스를 사용 하려면 Azure Portal에서 만든 F0 또는 S0 키가 있어야 합니다. 음성 조정은 [30 일 무료 평가판 키](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial)를 지원 **하지 않습니다** .

4. [음성 조정](https://aka.ms/voicetuning) 포털에 로그인 하 고 음성 서비스 구독을 연결 합니다. 단일 Speech Services 구독을 선택한 다음 프로젝트를 만듭니다.
5. **새 튜닝**을 선택 합니다. 그러고 나서 다음 단계를 수행합니다.

   * **모든 구독**을 찾아 선택 합니다.  
   * **기존 구독 연결**을 선택합니다.  
     ![기존 구독](./media/custom-voice/custom-voice-connect-subscription.png)을 연결 합니다.
   * Azure Speech Services 구독 키를 입력 하 고 **추가**를 선택 합니다. 구독 [페이지](https://go.microsoft.com/fwlink/?linkid=2090458)의 음성 사용자 지정 포털에서 구독 키를 사용할 수 있습니다. [Azure Portal](https://portal.azure.com/)의 리소스 관리 창에서 키를 가져올 수도 있습니다.
   * 사용할 음성 서비스 구독이 둘 이상 있는 경우 각 구독에 대해 이러한 단계를 반복 합니다.

## <a name="customize-the-text-to-speech-output"></a>텍스트 음성 변환 출력 사용자 지정

계정을 만들고 구독을 연결 했으므로 이제 텍스트 음성 변환 출력의 튜닝을 시작할 수 있습니다.

1. 음성을 선택 합니다.
2. 편집 하려는 텍스트를 입력 합니다.
3. 편집을 시작 하기 전에 오디오를 재생 하 여 출력에 대 한 느낌을 받으세요.
4. 구체화할 단어/문장을 선택 하 고 다른 SSML 기반 함수를 사용 하 여 시험해 볼 수 있습니다.

>[!TIP]
> SSML를 조정 하 고 음성 출력을 튜닝 하는 방법에 대 한 자세한 내용은 [ssml (Speech 합성 Markup Language)](speech-synthesis-markup.md)을 참조 하세요.

## <a name="limitations"></a>제한 사항

신경망 조정은 표준 및 사용자 지정 음성의 조정과 약간 다릅니다.

* Intonation은 신경망에 대해 지원 되지 않습니다.
* 피치 및 볼륨 기능은 전체 문장 에서만 작동 합니다. 이러한 기능은 단어 수준에서 사용할 수 없습니다.
* Rate의 경우 일부 신경망을 사용 하 여 조정할 수 있는 반면, 일부 신경망은 전체 문장을 선택 해야 합니다.

> [!TIP]
> 음성 조정 도구는 기능 및 튜닝에 대 한 컨텍스트 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure에서 음성 리소스 만들기](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [음성 조정 시작](https://speech.microsoft.com/app.html#/VoiceTuning)
* [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)
