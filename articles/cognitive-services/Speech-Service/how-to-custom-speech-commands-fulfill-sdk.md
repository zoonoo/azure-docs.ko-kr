---
title: 음성 SDK를 사용하여 클라이언트에서 명령을 수행하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 음성 SDK를 사용하여 클라이언트에서 사용자 지정 명령 작업을 처리하는 방법을 설명합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367752"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>음성 SDK(미리 보기)를 통해 클라이언트에서 명령 이행

사용자 지정 명령 응용 프로그램을 사용하여 작업을 완료하려면 연결된 클라이언트 장치에 사용자 지정 페이로드를 보낼 수 있습니다.

이 문서에서는 다음과 같은 작업을 수행합니다.

- 사용자 지정 명령 응용 프로그램에서 사용자 지정 JSON 페이로드 정의 및 전송
- C# UWP 음성 SDK 클라이언트 응용 프로그램에서 사용자 지정 JSON 페이로드 콘텐츠를 수신하고 시각화

## <a name="prerequisites"></a>사전 요구 사항

- [비주얼 스튜디오 2019](https://visualstudio.microsoft.com/downloads/)
- 음성 서비스에 대한 Azure 구독 키
  - [무료로 하나를 얻거나](get-started.md) Azure [포털에서](https://portal.azure.com) 만들 수 있습니다.
- 이전에 만든 사용자 지정 명령 앱
  - [빠른 시작: 매개 변수를 사용하여 사용자 지정 명령 만들기(미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
- 음성 SDK 지원 클라이언트 응용 프로그램
  - [빠른 시작: 음성 SDK(미리 보기)를 사용하여 사용자 지정 명령 응용 프로그램에 연결](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>선택 사항: 빠르게 시작하기

이 문서에서는 사용자 지정 명령 응용 프로그램과 대화할 클라이언트 응용 프로그램을 만드는 방법을 단계별로 설명합니다. 바로 다이빙을 하려는 경우 이 문서에서 사용할 수 있는 컴파일 즉시 사용할 수 있는 완전 한 소스 코드는 [음성 SDK 샘플에서](https://aka.ms/csspeech/samples)사용할 수 있습니다.

## <a name="fulfill-with-json-payload"></a>JSON 페이로드로 이행

1. [음성 스튜디오에서](https://speech.microsoft.com/) 이전에 만든 사용자 지정 명령 응용 프로그램을 엽니다.
1. 완료 **규칙** 섹션을 확인하여 사용자에게 다시 응답하는 이전에 만든 규칙이 있는지 확인합니다.
1. 페이로드를 클라이언트에 직접 보내려면 활동 보내기 작업을 사용하여 새 규칙을 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![활동 완료 규칙 보내기](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 규칙 이름 | 업데이트디바이스상태 | 규칙의 목적을 설명하는 이름 |
   | 조건 | 필수 매개 `OnOff` 변수 - 및`SubjectDevice` | 규칙을 실행할 수 있는 시기를 결정하는 조건 |
   | 동작 | `SendActivity`(아래 참조) | 규칙 조건이 true일 때 취할 작업 |

   > [!div class="mx-imgBorder"]
   > ![활동 페이로드 보내기](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>장치 켜기 또는 끄기 상태에 대한 시각적 개체 만들기

[빠른 시작: 음성 SDK(미리 보기)를 사용하여 사용자 지정 명령 응용 프로그램에 연결하여](./quickstart-custom-speech-commands-speech-sdk.md) `turn on the tv`. `turn off the fan` 이제 이러한 명령의 결과를 볼 수 있도록 몇 가지 시각적 개체를 추가합니다.

다음 XML을 사용하여 **켜기** 또는 **끄기를** 나타내는 텍스트가 있는 레이블이 지정된 상자를 추가합니다.`MainPage.xaml.cs`

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

이제 JSON 페이로드를 만들었으니 [JSON.NET](https://www.newtonsoft.com/json) 라이브러리에 대한 참조를 추가하여 직렬화를 처리할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![활동 페이로드 보내기](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

이벤트 `InitializeDialogServiceConnector` 처리기에 다음을 추가합니다. `ActivityReceived` 추가 코드는 활동에서 페이로드를 추출하고 그에 따라 TV 또는 팬의 시각적 상태를 변경합니다.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>체험

1. 애플리케이션 시작
1. 마이크 활성화 선택
1. 통화 버튼 선택
1. 말할`turn on the tv`
1. TV의 시각적 상태가 "켜기"로 변경되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 사용자 지정 명령 매개 변수에 유효성 검사를 추가(미리 보기)](./how-to-custom-speech-commands-validations.md)
