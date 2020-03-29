---
title: '튜토리얼 : 음성 음성 SDK를 사용하여 봇을 활성화 - 음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Microsoft 봇 프레임워크를 사용하여 에코 봇을 만들고 Azure에 배포한 다음 봇 프레임워크 직접 회선 음성 채널에 등록합니다. 그런 다음 봇과 대화하고 응답하는 것을 들을 수 있는 Windows용 샘플 클라이언트 앱을 구성합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 96d2c2e5e3772575e681d2db079ab0122b7014e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348545"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>튜토리얼: 음성 SDK를 사용하여 봇을 음성 으로 활성화

이제 음성 서비스의 기능을 사용하여 채팅 봇을 음성으로 쉽게 활성화할 수 있습니다.

이 자습서에서는 Microsoft 봇 프레임워크를 사용하여 에코 봇을 만들고 Azure에 배포한 다음 봇 프레임워크 직접 회선 음성 채널에 등록합니다. 그런 다음 봇과 대화하고 응답하는 것을 들을 수 있는 Windows용 샘플 클라이언트 앱을 구성합니다.

이 자습서는 Azure, Bot-Framework 봇, 직접 회선 음성 또는 음성 SDK로 여정을 시작하고 제한된 코딩으로 작업 시스템을 신속하게 구축하려는 개발자를 위해 설계되었습니다. 이러한 서비스에 대한 경험이나 익숙함은 필요하지 않습니다.

이 연습의 끝에서 다음과 같이 작동 하는 시스템을 설정 했습니다.

1. 샘플 클라이언트 응용 프로그램은 직접 회선 음성 채널 및 에코 봇에 연결하도록 구성됩니다.
1. 버튼 누를 때 기본 마이크에서 오디오가 녹음됩니다(또는 사용자 지정 키워드가 활성화되면 계속 녹음됨).
1. 선택적으로 사용자 지정 키워드 감지가 발생하여 오디오스트리밍이 클라우드로 이동됩니다.
1. 음성 SDK를 사용하여 앱은 직접 회선 음성 채널에 연결하고 오디오를 스트리밍합니다.
1. 선택적으로, 더 높은 정확도의 키워드 확인은 서비스에서 발생합니다.
1. 오디오가 음성 인식 서비스에 전달되고 텍스트로 전사됩니다.
1. 인식된 텍스트는 Echo-Bot에 봇 프레임워크 활동으로 전달됩니다. 
1. 응답 텍스트 텍스트 음성 변환(TTS) 서비스에 의해 오디오로 전환되고 재생을 위해 클라이언트 응용 프로그램으로 다시 스트리밍됩니다.

![다이어그램 태그](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "음성 채널 흐름")

> [!NOTE]
> 이 자습서의 단계는 유료 서비스가 필요하지 않습니다. 새 Azure 사용자는 무료 Azure 평가판 구독및 음성 서비스의 프리 계층의 크레딧을 사용하여 이 자습서를 완료할 수 있습니다.

이 자습서에서는 다음 내용을 다룹니다.
> [!div class="checklist"]
> * 새 Azure 리소스 만들기
> * Azure 앱 서비스에 Echo Bot 샘플을 빌드, 테스트 및 배포
> * 직접 회선 음성 채널로 봇 등록
> * Windows 음성 도우미 클라이언트를 빌드하고 실행하여 에코 봇과 상호 작용
> * 사용자 지정 키워드 활성화 추가
> * 인식 및 음성 음성의 언어를 변경하는 법을 배웁니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 내용은 다음과 같습니다.

- 작동 중인 마이크와 스피커(또는 헤드폰)가 있는 Windows 10 PC
- [비주얼 스튜디오 2017](https://visualstudio.microsoft.com/downloads/) 이상
- [.NET 코어 SDK](https://dotnet.microsoft.com/download) 버전 2.1 이상
- Azure 계정. [무료로 가입하십시오.](https://azure.microsoft.com/free/ai/)
- [GitHub](https://github.com/) 계정
- [윈도우에 대한 Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 자습서에서 만들 클라이언트 앱은 소수의 Azure 서비스를 사용합니다. 봇의 응답에 대한 왕복 시간을 줄이려면 이러한 서비스가 동일한 Azure 지역에 있는지 확인해야 합니다. 이 섹션에서는 **미국 서부** 지역에 리소스 그룹을 만듭니다. 이 리소스 그룹은 봇 프레임워크, 직접 회선 음성 채널 및 음성 서비스에 대한 개별 리소스를 만들 때 사용됩니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">리소스 그룹 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * **구독을** **무료 평가판으로** 설정합니다(기존 구독을 사용할 수도 있음).
   * **리소스 그룹의**이름을 입력합니다. 우리는 **SpeechEchoBot튜토리얼 - 리소스 그룹을 권장합니다.**
   * **지역** 드롭다운에서 미국 **서부를**선택합니다.
1. **검토 + 만들기**를 클릭합니다. 유효성 검사를 읽는 배너가 **표시됩니다.**
1. **만들기**를 클릭합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.
1. 이 자습서의 후반부에서 만들 리소스와 마찬가지로 쉽게 액세스할 수 있도록 이 리소스 그룹을 대시보드에 고정하는 것이 좋습니다. 이 리소스 그룹을 고정하려면 대시보드 오른쪽 상단에 있는 핀 아이콘을 클릭합니다.

### <a name="choosing-an-azure-region"></a>Azure 지역 선택

이 자습서에 대해 다른 지역을 사용하려는 경우 다음과 같은 요인으로 인해 선택 사항이 제한될 수 있습니다.

* [지원되는 Azure 영역을](regions.md#voice-assistants)사용하는지 확인합니다.
* 직선 음성 채널은 표준 및 신경 음성이 있는 텍스트 음성 변환 서비스를 사용합니다. 신경 음성은 [특정 Azure 영역으로 제한됩니다.](regions.md#standard-and-neural-voices)
* 무료 평가판 키는 특정 지역으로 제한될 수 있습니다.

지역에 대한 자세한 내용은 [Azure 위치를](https://azure.microsoft.com/global-infrastructure/locations/)참조하십시오.

## <a name="create-resources"></a>리소스 만들기

지원되는 지역에 리소스 그룹이 있으므로 다음 단계는 이 자습서에서 사용할 각 서비스에 대한 개별 리소스를 만드는 것입니다.

### <a name="create-a-speech-service-resource"></a>음성 서비스 리소스 만들기

다음 지침을 따라 음성 리소스를 만듭니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">음성 서비스 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * 리소스에 **이름을**지정합니다. 우리는 **SpeechEchoBot자습서 음성을 권장합니다**
   * **구독의**경우 **무료 평가판을** 선택했는지 확인합니다.
   * **위치의**경우 **미국 서부를**선택합니다.
   * **가격 책정 계층의**경우 **F0을**선택합니다. 이것이 프리 티어입니다.
   * **리소스 그룹의**경우 **SpeechEchoBot자습서-리소스 그룹을**선택합니다.
5. 필요한 모든 정보를 입력한 후 에서 **만들기를**클릭합니다. 리소스를 만드는 데 몇 분 정도 걸릴 수 있습니다.
6. 이 자습서의 이후 에서이 서비스에 대 한 구독 키가 필요 합니다. 리소스의 **개요(키** 관리) 또는 **키에서**언제든지 이러한 키에 액세스할 수 있습니다.

이 시점에서 리소스**그룹(SpeechEchoBot-ResourceGroup)에**음성 리소스가 있는지 확인합니다.

| 이름 | Type  | 위치 |
|------|-------|----------|
| 스피치에코봇튜토리얼-스피치 | Cognitive Services | 미국 서부 |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

다음 단계는 앱 서비스 계획을 만드는 것입니다. App Service 계획은 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure 앱 서비스 계획 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * **구독을** **무료 평가판으로** 설정합니다(기존 구독을 사용할 수도 있음).
   * **리소스 그룹의**경우 **SpeechEchoBot자습서-리소스 그룹을**선택합니다.
   * 리소스에 **이름을**지정합니다. 우리는 **SpeechEchoBot튜토리얼 - 앱 서비스 계획을 권장합니다**
   * **운영 체제의**경우 **Windows**를 선택합니다.
   * **리전의**경우 **미국 서부를**선택합니다.
   * **가격 책정 계층의**경우 **표준 S1이** 선택되어 있는지 확인합니다. 기본값이어야 합니다. 그렇지 않은 경우 위에서 설명한 대로 **운영 체제를** **Windows로** 설정해야 합니다.
5. **검토 + 만들기**를 클릭합니다. 유효성 검사를 읽는 배너가 **표시됩니다.**
6. **만들기**를 클릭합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.

이 시점에서 리소스**그룹(SpeechEchoBot-ResourceGroup)에**두 가지 리소스가 있는지 확인합니다.

| 이름 | Type  | 위치 |
|------|-------|----------|
| 스피치에코봇튜토리얼-앱서비스플랜 | App Service 계획 | 미국 서부 |
| 스피치에코봇튜토리얼-스피치 | Cognitive Services | 미국 서부 |

## <a name="build-an-echo-bot"></a>에코 봇 빌드

이제 몇 가지 리소스를 만들었으니 봇을 빌드해 보겠습니다. 이름에서 알 수 있듯이 응답으로 입력한 텍스트를 에코하는 Echo Bot 샘플부터 시작해 보겠습니다. 걱정하지 마세요, 샘플 코드는 변경없이 사용할 준비가되어 있습니다. 봇을 Azure에 배포한 후 연결하는 직접 회선 음성 채널과 함께 작동하도록 구성됩니다.

> [!NOTE]
> 다음 지침과 에코 봇에 대한 추가 정보는 [GitHub의 샘플 README에서](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)확인할 수 있습니다.

### <a name="run-the-bot-sample-on-your-machine"></a>컴퓨터에서 봇 샘플 실행

1. 샘플 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Visual Studio를 시작합니다.
3. 도구 모음에서 **파일** > **열기** > **프로젝트/솔루션을**선택하고 Echo Bot 프로젝트 솔루션을 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 프로젝트가 로드된 후 <kbd>F5를</kbd> 눌러 프로젝트를 빌드하고 실행합니다.
5. 브라우저가 실행되어야하며 이와 유사한 화면이 표시됩니다.
    > [!div class="mx-imgBorder"]
    > [![에코봇 실행-온-로컬 호스트](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "로컬 호스트에서 실행되는 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>봇 프레임워크 에뮬레이터로 봇 샘플 테스트

[봇 프레임워크 에뮬레이터는](https://github.com/microsoft/botframework-emulator) 봇 개발자가 터널을 통해 로컬 또는 원격으로 봇을 테스트하고 디버깅할 수 있는 데스크톱 앱입니다. 에뮬레이터는 입력(음성이 아님)으로 입력된 텍스트를 지원합니다. 봇은 텍스트로 응답합니다. 다음 단계에 따라 Bot 프레임워크 에뮬레이터를 사용하여 텍스트 입력 및 텍스트 출력을 사용하여 로컬로 실행되는 Echo Bot을 테스트합니다. Azure에 봇을 배포한 후 음성 입력 및 음성 출력으로 테스트합니다.

1. 봇 [프레임워크 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 버전 4.3.0 이상 설치
2. 봇 프레임워크 에뮬레이터를 실행하고 봇을 엽니다.
   * **파일** -> **열기 봇**.
3. 봇의 URL을 입력합니다. 예를 들어:

   ```
   http://localhost:3978/api/messages
   ```
   을 누르면 "연결"을 누릅니다.
4. 봇은 즉시 "안녕하세요 환영합니다!"로 당신을 맞이해야합니다! 메시지가 표시됩니다. 모든 문자 메시지를 입력하고 봇에서 응답을 받는지 확인합니다.
5. 이것은 에코 봇 인스턴스와의 통신 교환이 어떻게 보일 지 모릅니다: [ ![봇 프레임워크 에뮬레이터](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "봇 프레임워크 에뮬레이터")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Azure 앱 서비스에 봇 배포

다음 단계는 에코 봇을 Azure에 배포하는 것입니다. 봇을 배포하는 방법에는 몇 가지가 있지만 이 자습서에서는 Visual Studio에서 직접 게시하는 데 중점을 둡니다.

> [!NOTE]
> 또는 [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) 및 배포 템플릿을 사용하여 [봇을 배포할](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)수 있습니다.

1. Visual Studio에서 직접 회선 음성 채널에서 사용하도록 구성된 에코 봇을 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 솔루션 **탐색기에서** **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시를 선택합니다...**
1. 게시 대상 **선택이라는** 새 창이 열립니다.
1. **Azure 서비스** 탐색에서 **앱 서비스를** 선택한 다음 **새 만들기를**선택한 다음 **프로필 만들기를**클릭합니다.
1. 앱 **서비스 만들기** 창이 나타나면 다음을 수행합니다.
   * **계정 추가를**클릭하고 Azure 계정 자격 증명으로 로그인합니다. 이미 로그인한 경우 드롭다운 목록에서 원하는 계정을 선택합니다.
   * 앱 **이름의**경우 봇에 대해 전역적으로 고유한 이름을 입력해야 합니다. 이 이름은 고유한 봇 URL을 만드는 데 사용됩니다. 기본값은 날짜와 시간을 포함하여 채워집니다(예: "EchoBot20190805125647"). 이 자습서의 기본 이름을 사용할 수 있습니다.
   * **구독의**경우 **무료 평가판으로** 설정합니다.
   * **리소스 그룹의**경우 **SpeechEchoBot자습서-리소스 그룹을** 선택합니다.
   * **호스팅 계획의**경우 **SpeechEchoBot자습서-앱 서비스플랜을** 선택합니다.
   * **응용 프로그램 인사이트를**보려면 **없음으로** 둡니다.
1. **만들기를 클릭합니다.**
1. Visual Studio에는 다음과 같은 성공 메시지가 표시됩니다.

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 기본 브라우저가 열리고 "봇이 준비되었습니다!"라는 페이지가 표시되어야 합니다.
1. 이 시점에서 Azure 포털에서 리소스 그룹 **SpeechEchoBot자습서-리소스 그룹을** 확인하고 세 가지 리소스가 있는지 확인합니다.

| 이름 | Type  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| 스피치에코봇튜토리얼-앱서비스플랜 | App Service 계획 | 미국 서부 |
| 스피치에코봇튜토리얼-스피치 | Cognitive Services | 미국 서부 |

## <a name="enable-web-sockets"></a>웹 소켓 사용

봇이 웹 소켓을 사용하여 직접 회선 음성 채널과 통신할 수 있도록 작은 구성을 변경해야 합니다. 다음 단계를 수행하여 웹 소켓을 사용하도록 설정합니다.

1. [Azure 포털로](https://portal.azure.com)이동하여 앱 서비스를 찾습니다. 리소스의 이름은 **EchoBot20190805125647(고유한** 앱 이름)과 유사해야 합니다.
2. Azure **서비스** 탐색에서 **설정에서** **구성**을 클릭합니다.
3. 일반 **설정** 탭을 선택합니다.
4. **웹 소켓의** 토글을 찾아 **설정 합니다.**
5. **저장**을 클릭합니다.

> [!TIP]
> Azure 앱 서비스 페이지 상단의 컨트롤을 사용하여 서비스를 중지하거나 다시 시작할 수 있습니다. 이 문제는 문제 해결 시 유용할 수 있습니다.

## <a name="create-a-channel-registration"></a>채널 등록 만들기

이제 봇을 호스트하는 Azure 앱 서비스를 만들었으니 다음 단계는 **봇 채널 등록을**만드는 것입니다. 채널 등록을 만드는 것은 직접 회선 음성 채널을 포함하여 봇 프레임워크 채널에 봇을 등록하기 위한 전제 조건입니다.

> [!NOTE]
> 봇이 채널을 활용하는 방법에 대해 자세히 알아보려면 [봇을 채널에 연결하세요.](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure 봇 채널 등록 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 다음과 같은 정보를 제공하라는 메시지가 표시됩니다.
   * **봇 핸들의**경우 **SpeechEchoBot자습서-봇 등록을**입력합니다.
   * **구독의**경우 **무료 평가판을**선택합니다.
   * **리소스 그룹의**경우 **SpeechEchoBot자습서-리소스 그룹을**선택합니다.
   * **위치의**경우 **미국 서부를**선택합니다.
     * **가격 책정 계층의**경우 **F0을**선택합니다.
     * **메시징 끝점의**경우 끝에 경로가 추가된 웹 앱의 `/api/messages` URL을 입력합니다. 예를 들어 전 세계적으로 고유한 앱 이름이 **EchoBot20190805125647인**경우 메시징 `https://EchoBot20190805125647.azurewebsites.net/api/messages/`끝점은 다음과 같은 것입니다.
     * **응용 프로그램 인사이트를**보려면 이 것을 **해제로**설정할 수 있습니다. 자세한 내용은 [봇 분석을](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)참조하십시오.
     * **자동 만들기 앱 ID 및 암호**무시 .
5. **봇 채널 등록** 블레이드 하단에서 **만들기를**클릭합니다.

이 시점에서 Azure 포털에서 리소스 그룹 **SpeechEchoBot자습서-리소스 그룹을** 확인합니다. 이제 네 가지 리소스가 표시됩니다.

| 이름 | Type  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| 스피치에코봇튜토리얼-앱서비스플랜 | App Service 계획 | 미국 서부 |
| 스피치에코봇튜토리얼-봇등록 | 봇 채널 등록 | 전역 |
| 스피치에코봇튜토리얼-스피치 | Cognitive Services | 미국 서부 |

> [!IMPORTANT]
> 미국 서부를 선택한 경우에도 봇 채널 등록 리소스에 글로벌 리전이 표시됩니다. 예상된 동작입니다.

## <a name="register-the-direct-line-speech-channel"></a>직접 회선 음성 채널 등록

이제 직접 회선 음성 채널에 봇을 등록할 차례입니다. 이 채널은 음성 SDK로 컴파일된 에코 봇과 클라이언트 앱 간의 연결을 만드는 데 사용됩니다.

1. [Azure 포털에서](https://portal.azure.com) **SpeechEchoBot자습서-봇등록** 리소스를 찾아 엽니다.
1. Azure **서비스** 탐색에서 **채널을**선택합니다.
   * 더 **많은 채널을**찾고 **직접 회선 음성을 찾아 클릭합니다.**
   * 페이지의 텍스트를 검토하여 **직접 회선 음성 구성을**한 다음 "인지 서비스 계정"이라는 드롭다운 메뉴를 확장합니다.
   * 메뉴에서 이전에 만든 음성 리소스(예: **SpeechEchoBot자습서 음성)를**선택하여 봇을 음성 구독 키에 연결합니다.
   * **저장**을 클릭합니다.

1. 봇 **관리** 탐색에서 **설정을**클릭합니다.
   * **스트리밍 끝점 사용**으로 레이블이 지정된 확인란을 선택합니다. 이는 봇과 직접 회선 음성 채널 간의 웹 소켓에 구축된 통신 프로토콜을 활성화하는 데 필요합니다.
   * **저장**을 클릭합니다.

> [!TIP]
> 자세한 내용을 보려면 [봇을 직접 회선 음성에 연결 을](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)참조하십시오. 이 페이지에는 추가 정보 및 알려진 문제가 포함되어 있습니다.

## <a name="build-the-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트 빌드

이 단계에서는 Windows 음성 도우미 클라이언트를 빌드합니다. 클라이언트는 [음성 SDK를](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) 사용하여 직접 회선 음성 채널을 사용하여 봇과의 통신을 관리하는 C#의 WPF(Windows 프레젠테이션 기반) 앱입니다. 사용자 지정 클라이언트 앱을 작성하기 전에 봇과 상호 작용하고 테스트하는 데 사용합니다.

Windows 음성 도우미 클라이언트에는 봇에 대한 연결을 구성하고, 텍스트 대화를 보고, JSON 형식으로 봇 프레임워크 활동을 보고, 적응형 카드를 표시할 수 있는 간단한 UI가 있습니다. 또한 사용자 지정 키워드의 사용을 지원합니다. 이 클라이언트를 사용하여 봇과 대화하고 음성 응답을 받을 수 있습니다.

계속 진행하기 전에 마이크와 스피커가 활성화되어 있는지 확인합니다.

1. [Windows 음성 도우미 클라이언트의](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)GitHub 리포지토리로 이동합니다.
2. 리포지토리를 복제하고, 프로젝트를 빌드하고, 클라이언트를 구성하고, 클라이언트를 시작하는 데 제공된 지침을 따릅니다.
3. **다시 연결을** 클릭하고 마이크 버튼을 누르는 메시지가 표시되는지 **확인하거나 봇과 대화를 시작하려면 입력합니다.**
4. 그것을 테스트 해 봅시다. 마이크 버튼을 클릭하고 영어로 몇 단어를 말한다. 당신이 말하는 대로 인식 된 텍스트가 나타납니다. 당신이 말하기를 끝나면, 봇은 자신의 목소리로 응답할 것입니다, "에코"를 말하고 인식 된 단어 다음에.
5. 텍스트를 사용하여 봇과 통신할 수도 있습니다. 하단 막대의 텍스트를 입력하기만 하면 됩니다. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트에서 오류 문제 해결

기본 앱 창에서 오류 메시지가 발생하면 이 테이블을 사용하여 오류를 식별하고 문제를 해결합니다.

| Error | 어떻게 해야 하나요? |
|-------|----------------------|
|오류 인증 실패: 인증 오류(401)로 웹소켓 업그레이드에 실패했습니다. 올바른 구독 키(또는 권한 부여 토큰) 및 지역 이름 확인| 앱의 설정 페이지에서 음성 구독 키와 해당 지역을 올바르게 입력했는지 확인합니다.<br>음성 키와 키 영역이 올바르게 입력되었는지 확인합니다. |
|오류 연결실패: 원격 호스트에 의해 연결이 닫혔습니다. 오류 코드: 1011. 오류 세부 정보: 메시지를 보내기 전에 봇에 연결할 수 없습니다. | ["스트리밍 엔드포인트 사용"](#register-the-direct-line-speech-channel) 상자 및/또는 전환된 웹 소켓을 켜기로 선택했는지 [ **확인합니다.** ](#enable-web-sockets)<br>Azure 앱 서비스가 실행 되고 있는지 확인합니다. 이 경우 앱 서비스를 다시 시작해 보십시오.|
|오류 연결실패: 원격 호스트에 의해 연결이 닫혔습니다. 오류 코드: 1011. 오류 세부 정보: 응답 상태 코드는 성공을 나타내지 않습니다: 500 (InternalServerError)| 봇은 출력 활동 [말하기](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 필드에 신경망 음성을 지정했지만 음성 구독 키와 연결된 Azure 영역은 신경 음성을 지원하지 않습니다. [표준 및 신경 음성을](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)참조하십시오.|
|오류 연결실패: 원격 호스트에 의해 연결이 닫혔습니다. 오류 코드: 1000. 오류 세부 정보: 최대 웹 소켓 연결 유휴 기간 초과(> 300000ms)| 채널에 대한 연결이 5분 이상 열려 있고 비활성 상태로 남아 있을 때 예상되는 오류입니다. |

테이블에서 문제가 해결되지 않으면 [음성 도우미: 자주 묻는 질문을](faq-voice-assistants.md)참조하십시오.

### <a name="view-bot-activities"></a>봇 활동 보기

모든 봇은 **활동** 메시지를 보내고 받습니다. Windows 음성 도우미 클라이언트의 **활동 로그** 창에는 클라이언트가 봇에서 받은 각 활동이 있는 타임스탬프가 있는 로그가 표시됩니다. [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) 메서드를 사용하여 클라이언트가 봇에 보낸 활동을 볼 수도 있습니다. 로그 항목을 선택하면 연관된 활동의 세부 정보가 JSON으로 표시됩니다.

다음은 클라이언트가 받은 활동의 샘플 json입니다.

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON 출력에서 반환되는 내용에 대해 자세히 알아보려면 [활동의 필드를](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)참조하십시오. 이 자습서에서는 [텍스트](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) 및 [말하기](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 필드에 집중할 수 있습니다.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>음성 SDK에 대한 호출에 대한 클라이언트 소스 코드 보기

Windows 음성 도우미 클라이언트는 음성 SDK를 포함하는 NuGet 패키지 [Microsoft.CognitiveServices.Speech를](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)사용합니다. 샘플 코드 검토를 시작하는 좋은 장소는 파일의 [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)InitSpeechConnector() 메서드로, 이 두 개의 Speech SDK 개체를 만듭니다.
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- 구성 설정(예: 음성 구독 키, 키 영역)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- 인식 된 음성 및 봇 응답을 처리하기위한 채널 연결 및 클라이언트 구독 이벤트를 관리합니다.

## <a name="add-custom-keyword-activation"></a>사용자 지정 키워드 활성화 추가

음성 SDK는 사용자 지정 키워드 활성화를 지원합니다. 마이크로소프트의 도우미에 대 한 "안녕 Cortana"와 마찬가지로, 당신은 지속적으로 당신의 선택의 키워드를 듣고 응용 프로그램을 쓸 수 있습니다. 키워드는 단일 단어 또는 다중 단어 구가 될 수 있습니다.

> [!NOTE]
> *키워드라는* 용어는 *절전 모드 해제 단어와*같은 의미로 사용되는 경우가 많으며 Microsoft 설명서에서 둘 다 사용되는 것을 볼 수 있습니다.

키워드 검색은 클라이언트 앱에서 수행됩니다. 키워드를 사용하는 경우 키워드가 검색된 경우에만 오디오가 직접 회선 음성 채널로 스트리밍됩니다. 직접 회선 음성 채널에는 *KWV(키워드 확인)라는*구성 요소가 포함되어 있으며, 클라우드에서 선택한 키워드가 오디오 스트림의 시작 부분에 있는지 확인하기 위해 보다 복잡한 처리를 수행합니다. 키워드 확인에 성공하면 채널이 봇과 통신합니다.

다음 단계에 따라 키워드 모델을 만들고, 이 모델을 사용하도록 Windows Voice Assistant 클라이언트를 구성하고, 마지막으로 봇으로 테스트합니다.

1. 이 지침에 따라 [Speech 서비스를 사용하여 사용자 지정 키워드를 만듭니다.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
2. 이전 단계에서 다운로드한 모델 파일의 압축을 해제합니다. 키워드의 이름을 지정해야 합니다. `kws.table`.
3. Windows 음성 도우미 클라이언트에서 **설정** 메뉴를 찾습니다(오른쪽 상단에 있는 기어 아이콘을 찾습니다). **모델 파일 경로를** 찾아 2단계에서 파일의 `kws.table` 전체 경로 이름을 입력합니다.
4. **사용 이라고**레이블이 지정 된 확인 란을 선택 합니다. 확인란 옆에 "다음 연결 시 키워드를 듣습니다"라는 메시지가 표시됩니다. 잘못된 파일이나 잘못된 경로를 제공한 경우 오류 메시지가 표시됩니다.
5. 음성 **구독 키,** **구독 키 영역을**입력한 다음 **확인을** 클릭하여 **설정** 메뉴를 닫습니다.
6. 다시 연결 을 **클릭합니다.** "새 대화가 시작되었습니다 - 마이크 버튼을 입력하거나 누르거나 키워드를 말하십시오"라는 메시지가 표시됩니다. 응용 프로그램은 지금 지속적으로 듣고있다.
7. 키워드로 시작하는 모든 구문을 말하십시오. 예를 들어 "**{키워드}** 몇 시입니까?". 키워드를 발언한 후 일시 중지할 필요가 없습니다. 작업이 완료되면 다음 두 가지 상황이 발생합니다.
   * 당신은 당신이 말한의 기록을 볼 수 있습니다
   * 얼마 지나지 않아 봇의 반응을 듣게 됩니다.
8. 봇이 지원하는 세 가지 입력 유형을 계속 실험해 보십시오.
   * 하단 막대에 텍스트 입력
   * 마이크 아이콘을 누르고 말하기
   * 키워드로 시작하는 문구 말하기

### <a name="view-the-source-code-that-enables-keyword"></a>키워드를 활성화하는 소스 코드 보기

Windows 음성 도우미 클라이언트 소스 코드에서 이러한 파일을 살펴보고 키워드 검색을 활성화하는 데 사용되는 코드를 검토합니다.

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)에는 디스크의 로컬 파일에서 [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)모델을 인스턴스화하는 데 사용되는 Speech SDK 메서드에 대한 호출이 포함됩니다.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)에는 연속 키워드 검색을 활성화하는 음성 SDK 메서드호출이 [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)포함되어 있습니다.

## <a name="optional-change-the-language-and-bot-voice"></a>(선택 사항) 언어 및 봇 음성 변경

당신이 만든 봇은 듣고 기본적으로 미국 영어 텍스트 음성으로, 영어로 응답합니다. 그러나 영어 또는 기본 음성을 사용하는 데만 국한되지 는 않습니다. 이 섹션에서는 봇이 듣고 응답할 언어를 변경하는 방법을 배웁니다. 또한 해당 언어에 대해 다른 음성을 선택하는 방법도 배웁니다.

### <a name="change-the-language"></a>언어 변경

[음성-텍스트](language-support.md#speech-to-text) 테이블에 언급된 언어 중 하나를 선택할 수 있습니다. 아래 예제에서는 언어를 독일어로 변경합니다.

1. Windows 음성 도우미 클라이언트 앱을 열고 설정 단추(오른쪽 상단 기어 `de-de` 아이콘)를 클릭하고 언어 필드(음성-텍스트 테이블에 언급된 로캘 값)를 [입력합니다.](language-support.md#speech-to-text) 이렇게 하면 음성 언어가 인식되고 기본값입니다. `en-us` 또한 직접 회선 음성 채널에 봇 응답에 대한 기본 독일어 음성을 사용하도록 지시합니다.
2. 설정 페이지를 닫고 다시 연결 버튼을 클릭하여 에코 봇에 대한 새 연결을 설정합니다.
3. 마이크 버튼을 클릭하고 독일어로 구를 말한다. 인식된 텍스트와 기본 독일어 음성으로 응답하는 에코 봇이 표시됩니다.

### <a name="change-the-default-bot-voice"></a>기본 봇 음성 변경

텍스트 음성 음성을 선택하고 발음을 제어하는 것은 Bot이 간단한 텍스트 대신 [음성 합성 마크업](speech-synthesis-markup.md) 언어(SSML) 형태로 응답을 지정하는 경우 수행할 수 있습니다. 에코 봇은 SSML을 사용하지 않지만 이를 위해 코드를 쉽게 수정할 수 있습니다. 아래 예제에서는 에코 봇 응답에 SSML을 추가하여 독일의 음성 스테판 아폴로(남성 음성)가 기본 여성 음성 대신 사용되도록 합니다. 해당 언어에 지원되는 [표준 음성](language-support.md#standard-voices) 및 [신경 음성](language-support.md#neural-voices) 목록을 참조하십시오.

1. 시작부터 열어 `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`보겠습니다.
2. 다음 두 줄을 찾습니다.
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 다음으로 교체하십시오.
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Visual Studio에서 솔루션을 빌드하고 빌드 오류를 수정합니다.

'MessageFactory.Text' 메서드의 두 번째 인수는 봇 회신에서 [활동 말하기 필드를](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 설정합니다. 위의 변경으로 기본이 아닌 독일어 음성을 지정하기 위해 단순 텍스트에서 SSML로 대체되었습니다.

### <a name="redeploy-your-bot"></a>봇 재배포

이제 봇에 필요한 변경을 수행했습니다, 다음 단계는 Azure 앱 서비스에 다시 게시하고 그것을 밖으로 시도하는 것입니다 :

1. 솔루션 탐색기 창에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시를**선택합니다.
2. 이전 배포 구성이 이미 기본값으로 로드되었습니다. **EchoBot20190805125647 - 웹 배포**옆에 **게시를** 클릭하기만 하면 됩니다.
3. **성공한 게시** 메시지가 Visual Studio 출력 창에 나타나고 "봇이 준비되었습니다!"라는 메시지와 함께 웹 페이지가 시작됩니다.
4. Windows 음성 도우미 클라이언트 앱을 열고 설정 버튼(오른쪽 상단 기어 아이콘)을 `de-de` 클릭하고 언어 필드에 여전히 있는지 확인합니다.
5. Windows 음성 [도우미 클라이언트 빌드의](#build-the-windows-voice-assistant-client) 지침에 따라 새로 배포된 봇과 다시 연결하고, 새 언어로 말하고, 새 음성으로 해당 언어로 봇의 회신을 듣습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 배포된 에코 봇을 계속 사용하지 않을 경우 Azure 리소스 그룹 **SpeechEchoBot-ResourceGroup을**삭제하기만 하면 해당 리소스 및 연결된 모든 Azure 리소스를 제거할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com) **Azure 서비스** 탐색에서 리소스 **그룹을 클릭합니다.**
2. 라는 리소스 그룹을 **찾습니다.** 세 개의 점(...)을 클릭합니다.
3. **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 SDK로 사용자 고유의 클라이언트 앱 빌드](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>참조

* 봇 응답 시간 개선을 보려면 [가까운 Azure 지역에](https://azure.microsoft.com/global-infrastructure/locations/) 배포
* [고품질 신경TTS 음성을 지원하는 Azure 지역에](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices) 배포
* 다이렉트 라인 스피치 채널과 관련된 가격:
  * [Bot Service 가격 책정](https://azure.microsoft.com/pricing/details/bot-service/)
  * [음성 서비스](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 사용자 고유의 음성 지원 봇 빌드 및 배포:
  * 봇 [프레임워크 봇을](https://dev.botframework.com/)빌드합니다. [직접 라인 음성 채널에](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) 등록하고 [음성에 대한 봇을 사용자 정의](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * 기존 [봇 프레임워크 솔루션](https://microsoft.github.io/botframework-solutions/index)살펴보기 : [가상 도우미를](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) 구축하고 [직접 회선 음성으로 확장](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
