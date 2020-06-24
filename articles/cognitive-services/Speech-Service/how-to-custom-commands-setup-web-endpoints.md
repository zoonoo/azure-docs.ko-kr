---
title: 웹 끝점 설정 (미리 보기)
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명령의 웹 끝점 설정
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5bdb77d27b01f576ca06aa5b6d3df0572b3b1ea6
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308067"
---
# <a name="set-up-web-endpoints"></a>웹 끝점 설정

이 문서에서는 클라이언트 응용 프로그램에서 HTTP 요청을 수행할 수 있도록 하는 사용자 지정 명령 응용 프로그램에서 웹 끝점을 설정 하는 방법에 대해 알아봅니다. 다음 작업을 완료 합니다.

- 사용자 지정 명령 응용 프로그램에서 웹 끝점 설정
- 사용자 지정 명령 응용 프로그램에서 웹 끝점 호출
- 웹 끝점 응답 수신 
- 사용자 지정 JSON 페이로드에 웹 끝점 응답을 통합 하 고, c # UWP Speech SDK 클라이언트 응용 프로그램에서이를 보내고 시각화 합니다.

## <a name="prerequisites"></a>필수 구성 요소
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 음성 서비스에 대 한 Azure 구독 키: [무료로 하나 얻기](get-started.md) 또는 [Azure Portal](https://portal.azure.com) 에 만들기
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)
> * 음성 SDK 사용 클라이언트 앱: [방법: 클라이언트 응용 프로그램에 대 한 작업 종료](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>웹 끝점 설정

1. 이전에 만든 사용자 지정 명령 응용 프로그램을 엽니다. 
1. "웹 끝점"으로 이동 하 여 "새 웹 끝점"을 클릭 합니다.

   > [!div class="mx-imgBorder"]
   > ![새 웹 끝점](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 설정 | 제안 값 | Description |
   | ------- | --------------- | ----------- |
   | Name | UpdateDeviceState | 웹 끝점의 이름입니다. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | 사용자 지정 명령 앱에서 통신할 끝점의 URL입니다. |
   | 메서드 | POST | 끝점과 함께 허용 되는 상호 작용 (예: GET, POST)입니다.|
   | 헤더 | 키: 앱, 값: 앱의 고유한 이름 | 요청 헤더에 포함할 헤더 매개 변수입니다.|

    > [!NOTE]
    > - Tv 및 팬의 장치 상태를 저장 하는 데이터베이스와 후크 하는 [Azure Function](https://docs.microsoft.com/azure/azure-functions/)을 사용 하 여 만든 웹 끝점 예제
    > - 제안 된 헤더는 예제 끝점에만 필요 합니다.
    > - 실제 세계에서 웹 끝점은 장치를 관리 하는 [IOT hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) 에 대 한 끝점이 될 수 있습니다.

1. **저장**을 클릭합니다.

## <a name="call-web-endpoints"></a>웹 끝점 호출

1. 처리 되지 않은 **명령으로 이동 하 여** 완료 규칙 아래에서 **ConfirmationResponse** 을 선택한 다음 **작업 추가**를 선택 합니다.
1. **새 작업-형식**에서 **호출 웹 끝점** 을 선택 합니다.
1. **작업 편집-끝점**에서 만든 웹 엔드포인트 인 **updatedevicestate**를 선택 합니다.  
1. **구성**에서 다음 값을 입력 합니다. 
   > [!div class="mx-imgBorder"]
   > ![웹 끝점 동작 매개 변수 호출](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 설정 | 제안 값 | Description |
   | ------- | --------------- | ----------- |
   | 엔드포인트 | UpdateDeviceState | 이 작업에서 호출 하려는 웹 끝점입니다. |
   | 쿼리 매개 변수 | 항목 = {#, 장치} &&값 = {OnOff} | 웹 끝점 URL에 추가할 쿼리 매개 변수입니다.  |
   | 본문 내용 | 해당 없음 | 요청의 본문 콘텐츠입니다. |

    > [!NOTE]
    > - 제안 된 쿼리 매개 변수는 예제 끝점에만 필요 합니다.

1. **성공 시-실행 작업**에서 **음성 응답 보내기**를 선택 합니다.
   
   > [!div class="mx-imgBorder"]
   > ![성공 시 웹 끝점 호출 동작](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 설정 | 제안 값 | Description |
   | ------- | --------------- | ----------- |
   | 실행할 작업 | 음성 응답 보내기 | 웹 끝점에 대 한 요청이 성공 하는 경우 실행할 작업입니다. |
   
   > [!NOTE]
   > - 을 사용 하 여 http 응답의 필드에 직접 액세스할 수도 있습니다 `{YourWebEndpointName.FieldName}` . 예: `{UpdateDeviceState.TV}`

1. **실패 시-실행 작업**에서 **음성 응답 보내기** 를 선택 합니다.
   > [!div class="mx-imgBorder"]
   > ![Fail에서 웹 끝점 호출 동작](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 설정 | 제안 값 | Description |
   | ------- | --------------- | ----------- |
   | 실행할 작업 | 음성 응답 보내기 | 웹 끝점에 대 한 요청이 실패 하는 경우 실행할 작업입니다. |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}`는 선택 사항입니다. 오류 메시지를 노출 하지 않으려는 경우에는이를 자유롭게 제거할 수 있습니다.
   > - 예제 끝점 내에서 헤더 매개 변수 누락 등의 일반적인 오류에 대 한 자세한 오류 메시지와 함께 http 응답을 다시 보냅니다. 

### <a name="try-it-out-in-test-portal"></a>테스트 포털에서 사용해 보기
- 성공 시 응답 \
저장, 학습 및 테스트
   > [!div class="mx-imgBorder"]
   > ![성공 시 웹 끝점 호출 동작](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 실패 시 응답 \
쿼리 매개 변수 중 하나를 제거 하 고 저장, 다시 학습 및 테스트 합니다.
   > [!div class="mx-imgBorder"]
   > ![성공 시 웹 끝점 호출 동작](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>클라이언트 응용 프로그램과 통합

[방법: 클라이언트 응용 프로그램으로 작업 보내기 (미리 보기)](./how-to-custom-commands-send-activity-to-client.md)에서 **클라이언트 작업에 대 한 보내기** 작업을 추가 했습니다. **웹 끝점 호출** 동작이 성공 했는지 여부에 관계 없이 작업이 클라이언트 응용 프로그램에 전송 됩니다.
그러나 대부분의 경우 웹 끝점에 대 한 호출이 성공 하는 경우에만 작업을 클라이언트 응용 프로그램으로 전송 합니다. 이 예제에서 장치의 상태가 성공적으로 업데이트 되는 경우입니다.

1. 이전에 추가한 **클라이언트 작업에 대 한 보내기 작업을** 삭제 합니다.
1. 호출 웹 끝점 편집: 
    1. **구성**에서 **쿼리 매개 변수가** 인지 확인 합니다.`item={SubjectDevice}&&value={OnOff}`
    1. **성공 시**에서 작업을 **실행으로** 변경 하 여 **클라이언트에 작업 보내기**
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
    > ![성공 시 보내기 작업](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
이제 웹 끝점에 대 한 요청이 성공 하는 경우에만 작업을 클라이언트로 보냅니다.

### <a name="create-visuals-for-syncing-device-state"></a>장치 상태를 동기화 하기 위한 시각적 개체 만들기
블록 위에 다음 XML을 추가 합니다 `MainPage.xaml` `"EnableMicrophoneButton"` .

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>장치 상태 동기화 

에서 `MainPage.xaml.cs` 참조를 추가 `using Windows.Web.Http;` 합니다. `MainPage` 클래스에 다음 코드를 추가합니다. 이 메서드는 예제 끝점에 GET 요청을 보내고 앱에 대 한 현재 장치 상태를 추출 합니다. `<your_app_name>`사용자 지정 명령 웹 끝점의 **헤더** 에 사용 된 항목으로 변경 해야 합니다.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>기능 직접 사용해 보기

1. 애플리케이션 시작
1. 장치 상태 동기화를 클릭 합니다.
이전 섹션에서를 사용 하 여 앱을 테스트 `turn on tv` 한 경우 TV 쇼가 "설정"으로 표시 됩니다.
    > [!div class="mx-imgBorder"]
    > ![장치 상태 동기화](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. 마이크 사용을 선택 합니다.
1. 대화 단추를 선택 합니다.
1. 한다는`turn on the fan`
1. 팬의 시각적 상태가 "켜기"로 변경 됩니다.
    > [!div class="mx-imgBorder"]
    > ![팬 켜기](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 응용 프로그램에 CI/CD 프로세스를 사용 하도록 설정](./how-to-custom-commands-deploy-cicd.md)