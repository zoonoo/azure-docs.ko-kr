---
title: Speech SDK를 사용 하 여 클라이언트에서 사용자 지정 명령을 수행 하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Speech SDK를 사용 하 여 클라이언트에서 사용자 지정 명령 활동을 처리 하는 방법을 설명 합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: b55bb1c8379cf0a80a95aa0ba1a29297154d5831
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156508"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>방법: 음성 SDK를 사용 하 여 클라이언트에서 명령 수행 (미리 보기)

사용자 지정 명령 응용 프로그램을 사용 하 여 작업을 완료 하려면 연결 된 클라이언트 장치에 사용자 지정 페이로드를 보낼 수 있습니다.

이 문서에서는 다음을 수행 합니다.

- 사용자 지정 명령 응용 프로그램에서 사용자 지정 JSON 페이로드 정의 및 보내기
- C# UWP Speech SDK 클라이언트 응용 프로그램에서 사용자 지정 JSON 페이로드 콘텐츠 수신 및 시각화

## <a name="prerequisites"></a>필수 조건

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- 음성 서비스에 대 한 Azure 구독 키
  - [무료로 다운로드](get-started.md) 하거나 [Azure Portal](https://portal.azure.com) 에서 만드세요.
- 이전에 만든 사용자 지정 명령 앱
  - [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
- 음성 SDK 사용 클라이언트 응용 프로그램
  - [빠른 시작: 음성 SDK (미리 보기)를 사용 하 여 사용자 지정 명령 응용 프로그램에 연결](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>선택 사항: 빠르게 시작

이 문서에서는 클라이언트 응용 프로그램이 사용자 지정 명령 응용 프로그램과 통신 하도록 만드는 방법을 단계별로 설명 합니다. 이 문서에서 사용 하는 완전 하 고 컴파일 가능한 소스 코드는 [음성 SDK 샘플](https://aka.ms/csspeech/samples)에서 바로 사용할 수 있습니다.

## <a name="fulfill-with-json-payload"></a>JSON 페이로드에 만족

1. [Speech Studio](https://speech.microsoft.com/) 에서 이전에 만든 사용자 지정 명령 응용 프로그램 열기
1. **완료 규칙** 섹션에서 사용자에 게 다시 응답 하는 이전에 만든 규칙이 있는지 확인 합니다.
1. 클라이언트에 직접 페이로드를 보내려면 작업 보내기 작업을 사용 하 여 새 규칙을 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![보내기 작업 완료 규칙](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 설정 | 제안 값 | Description |
   | ------- | --------------- | ----------- |
   | 규칙 이름 | UpdateDeviceState | 규칙의 용도를 설명 하는 이름입니다. |
   | 조건 | 필수 매개 변수-`OnOff` 및 `SubjectDevice` | 규칙을 실행할 수 있는 시기를 결정 하는 조건 |
   | 작업 | `SendActivity` (아래 참조) | 규칙 조건이 참인 경우 수행할 동작입니다. |

   > [!div class="mx-imgBorder"]
   > ![전송 작업 페이로드](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>장치 켜기 또는 끄기 상태에 대 한 시각적 개체 만들기

[빠른 시작: 음성 sdk (미리 보기)를 사용 하 여 사용자 지정 명령 응용 프로그램에 연결](./quickstart-custom-speech-commands-speech-sdk.md) `turn on the tv`, `turn off the fan`등의 명령을 처리 하는 음성 sdk 클라이언트 응용 프로그램을 만들었습니다. 이제 일부 시각적 개체를 추가 하 여 해당 명령의 결과를 볼 수 있습니다.

에 추가 된 다음 XML을 사용 하 여 **설정** 하거나 **해제** 하는 텍스트를 사용 하 여 레이블 상자를 추가 `MainPage.xaml.cs`

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

이제 JSON 페이로드를 만들었으므로 [JSON.NET](https://www.newtonsoft.com/json) 라이브러리에 대 한 참조를 추가 하 여 deserialization을 처리할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![전송 작업 페이로드](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

`InitializeDialogServiceConnector`에서 `ActivityReceived` 이벤트 처리기에 다음을 추가 합니다. 추가 코드는 활동에서 페이로드를 추출 하 고 그에 따라 tv 또는 팬의 시각적 상태를 변경 합니다.

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

## <a name="try-it-out"></a>사용해 보기

1. 애플리케이션 시작
1. 마이크 사용을 선택 합니다.
1. 대화 단추를 선택 합니다.
1. 예 `turn on the tv`
1. Tv의 시각적 상태가 "켜기"로 변경 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 사용자 지정 명령 매개 변수에 유효성 검사 추가 (미리 보기)](./how-to-custom-speech-commands-validations.md)
