---
title: 직접 줄 음성에 대 한 질문과 대답
titleSuffix: Azure Cognitive Services
description: 직접 줄 음성 채널을 사용 하 여 음성 우선 가상 도우미에 대 한 가장 인기 있는 질문에 답변 합니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 2c669f00ae65667f85976aca218ce51d630159ee
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465496"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>음성 우선 가상 도우미 미리 보기: 질문과 대답

이 문서의 질문에 대 한 답변을 찾을 수 없으면, 체크 아웃 [다른 지원 옵션](support.md)합니다.

## <a name="general"></a>일반

**Q: 직접 줄 음성 란?**

**A:** `DialogServiceConnector` Speech SDK에서 양방향 봇 Bot Framework에 직접 줄 음성 채널에 연결 된 비동기 통신을 제공 합니다. 이 채널 봇을 대화형 환경을 아웃 음성, 음성에 완벽 하 게 될 수 있도록 Azure 음성 서비스에서 음성-텍스트 및 텍스트 음성 변환에 대 한 조정 된 액세스를 제공 합니다. 절전 모드 해제 단어 및 단어 확인 절전 모드 해제에 대 한 지원과이 솔루션을 통해 브랜드 또는 제품에 대해 고도로 사용자 지정 가능한 음성 우선 가상 도우미를 작성할 수 있습니다.

**Q: 어떻게 시작하나요?**

**A:** 음성 우선 가상 도우미를 만드는 방식으로 시작 하려면 가장은 시작 하기 [기본 Bot Framework 봇 만들기](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)합니다. 다음으로 연결 하면 봇이 마치 합니다 [직접 줄 음성 채널](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)합니다.

## <a name="debugging"></a>디버그

**Q: 연결할 때는 401 오류 메시지가 표시 하 고 아무 것도 작동 합니다. 필자는 필자의 음성 구독 키가 유효 합니다. 무슨 일이죠?**

**A:** 미리 보기에서 직접 줄 음성 제한이 특정 구독에 사용할 수 있습니다. 사용 해야 합니다 **Speech** 리소스 (Microsoft.CognitiveServicesSpeechServices, "Speech") 및 *하지* 는 **Cognitive Services** 리소스 ( Microsoft.CognitiveServicesAllInOne, "모든 Cognitive Services"). 만 [Speech Services 영역의 하위 집합](regions.md#voice-first-virtual-assistants) 직접 줄 음성에 대 한 현재 지원 됩니다.

![직접 회선 음성에 대 한 구독을 수정한](media/voice-first-virtual-assistants/faq-supported-subscription.png "호환 음성 구독의 예제")

**Q: 직접 줄 음성 인식 텍스트 다시 가져오려면 있지만 내 봇에서 '1011' 오류가 발생 하 고 아무 것도 표시 합니다. 이유**

**A:** 이 오류는 봇 및 직접 줄 음성 간 통신 문제를 나타냅니다. 되었는지 확인 하십시오 [직접 줄 음성 채널 연결](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)를 [스트리밍 프로토콜 지원이 추가 되었습니다](https://aka.ms/botframework/addstreamingprotocolsupport) (관련된 Websocket 지원 사용), 봇 및 봇을 들어오는에 응답 하는 확인 채널에서 요청 합니다.

**Q: 아직이 코드가 작동 하지 않습니다 및/또는 DialogServiceConnector를 사용 하는 경우 다른 오류가 발생 했습니다. 어떻게 해야 하나요?**

**A:** 파일 기반 로깅 보다 훨신 더 많은 세부 정보를 제공 및 지원 요청을 가속화 하는 데 도움이 됩니다. 이 기능을 사용 하려면 [파일 로깅 사용 방법](how-to-use-logging.md)합니다.

## <a name="next-steps"></a>다음 단계

* [문제 해결](troubleshooting.md)
* [릴리스 정보](releasenotes.md)
