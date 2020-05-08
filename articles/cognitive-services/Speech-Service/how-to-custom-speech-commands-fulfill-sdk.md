---
title: Speech SDK를 사용 하 여 클라이언트에서 명령을 수행 하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Speech SDK를 사용 하 여 클라이언트에서 사용자 지정 명령 활동을 처리 하는 방법을 설명 합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871759"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>음성 SDK (미리 보기)를 사용 하 여 클라이언트에서 명령 충족

사용자 지정 명령 응용 프로그램을 사용 하 여 작업을 완료 하려면 연결 된 클라이언트 장치에 사용자 지정 페이로드를 보낼 수 있습니다.

이 문서에서는 다음을 수행 합니다.

- 사용자 지정 명령 응용 프로그램에서 사용자 지정 JSON 페이로드 정의 및 보내기
- C # UWP Speech SDK 클라이언트 응용 프로그램에서 사용자 지정 JSON 페이로드 콘텐츠 수신 및 시각화

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 음성 서비스에 대 한 Azure 구독 키: [무료로 하나 얻기](get-started.md) 또는 [Azure Portal](https://portal.azure.com) 에 만들기
> * 이전에 만든 사용자 지정 명령 앱: [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
> * 음성 SDK 사용 클라이언트 응용 프로그램: [빠른 시작: 음성 sdk를 사용 하 여 사용자 지정 명령 응용 프로그램에 연결 (미리 보기)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>선택 사항: 빠르게 시작

이 문서에서는 클라이언트 응용 프로그램이 사용자 지정 명령 응용 프로그램과 통신 하도록 만드는 방법을 단계별로 설명 합니다. 이 문서에서 사용 하는 완전 하 고 컴파일 가능한 소스 코드는 [음성 SDK 샘플](https://aka.ms/csspeech/samples)에서 바로 사용할 수 있습니다.

## <a name="fulfill-with-json-payload"></a>JSON 페이로드에 만족

1. 빠른 시작 [: 매개 변수를 사용 하 여 사용자 지정 명령 만들기](./quickstart-custom-speech-commands-create-parameters.md) 에서 이전에 만든 사용자 지정 명령 응용 프로그램을 엽니다.
1. **완료 규칙** 섹션에서 사용자에 게 다시 응답 하는 이전에 만든 규칙이 있는지 확인 합니다.
1. 클라이언트에 직접 페이로드를 보내려면 작업 보내기 작업을 사용 하 여 새 규칙을 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![작업 완료 규칙 보내기](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 규칙 이름 | UpdateDeviceState | 규칙의 용도를 설명 하는 이름입니다. |
   | 조건 | 필수 매개 변수 `OnOff` -및`SubjectDevice` | 규칙을 실행할 수 있는 시기를 결정 하는 조건 |
   | 동작 | `SendActivity`(아래 참조) | 규칙 조건이 참인 경우 수행할 동작입니다. |

1. 아래 JSON을 **활동 내용** 에 복사 합니다.
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![전송 작업 페이로드](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>장치 켜기 또는 끄기 상태에 대 한 시각적 개체 만들기

[빠른 시작: 음성 sdk를 사용 하 여 사용자 지정 명령 응용 프로그램에 연결](./quickstart-custom-speech-commands-speech-sdk.md),와 같은 `turn on the tv`명령을 처리 하는 음성 sdk 클라이언트 응용 `turn off the fan`프로그램을 만들었습니다. 일부 시각적 개체를 추가 하면 해당 명령의 결과를 볼 수 있습니다.

에 추가 된 다음 XML을 사용 하 여 텍스트를 **설정** 하거나 **해제** 하는 텍스트와 함께 레이블이 지정 된 상자를`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>사용자 지정 가능한 페이로드 처리
### <a name="add-reference-libraries"></a>참조 라이브러리 추가

JSON 페이로드를 만들었으므로 [JSON.NET](https://www.newtonsoft.com/json) 라이브러리에 대 한 참조를 추가 하 여 deserialization을 처리 해야 합니다.
- 솔루션을 마우스 오른쪽 단추로 클라이언트 합니다.
- **솔루션용 NuGet 패키지 관리**를 선택 하 고 **설치** 를 선택 합니다. 
- 업데이트 목록에서 **newtonsoft.json** 를 검색 하 고 **microsoft.netcore.universalwindowsplatform** 를 최신 버전으로 업데이트 합니다.

> [!div class="mx-imgBorder"]
> ![전송 작업 페이로드](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

' MainPage ', 추가
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>수신 페이로드 처리

에서 `InitializeDialogServiceConnector` `ActivityReceived` 이벤트 처리기를 다음 코드로 바꿉니다. 수정 `ActivityReceived` 된 이벤트 처리기는 활동에서 페이로드를 추출 하 고 그에 따라 tv 또는 팬의 시각적 상태를 변경 합니다.

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
1. 마이크 사용을 선택 합니다.
1. 대화 단추를 선택 합니다.
1. 한다는`turn on the tv`
1. Tv의 시각적 상태가 "켜기"로 변경 됩니다.
   > [!div class="mx-imgBorder"]
   > ![전송 작업 페이로드](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 사용자 지정 명령 매개 변수에 유효성 검사 추가 (미리 보기)](./how-to-custom-speech-commands-validations.md)
