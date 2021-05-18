---
title: 사용자 지정 명령 앱 테스트
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 애플리케이션을 테스트하는 다양한 방법을 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021816"
---
# <a name="test-your-custom-commands-application"></a>사용자 지정 명령 애플리케이션 테스트

이 문서에서는 사용자 지정 명령 애플리케이션을 테스트하는 다양한 방법을 알아봅니다.

## <a name="test-in-the-portal"></a>포털에서 테스트

포털에서의 테스트는 사용자 지정 명령 애플리케이션이 예상대로 작동하는지 확인하는 가장 간단하고 빠른 방법입니다. 앱 교육이 성공적으로 완료되면 `Test` 단추를 클릭하여 테스트를 시작합니다.

> [!div class="mx-imgBorder"]
> ![포털에서 테스트](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트를 사용하여 테스트

Windows 음성 도우미 클라이언트는 C#의 WPF(Windows Presentation Foundation) 애플리케이션으로, 이 애플리케이션을 사용하면 사용자 지정 클라이언트 애플리케이션을 만들기 전에 봇과의 상호 작용을 쉽게 테스트할 수 있습니다.

이 도구는 사용자 지정 명령 애플리케이션 ID를 수락할 수 있습니다. 사용자 지정 명령 서비스에 호스팅되는 작업 완료 또는 명령 및 제어 시나리오를 테스트할 수 있습니다.

클라이언트를 설정하려면 [Windows 음성 도우미 클라이언트](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)를 체크 아웃합니다.

> [!div class="mx-imgBorder"]
> ![WVAC 프로필 만들기](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Speech SDK 지원 클라이언트 애플리케이션을 사용하여 테스트 
Speech SDK(소프트웨어 개발 키트)는 음성 지원 애플리케이션을 개발할 수 있도록 많은 Speech Service 기능을 제공합니다. Speech SDK는 많은 프로그래밍 언어 및 모든 플랫폼에서 사용할 수 있습니다.

Speech SDK를 사용하여 UWP(유니버설 Windows 플랫폼) 클라이언트 애플리케이션을 설정하고 사용자 지정 명령 애플리케이션과 통합하려면 다음을 수행합니다.  
- [방법: Speech SDK를 사용하여 클라이언트 애플리케이션과 통합](./how-to-custom-commands-setup-speech-sdk.md)
- [방법: 클라이언트 애플리케이션에 작업 보내기](./how-to-custom-commands-send-activity-to-client.md)
- [방법: 웹 엔드포인트 설정](./how-to-custom-commands-setup-web-endpoints.md)

다른 프로그래밍 언어 및 플랫폼:
- [Speech SDK 프로그래밍 언어, 플랫폼, 시나리오 용량](./speech-sdk.md)
- [음성 도우미 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 샘플 참조](https://aka.ms/speech/cc-samples)