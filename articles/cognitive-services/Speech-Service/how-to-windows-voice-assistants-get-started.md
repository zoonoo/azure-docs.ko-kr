---
title: Windows의 음성 도우미-시작 하기
titleSuffix: Azure Cognitive Services
description: 샘플 코드 빠른 시작에 대 한 참조를 포함 하 여 windows 음성 에이전트 개발을 시작 하는 단계입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 77d756bc0c7bbdfa38bb1262638ef85b051fc234
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026491"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows에서 음성 도우미 시작

이 가이드에서는 Windows에서 음성 도우미를 개발 하기 시작 하는 단계를 안내 합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

Windows 용 음성 도우미 개발을 시작 하려면 적절 한 개발 환경이 있는지 확인 해야 합니다.

- **Visual Studio:** 2017, Community Edition 이상 [Microsoft Visual Studio](https://visualstudio.microsoft.com/)설치 해야 합니다.
- **Windows 버전**: windows insider fast 링 빌드 및 windows 참가자 버전의 WINDOWS SDK 있는 PC입니다. 이 샘플 코드는 Windows SDK 19018를 사용 하 여 Windows 참가자 릴리스 빌드 19025.vb_release_analog .191112-1600에서 작업 하는 것으로 확인 되었습니다. 지정 된 버전을 초과 하는 빌드 또는 SDK는 호환 되어야 합니다.
- **UWP 개발 도구**: Visual Studio의 유니버설 Windows 플랫폼 개발 워크 로드입니다. Uwp 응용 프로그램을 개발할 수 있도록 컴퓨터를 준비 하려면 UWP [가져오기 설정](/windows/uwp/get-started/get-set-up) 페이지를 참조 하세요.
- **작업 중인 마이크와 오디오 출력**

## <a name="obtain-resources-from-microsoft"></a>Microsoft에서 리소스 가져오기

Windows에서 완전히 사용자 지정 된 음성 에이전트에 필요한 일부 리소스에는 Microsoft의 리소스가 필요 합니다. [UWP Voice Assistant 샘플](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) 은 초기 개발 및 테스트를 위해 이러한 리소스의 샘플 버전을 제공 하므로 초기 개발에는이 섹션이 필요 하지 않습니다.

- **키워드 모델:** 음성 활성화를 사용 하려면 bin 파일 형식의 Microsoft 키워드 모델을 사용 해야 합니다. UWP Voice Assistant 샘플에 제공 된 bin 파일은 "Contoso" 키워드에 대해 학습 됩니다.
- **제한 된 액세스 기능 토큰:** ConversationalAgent Api는 마이크 오디오에 대 한 액세스를 제공 하므로 제한 된 액세스 기능 제한에서 보호 됩니다. 제한 된 액세스 기능을 사용 하려면 Microsoft에서 응용 프로그램의 패키지 id에 연결 된 제한 된 액세스 기능 토큰을 가져와야 합니다.

## <a name="establish-a-dialog-service"></a>대화 상자 서비스 설정

전체 음성 도우미 환경을 위해 응용 프로그램에는 대화 상자 서비스가 필요 합니다.

- 지정 된 오디오 파일에서 키워드를 검색 합니다.
- 사용자 입력을 수신 하 고 텍스트로 변환
- Bot에 텍스트 제공
- 봇의 텍스트 응답을 오디오 출력으로 변환

이는 직접 줄 음성을 사용 하 여 기본 대화 상자 서비스를 만들기 위한 요구 사항입니다.

- **Speech Services 구독:** 음성-텍스트 및 텍스트 음성 변환 변환에 대 한 인식 음성 서비스 구독입니다. [여기](./overview.md#try-the-speech-service-for-free)에서 음성 서비스를 무료로 사용해 보세요.
- **Bot 프레임 워크 봇:**  음성 입력 및 출력을 사용 하도록 [줄 음성을 직접](./direct-line-speech.md) 구독 하는 bot Framework 버전 4.2 이상을 사용 하 여 만든 봇입니다. [이 가이드](./tutorial-voice-enable-your-bot-speech-sdk.md) 에는 "echo bot"을 만들고이를 구독 하 여 줄 음성이 전달 되는 단계별 지침이 포함 되어 있습니다. 사용자 지정 된 봇을 만드는 방법에 대 한 단계를 보려면 [여기](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) 로 이동 하 여 "echo bot" 대신 새 봇을 사용 하 여 줄 음성으로 이동 하는 것과 [동일한 단계를](./tutorial-voice-enable-your-bot-speech-sdk.md) 수행 하면 됩니다.

## <a name="try-out-the-sample-app"></a>샘플 앱 체험

Speech Services 구독 키와 echo bot의 봇 ID를 사용 하 여 [UWP Voice Assistant 샘플](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)을 사용해 볼 준비가 되었습니다. 추가 정보의 지침에 따라 앱을 실행 하 고 자격 증명을 입력 합니다.

## <a name="create-your-own-voice-assistant-for-windows"></a>자신만의 Windows 용 음성 도우미 만들기

Microsoft에서 제한 된 액세스 기능 토큰과 bin 파일을 받은 후에는 Windows에서 고유한 음성 도우미를 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 도우미 구현 가이드 읽기](windows-voice-assistants-implementation-guide.md)