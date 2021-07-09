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
ms.openlocfilehash: d97ab241120ddc4c8e7434591b866312ea7708ae
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111890117"
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

## <a name="test-programatically-with-the-cognitive-services-voice-assistant-test-tool"></a>Cognitive Services 음성 도우미 테스트 도구를 사용하여 프로그래밍 방식으로 테스트

음성 도우미 테스트 도구는 Microsoft 음성 도우미의 엔드투엔드 기능 회귀 테스트를 위한 구성 가능한 .NET Core C# 콘솔 애플리케이션입니다. 

이 도구는 콘솔 명령으로 수동으로 실행하거나 Azure DevOps CI/CD 파이프라인의 일부로 자동화하여 봇의 회귀를 방지할 수 있습니다.

도구를 설정하는 방법을 알아보려면 [음성 도우미 테스트 도구](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/clients/csharp-dotnet-core/voice-assistant-test)를 참조하세요.

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Speech SDK 지원 클라이언트 애플리케이션을 사용하여 테스트

Speech SDK(소프트웨어 개발 키트)는 음성 지원 애플리케이션을 개발할 수 있도록 많은 Speech Service 기능을 제공합니다. 대부분의 플랫폼에서 다양한 프로그래밍 언어를 사용할 수 있습니다.

Speech SDK를 사용하여 UWP(유니버설 Windows 플랫폼) 클라이언트 애플리케이션을 설정하고 사용자 지정 명령 애플리케이션과 통합하려면 다음을 수행합니다.  
- [방법: Speech SDK를 사용하여 클라이언트 애플리케이션과 통합](./how-to-custom-commands-setup-speech-sdk.md)
- [방법: 클라이언트 애플리케이션에 작업 보내기](./how-to-custom-commands-send-activity-to-client.md)
- [방법: 웹 엔드포인트 설정](./how-to-custom-commands-setup-web-endpoints.md)

다른 프로그래밍 언어 및 플랫폼:
- [Speech SDK 프로그래밍 언어, 플랫폼, 시나리오 용량](./speech-sdk.md)
- [음성 도우미 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub 샘플 참조](https://aka.ms/speech/cc-samples)
