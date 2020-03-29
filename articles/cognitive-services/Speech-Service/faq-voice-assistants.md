---
title: 음성 비서가 자주 묻는 질문
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명령(미리 보기) 또는 직접 회선 음성 채널을 사용하여 음성 비서에 대한 가장 인기 있는 질문에 대한 답변을 얻을 수 있습니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110350"
---
# <a name="voice-assistants-frequently-asked-questions"></a>음성 비서가 자주 묻는 질문

이 문서에서 질문에 대한 답변을 찾을 수 없는 경우 [다른 지원 옵션을](support.md)확인하십시오.

## <a name="general"></a>일반

**Q: 음성 비서란 무엇입니까?**

**A:** Cortana와 마찬가지로 음성 도우미는 사용자의 음성 발언을 듣고, 의미를 위해 해당 발언의 내용을 분석하고, 발언의 의도에 응답하여 하나 이상의 작업을 수행한 다음 음성 구성 요소를 포함하는 사용자에게 응답을 제공하는 솔루션입니다. 시스템과 상호 작용하기 위한 "음성 인, 음성"경험입니다. 음성 도우미 작성자는 음성 `DialogServiceConnector` SDK에서 사용자 지정 [명령(미리 보기)](custom-commands.md) 또는 봇 프레임워크의 [직접 회선 음성](direct-line-speech.md) 채널을 사용하여 만든 도우미와 통신하기 위해 음성 SDK를 사용하여 온디바이스 응용 프로그램을 만듭니다. 이러한 도우미는 맞춤 키워드, 맞춤 음성 및 맞춤 음성을 사용하여 브랜드 또는 제품에 맞는 환경을 제공할 수 있습니다.

**Q: 사용자 지정 명령(미리 보기) 또는 직접 줄 연설을 사용해야 합니까? 차이점은 뭔가요?**

**A:** [사용자 지정 명령(미리 보기)은](custom-commands.md) 작업 완료 시나리오에 적합한 도우미를 쉽게 만들고 호스팅할 수 있는 복잡성이 낮은 도구 집합입니다. [다이렉트 라인 스피치는](direct-line-speech.md) 강력한 대화형 시나리오를 가능하게 하는 풍부하고 정교한 기능을 제공합니다. 자세한 내용은 [보조 솔루션 비교를](voice-assistants.md#choosing-an-assistant-solution) 참조하십시오.

**Q: 어떻게 시작하나요?**

**A:** 사용자 지정 명령(미리 보기) 응용 프로그램 또는 기본 봇 프레임워크 봇을 만드는 것으로 시작하는 가장 좋은 방법입니다.

- [사용자 지정 명령 만들기(미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-new.md)
- [기본 봇 프레임워크 봇 만들기](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [봇을 직접 회선 음성 채널에 연결](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>디버깅

**Q: 내 채널 비밀은 어디에 있습니까?**

**A:** 직접 회선 음성의 미리 보기 버전을 사용했거나 관련 문서를 읽는 경우 직접 회선 음성 채널 등록 페이지에서 비밀 키를 찾을 수 있습니다. Speech SDK의 `DialogServiceConfig` `FromBotSecret` v1.7 팩터리 메서드도 이 값을 기대합니다.

최신 버전의 Direct Line Speech는 장치에서 봇에 연락하는 프로세스를 단순화합니다. 채널 등록 페이지에서 맨 위에 있는 드롭다운은 다이렉트 라인 스피치 채널 등록을 음성 리소스와 연결합니다. 연결되면 v1.8 Speech SDK에는 `BotFrameworkConfig::FromSubscription` 구독과 연결된 `DialogServiceConnector` 봇에 연결하도록 구성하는 팩터리 메서드가 포함되어 있습니다.

클라이언트 응용 프로그램을 v1.7에서 v1.8로 마이그레이션하는 `DialogServiceConfig::FromBotSecret` 경우 채널 비밀 매개 변수(예: 사용한 이전 비밀)에 대해 비어 있지 않고 null이 아닌 값으로 계속 작업할 수 있습니다. 최신 채널 등록과 연결된 음성 구독을 사용할 때는 무시됩니다. 서비스 측 연결이 관련되기 전에 장치에서 확인하므로 값이 null이 아닌 비비어 _있어야 합니다._

자세한 가이드는 채널 등록을 안내하는 [튜토리얼 섹션을](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) 참조하세요.

**Q : 연결할 때 401 오류가 발생하며 아무 것도 작동하지 않습니다. 음성 구독 키가 유효하다는 것을 알고 있습니다. 무슨 일이죠?**

**A:** Azure 포털에서 구독을 관리할 때 **는 음성** 리소스(Microsoft.CognitiveServicesSpeechServices, "음성")를 사용하고 있는지, **인지 서비스** 리소스(Microsoft.CognitiveServicesAllInOne, "모든 인지 서비스")가 _아닌지_ 사용해 주시기 바랍니다. 또한 음성 [비서에 대한 음성 서비스 지역 지원을](regions.md#voice-assistants)확인하십시오.

![직접 회선 음성에 대한 올바른 구독](media/voice-assistants/faq-supported-subscription.png "호환되는 음성 구독의 예")

**Q : 내 `DialogServiceConnector`인식 텍스트를 다시 얻을 수 있지만 봇에서 '1011'오류와 아무것도 표시되지 않습니다. 왜?**

**A:** 이 오류는 도우미와 음성 도우미 서비스 간의 통신 문제를 나타냅니다.

- 사용자 지정 명령(미리 보기)의 경우 사용자 지정 명령(미리 보기) 응용 프로그램이 게시되었는지 확인합니다.
- 직접 회선 음성의 경우 [봇을 직접 회선 음성 채널에 연결하고,](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)관련 웹 소켓 지원을 사용하여 [봇에 스트리밍 프로토콜 지원을 추가한](https://aka.ms/botframework/addstreamingprotocolsupport) 다음 봇이 채널에서 들어오는 요청에 응답하고 있는지 확인합니다.

**Q: 이 코드는 여전히 작동하지 않거나 `DialogServiceConnector`을 사용할 때 다른 오류가 발생합니다. 제가 뭘 해야 하나요?**

**A:** 파일 기반 로깅은 실질적으로 더 자세한 정보를 제공하며 지원 요청을 가속화하는 데 도움이 될 수 있습니다. 이 기능을 사용하려면 [파일 로깅 을 사용하는 방법을 참조하세요.](how-to-use-logging.md)

## <a name="next-steps"></a>다음 단계

- [문제 해결](troubleshooting.md)
- [릴리스 정보](releasenotes.md)
