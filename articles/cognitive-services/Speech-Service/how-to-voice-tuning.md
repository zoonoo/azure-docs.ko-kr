---
title: 텍스트 음성 변환 출력-음성 서비스를 미세 조정
titleSuffix: Azure Cognitive Services
description: Speech SDK에서 로깅을 사용 하도록 설정 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 8d3e25f8217f3cc8772de9fbbb06a407008ca6f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65593883"
---
# <a name="fine-tune-text-to-speech-output"></a>텍스트 음성 변환 출력 미세 조정

Azure 음성 서비스를 사용 하면 속도, 발음, 볼륨, 피치 및 사용 하 여 출력 하는 텍스트 음성 변환의 윤곽선에 맞게 [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)합니다. SSML은 어떤 기능을 조정 해야 하는 방법에 대 한 서비스를 알리기 위해 태그를 사용 하는 XML 기반 태그 언어입니다. SSML 메시지 텍스트 음성 변환 서비스로 각 요청의 본문에 전송 됩니다. 사용자 지정 프로세스를 간단 하 게 Speech Services 이제 제공 된 [음성 튜닝](https://aka.ms/voicetuning) 수 있는 도구를 시각적으로 검사 하 고 미세 조정 텍스트 음성 변환 출력을 실시간으로 합니다.

음성 튜닝이 도구는 Microsoft의 지원 [표준](language-support.md#standard-voices)하십시오 [신경망](language-support.md#text-to-speech), 및 [사용자 지정 음성](how-to-customize-voice-font.md)합니다.

## <a name="get-started-with-the-voice-tuning-tool"></a>음성 조정 도구를 사용 하 여 시작

음성 조정 도구를 사용 하 여 텍스트 음성 변환 출력을 미세 조정을 시작 하기 전에 이러한 단계를 완료 해야 합니다.

1. 만들기는 [무료 Microsoft 계정](https://account.microsoft.com/account) 아직 없는 경우.
2. 만들기는 [무료 Azure 계정](https://azure.microsoft.com/free/) 아직 없는 경우. 클릭 **무료 시작**, Microsoft 계정을 사용 하는 새 Azure 계정을 만듭니다.

3. Azure portal에서 음성 서비스 구독을 만듭니다. 에 대 한 단계별 지침은 [음성 리소스를 만드는 방법](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) 사용할 수 있습니다.
   >[!NOTE]
   >Azure portal에서 음성 리소스를 만들 때 Azure 위치 정보를 TTS 음성 영역과 일치 해야 합니다. 신경망 TTS 음성 Azure 위치를 하위 집합을 지원합니다. 지원의 전체 목록을 참조 하세요 [지역](regions.md#text-to-speech)합니다.

   >[!NOTE]
   >F0 또는 서비스를 사용 하려면 먼저 Azure portal에서 만든 S0 키를 해야 합니다. 튜닝 음성 **하지 않습니다** 지원 합니다 [30 일 무료 평가판 키](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial)합니다.

4. 에 로그인 합니다 [음성 튜닝](https://aka.ms/voicetuning) 포털 음성 서비스 구독을 연결 합니다. 단일 Speech Services 구독을 선택 하 고 프로젝트를 만듭니다.
5. 선택 **새 튜닝**합니다. 그러고 나서 다음 단계를 수행합니다.

   * 찾기 및 선택 **모든 구독**합니다.  
   * **기존 구독 연결**을 선택합니다.  
     ![기존 구독을 연결](./media/custom-voice/custom-voice-connect-subscription.png)합니다.
   * 사용자 Azure 음성 서비스의 구독 키를 입력 하 고 선택 **추가**합니다. 구독 키를 사용자 지정 음성 포털에서 사용할 수는 [구독 페이지](https://go.microsoft.com/fwlink/?linkid=2090458)합니다. 리소스 관리 창에서 키를 가져올 수도 있습니다는 [Azure portal](https://portal.azure.com/)합니다.
   * 사용 하려는 음성 서비스 구독이 둘 이상인 경우 각 구독에 대해 이러한 단계를 반복 합니다.

## <a name="customize-the-text-to-speech-output"></a>텍스트 음성 변환 출력을 사용자 지정

이제는 계정을 만들고 구독을 연결 했으므로, 텍스트 음성 변환 출력을 튜닝을 시작할 수 있습니다.

1. 음성을 선택 합니다.
2. 편집 하려는 텍스트를 입력 합니다.
3. 편집을 시작 하기 전에 출력에 대해 이해할 수 있도록 오디오를 재생 합니다.
4. 을 구체화 하려는 단어/문장을 선택 하 고 다른 SSML 기반 함수를 사용 하 여 실험을 시작 합니다.

>[!TIP]
> SSML 조정 및 음성 출력을 튜닝 하는 방법에 대 한 자세한 내용은 참조 하세요. [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)합니다.

## <a name="limitations"></a>제한 사항

신경망 음성 튜닝 하는 것은 표준 및 사용자 지정 음성에 대 한 튜닝 보다 약간 다릅니다.

* 신경망 음성에 대 한 정보와 지원 되지 않습니다.
* 완전 한 문장 피치 및 볼륨 기능 에서만 작동합니다. 이러한 기능은 word 수준에서 사용할 수 없습니다.
* 속도 대 한 전체 문장을 선택 해야 할 다른 단어 기반 일부 신경망 목소리를 조정할 수 있습니다.

> [!TIP]
> 음성 조정 도구 기능 및 튜닝 하는 방법에 대 한 컨텍스트 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure에서 음성 리소스 만들기](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [음성 조정 시작](https://speech.microsoft.com/app.html#/VoiceTuning)
* [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)
