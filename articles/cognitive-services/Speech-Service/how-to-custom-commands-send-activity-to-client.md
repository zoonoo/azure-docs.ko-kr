---
title: 사용자 지정 명령 작업을 클라이언트 응용 프로그램으로 보내기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에서 Speech SDK를 실행 하는 클라이언트 응용 프로그램으로 작업을 보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: fc62c87fd12457c60d3eb26cba6814aa1df76f87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839217"
---
# <a name="send-custom-commands-activity-to-client-application"></a>사용자 지정 명령 작업을 클라이언트 응용 프로그램으로 보내기

이 문서에서는 사용자 지정 명령 응용 프로그램에서 Speech SDK를 실행 하는 클라이언트 응용 프로그램으로 작업을 보내는 방법에 대해 알아봅니다.

다음 작업을 완료 합니다.

- 사용자 지정 명령 응용 프로그램에서 사용자 지정 JSON 페이로드 정의 및 보내기
- C # UWP Speech SDK 클라이언트 응용 프로그램에서 사용자 지정 JSON 페이로드 콘텐츠 수신 및 시각화

## <a name="prerequisites"></a>필수 구성 요소
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 이상. 이 가이드에서는 Visual Studio 2019을 사용 합니다.
> * Speech Service에 대한 Azure 구독 키: [Azure Portal](https://portal.azure.com)에서 [체험 계정을 가져오거나](overview.md#try-the-speech-service-for-free) 새로 만듭니다.
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)
> * 음성 SDK 사용 클라이언트 앱: [방법: 음성 sdk를 사용 하 여 클라이언트 응용 프로그램과 통합](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>클라이언트에 보내기 작업 설정 
1. 이전에 만든 사용자 지정 명령 응용 프로그램 열기
1. ConfirmationResponse **명령 선택** 에서 완료 규칙 아래 **ConfirmationResponse** 에 **있는 작업 추가** 를 선택 하 고 작업 추가를 선택 합니다.
1. **새 작업-형식**에서 **클라이언트에 작업 보내기** 를 선택 합니다.
1. 아래 JSON을 **활동 내용** 에 복사 합니다.
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. **저장** 을 클릭 하 여 작업 보내기 작업으로 새 규칙을 만들고, **학습** 하 고, 변경 내용을 **게시** 합니다.

   > [!div class="mx-imgBorder"]
   > ![작업 완료 규칙 보내기](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>클라이언트 애플리케이션과 통합

[방법: 음성 sdk (미리 보기)를 사용 하 여 클라이언트 응용 프로그램을 설치](./how-to-custom-commands-setup-speech-sdk.md)하는 경우,와 같은 명령을 처리 하는 speech sdk를 사용 하 여 UWP 클라이언트 응용 프로그램을 만들었습니다 `turn on the tv` `turn off the fan` . 일부 시각적 개체를 추가 하면 해당 명령의 결과를 볼 수 있습니다.

**설정** 또는 **해제**를 나타내는 텍스트가 있는 레이블이 지정 된 상자를 추가 하려면 StackPanel의 다음 XML 블록을에 추가 `MainPage.xaml` 합니다.

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>참조 라이브러리 추가

JSON 페이로드를 만들었으므로 [JSON.NET](https://www.newtonsoft.com/json) 라이브러리에 대 한 참조를 추가 하 여 deserialization을 처리 해야 합니다.

1. 솔루션을 마우스 오른쪽 단추로 클라이언트 합니다.
1. **솔루션용 NuGet 패키지 관리**를 선택 하 고 **찾아보기** 를 선택 합니다. 
1. **Newtonsoft.js**이미 설치 되어 있는 경우 버전은 12.0.3 이상 인지 확인 합니다. 그렇지 않은 경우 솔루션에 **대 한 NuGet 패키지 관리-업데이트**로 이동 하 여 **Newtonsoft.js에서** 업데이트를 검색 합니다. 이 가이드에서는 버전 12.0.3를 사용 합니다.

    > [!div class="mx-imgBorder"]
    > ![전송 작업 페이로드](media/custom-commands/send-activity-to-client-json-nuget.png)

1. 또한 NuGet 패키지 **microsoft.netcore.universalwindowsplatform** 가 적어도 6.2.10 인지 확인 합니다. 이 가이드에서는 버전 6.2.10를 사용 합니다.

' MainPage ', 추가

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>받은 페이로드를 처리 합니다.

에서 `InitializeDialogServiceConnector` `ActivityReceived` 이벤트 처리기를 다음 코드로 바꿉니다. 수정 된 `ActivityReceived` 이벤트 처리기는 활동에서 페이로드를 추출 하 고 tv 또는 팬의 시각적 상태를 각각 변경 합니다.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>기능 직접 사용해 보기

1. 애플리케이션 시작
1. [마이크 사용]을 선택합니다.
1. [대화] 단추를 선택합니다.
1. `turn on the tv`이라고 말합니다.
1. Tv의 시각적 상태가 "켜기"로 변경 됩니다.
   > [!div class="mx-imgBorder"]
   > ![T V의 시각적 상태가 이제 켜 지는 것을 보여 주는 스크린샷](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 웹 끝점 설정 (미리 보기)](./how-to-custom-commands-setup-web-endpoints.md)
