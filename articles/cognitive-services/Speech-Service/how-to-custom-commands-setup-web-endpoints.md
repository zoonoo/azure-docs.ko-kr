---
title: 웹 엔드포인트 설정(미리 보기)
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명령에 대한 웹 엔드포인트를 설정합니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: e64c5ddfafb8bc8e9041e6d6b3e473a9a20565ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843127"
---
# <a name="set-up-web-endpoints"></a>웹 엔드포인트 설정

이 문서에서는 클라이언트 애플리케이션에서 HTTP 요청을 수행할 수 있는 사용자 지정 명령 애플리케이션에서 웹 엔드포인트를 설정하는 방법에 대해 설명합니다. 다음 작업을 완료합니다.

- 사용자 지정 명령 애플리케이션에서 웹 엔드포인트 설정
- 사용자 지정 명령 애플리케이션에서 웹 엔드포인트 호출
- 웹 엔드포인트 응답 수신 
- 웹 엔드포인트 응답을 사용자 지정 JSON 페이로드에 통합 및 C# UWP Speech SDK 클라이언트 애플리케이션에서 이를 보내기 및 시각화

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Speech Service에 대한 Azure 구독 키: [Azure Portal](https://portal.azure.com)에서 [체험 계정을 가져오거나](overview.md#try-the-speech-service-for-free) 새로 만듭니다.
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)
> * Speech SDK 사용 클라이언트 앱: [방법: 클라이언트 애플리케이션에 작업 보내기](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>웹 엔드포인트 설정

1. 이전에 만든 사용자 지정 명령 애플리케이션을 엽니다. 
1. "웹 엔드포인트"로 이동하여 "새 웹 엔드포인트"를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![새 웹 엔드포인트](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 이름 | UpdateDeviceState | 웹 엔드포인트의 이름입니다. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | 사용자 지정 명령 앱에서 통신할 엔드포인트의 URL입니다. |
   | 메서드 | POST | 엔드포인트에 허용되는 상호 작용(예: GET, POST)입니다.|
   | 헤더 | 키: 앱, 값: applicationId의 처음 8자리를 사용합니다. | 요청 헤더에 포함할 헤더 매개 변수입니다.|

    > [!NOTE]
    > - [Azure 함수](https://docs.microsoft.com/azure/azure-functions/)를 사용하여 만든 예제 웹 엔드포인트 - TV 및 팬의 디바이스 상태를 저장하는 데이터베이스와 연결됩니다.
    > - 제안된 헤더는 예제 엔드포인트에만 필요합니다.
    > - 헤더 값이 예제 엔드포인트에서 고유한지 확인하려면 applicationId의 처음 8자리를 사용합니다.
    > - 실제 환경에서 웹 엔드포인트는 디바이스를 관리하는 [IOT 허브](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)에 대한 엔드포인트일 수 있습니다.

1. **저장**을 클릭합니다.

## <a name="call-web-endpoints"></a>웹 엔드포인트 호출

1. **TurnOnOff** 명령으로 이동하여 완료 규칙 아래에서 **ConfirmationResponse**를 선택한 다음, **작업 추가**를 선택합니다.
1. **새 작업 - 유형** 아래에서 **웹 엔드포인트 호출**을 선택합니다.
1. **작업 편집 - 엔드포인트**에서 만든 웹 엔드포인트인 **UpdateDeviceState**를 선택합니다.  
1. **구성**에서 다음 값을 입력합니다. 
   > [!div class="mx-imgBorder"]
   > ![웹 엔드포인트 호출 작업 매개 변수](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 엔드포인트 | UpdateDeviceState | 이 작업에서 호출하려는 웹 엔드포인트입니다. |
   | 쿼리 매개 변수 | item={SubjectDevice}&&value={OnOff} | 웹 엔드포인트 URL에 추가할 쿼리 매개 변수입니다.  |
   | 본문 콘텐츠 | N/A | 요청의 본문 콘텐츠입니다. |

    > [!NOTE]
    > - 제안된 쿼리 매개 변수는 예제 엔드포인트에만 필요합니다.

1. **성공 시 - 실행할 작업**에서 **음성 응답 보내기**를 선택합니다.
    
    **간단한 편집기**에서 `{SubjectDevice} is {OnOff}`를 입력합니다.
   
   > [!div class="mx-imgBorder"]
   > ![성공 시-실행 화면 작업을 보여 주는 스크린샷](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 실행할 작업 | 음성 응답 보내기 | 웹 엔드포인트에 대한 요청이 성공하면 실행할 작업입니다. |
   
   > [!NOTE]
   > - `{YourWebEndpointName.FieldName}`을 사용하여 http 응답의 필드에 직접 액세스할 수도 있습니다. `{UpdateDeviceState.TV}`

1. **실패 시 - 실행할 작업**에서 **음성 응답 보내기**를 선택합니다.

    **간단한 편집기**에서 `Sorry, {WebEndpointErrorMessage}`를 입력합니다.

   > [!div class="mx-imgBorder"]
   > ![실패 시 웹 엔드포인트 호출 작업](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 실행할 작업 | 음성 응답 보내기 | 웹 엔드포인트에 대한 요청이 실패하면 실행할 작업입니다. |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}`는 선택 사항입니다. 오류 메시지를 공개하지 않으려면 이를 자유롭게 제거할 수 있습니다.
   > - 예제 엔드포인트 내에서 헤더 매개 변수 누락과 같은 일반적인 오류에 대한 자세한 오류 메시지와 함께 http 응답을 다시 보냅니다. 

### <a name="try-it-out-in-test-portal"></a>테스트 포털에서 사용해 보기
- 성공 시 응답
저장, 학습 및 테스트
   > [!div class="mx-imgBorder"]
   > ![성공 응답을 보여 주는 스크린샷](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 실패 시 응답
쿼리 매개 변수 중 하나 제거, 저장, 학습 및 테스트
   > [!div class="mx-imgBorder"]
   > ![성공 시 웹 엔드포인트 호출 작업](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>클라이언트 애플리케이션과 통합

[방법: 클라이언트 애플리케이션에 작업 보내기(미리 보기)](./how-to-custom-commands-send-activity-to-client.md)에서 **클라이언트에 작업 보내기** 작업을 추가했습니다. 작업은 **웹 엔드포인트 호출** 작업의 성공 여부에 관계없이 클라이언트 애플리케이션에 보내집니다.
그러나 대부분의 경우 웹 엔드포인트 호출이 성공하는 경우에만 작업을 클라이언트 애플리케이션에 보내려고 합니다. 다음 예제에서 이 작업은 디바이스 상태가 성공적으로 업데이트되는 경우입니다.

1. 이전에 추가한 **클라이언트에 작업 보내기** 작업을 삭제합니다.
1. 웹 엔드포인트 호출을 편집합니다. 
    1. **구성**에서 **쿼리 매개 변수**가 `item={SubjectDevice}&&value={OnOff}`인지 확인합니다.
    1. **성공 시**에서 **실행할 작업**을 **클라이언트에 작업 보내기**로 변경합니다.
    1. 아래 JSON을 **작업 콘텐츠**에 복사합니다.
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![성공 시 작업 보내기](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
이제 웹 엔드포인트에 대한 요청이 성공하는 경우에만 작업을 클라이언트에 보냅니다.

### <a name="create-visuals-for-syncing-device-state"></a>디바이스 상태 동기화를 위한 시각적 개체 만들기
다음 XML을 `MainPage.xaml`의 `"EnableMicrophoneButton"` 블록 위에 추가합니다.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>디바이스 상태 동기화 

`MainPage.xaml.cs`에서 `using Windows.Web.Http;`라는 참조를 추가합니다. `MainPage` 클래스에 다음 코드를 추가합니다. 이 메서드는 GET 요청을 예제 엔드포인트에 보내고 앱의 현재 디바이스 상태를 추출합니다. `<your_app_name>`을 사용자 지정 명령 웹 엔드포인트의 **헤더**에서 사용한 것으로 변경해야 합니다.

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
1. [디바이스 상태 동기화]를 클릭합니다.
이전 섹션에서 `turn on tv`를 사용하여 앱을 테스트한 경우 TV 쇼가 "켜기"로 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![디바이스 상태 동기화](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. [마이크 사용]을 선택합니다.
1. [대화] 단추를 선택합니다.
1. `turn on the fan`이라고 말합니다.
1. 팬의 시각적 상태가 "켜기"로 변경됩니다.
    > [!div class="mx-imgBorder"]
    > ![팬 켜기](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 애플리케이션에 CI/CD 프로세스 사용](./how-to-custom-commands-deploy-cicd.md)