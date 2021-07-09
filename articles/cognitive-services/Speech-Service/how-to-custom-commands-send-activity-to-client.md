---
title: 클라이언트 애플리케이션에 사용자 지정 명령 작업 보내기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 애플리케이션에서 Speech SDK를 실행하는 클라이언트 애플리케이션으로 작업을 보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725101"
---
# <a name="send-custom-commands-activity-to-client-application"></a>클라이언트 애플리케이션에 사용자 지정 명령 작업 보내기

이 문서에서는 사용자 지정 명령 애플리케이션에서 Speech SDK를 실행하는 클라이언트 애플리케이션으로 작업을 보내는 방법에 대해 알아봅니다.

다음 작업을 완료합니다.

- 사용자 지정 명령 애플리케이션에서 사용자 지정 JSON 페이로드 정의 및 보내기
- C# UWP Speech SDK 클라이언트 애플리케이션에서 사용자 지정 JSON 페이로드 콘텐츠 수신 및 시각화

## <a name="prerequisites"></a>필수 조건
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 이상. 이 가이드에서는 Visual Studio 2019를 사용합니다.
> * Speech Service에 대한 Azure 구독 키: [Azure Portal](https://portal.azure.com)에서 [체험 계정을 가져오거나](overview.md#try-the-speech-service-for-free) 새로 만듭니다.
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)
> * Speech SDK 지원 클라이언트 앱: [방법: Speech SDK를 사용하여 클라이언트 애플리케이션과 통합](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>클라이언트에 작업 보내기 설정 
1. 이전에 만든 사용자 지정 명령 애플리케이션을 엽니다.
1. **TurnOnOff** 명령을 선택하고 완료 규칙 아래에서 **ConfirmationResponse** 를 선택한 다음 **작업 추가** 를 선택합니다.
1. **새 작업-형식** 에서 **클라이언트에 작업 보내기** 를 선택합니다.
1. 아래 JSON을 **작업 콘텐츠** 에 복사합니다.
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. **저장** 을 클릭하여 작업 보내기 작업, **학습** 및 변경 사항 **게시** 가 포함된 새 규칙을 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![작업 보내기 완료 규칙](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>클라이언트 애플리케이션과 통합

[방법: Speech SDK를 사용하여 클라이언트 애플리케이션 설정(미리 보기)](./how-to-custom-commands-setup-speech-sdk.md)에서 Speech SDK를 사용하여 `turn on the tv`, `turn off the fan`과 같은 명령을 처리하는 UWP 클라이언트 애플리케이션을 만들었습니다. 일부 시각적 개체를 추가하면 해당 명령의 결과를 볼 수 있습니다.

**켜짐** 또는 **꺼짐** 을 나타내는 텍스트 레이블이 있는 상자를 추가하려면 StackPanel의 다음 XML 블록을 `MainPage.xaml`에 추가합니다.

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

JSON 페이로드를 만들었으므로 역직렬화를 처리하려면 [JSON.NET](https://www.newtonsoft.com/json) 라이브러리에 대한 참조를 추가해야 합니다.

1. 솔루션을 마우스 오른쪽 단추로 클릭합니다.
1. **솔루션용 NuGet 패키지 관리** 를 선택하고 **찾아보기** 를 선택합니다. 
1. **Newtonsoft.json** 을 이미 설치한 경우 버전이 12.0.3 이상인지 확인합니다. 그렇지 않은 경우 **솔루션용 NuGet 패키지 관리 - 업데이트** 로 이동하고 **Newtonsoft.json** 을 검색하여 업데이트합니다. 이 가이드에서는 버전 12.0.3을 사용합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 보내기 페이로드](media/custom-commands/send-activity-to-client-json-nuget.png)

1. 또한 NuGet 패키지 **Microsoft.NETCore.UniversalWindowsPlatform** 이 6.2.10 이상인지 확인합니다. 이 가이드에서는 버전 6.2.10을 사용합니다.

'MainPage.xaml.cs'에서 다음을 추가합니다.

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>수신한 페이로드 처리

`InitializeDialogServiceConnector`에서`ActivityReceived` 이벤트 처리기를 다음 코드로 바꿉니다. 수정된 `ActivityReceived` 이벤트 처리기는 작업에서 페이로드를 추출하고 각각 TV 또는 환풍기의 시각적 상태를 변경합니다.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

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
1. TV의 시각적 상태가 "켜기"로 변경됩니다.
   > [!div class="mx-imgBorder"]
   > ![TV의 시각적 상태가 켜져 있음을 보여 주는 스크린샷](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 웹 엔드포인트 설정](./how-to-custom-commands-setup-web-endpoints.md)
