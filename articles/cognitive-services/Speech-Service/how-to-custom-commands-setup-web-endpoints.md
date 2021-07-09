---
title: 웹 엔드포인트 설정
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명령에 대한 웹 엔드포인트 설정
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725911"
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
> * 사용자 지정 명령 앱([사용자 지정 명령을 사용하여 음성 도우미 만들기](quickstart-custom-commands-application.md) 참조)
> * Speech SDK 지원 클라이언트 앱([Speech SDK를 사용하여 클라이언트 애플리케이션과 통합](how-to-custom-commands-setup-speech-sdk.md) 참조)

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Azure Function 앱을 사용하여 외부 웹 엔드포인트 배포

이 자습서의 경우 사용자 지정 명령 애플리케이션의 **TurnOnOff** 명령에서 설정하는 모든 디바이스에 대한 상태를 유지 관리하는 HTTP 엔드포인트가 필요합니다.

호출하려는 웹 엔드포인트가 이미 있는 경우 [다음 섹션](#setup-web-endpoints-in-custom-commands)으로 건너뜁니다. 또는 다음 섹션에서 이 섹션을 건너뛰려는 경우 사용할 수 있는 기본 호스팅된 웹 엔드포인트에 대한 세부 정보를 제공합니다.

### <a name="input-format-of-azure-function"></a>Azure 함수의 입력 형식

다음으로 [Azure Functions](../../azure-functions/index.yml)를 사용하여 엔드포인트를 배포합니다.
다음은 Azure 함수에 전달되는 사용자 지정 명령 이벤트의 형식입니다. Azure Function 앱을 작성하는 경우 이 정보를 사용합니다.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
다음 표에서는 이 입력의 주요 특성을 설명합니다.
        
| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 대화의 고유 식별자입니다. 클라이언트 앱에서 이 ID를 생성할 수 있습니다. |
| **currentCommand** | 대화에서 현재 활성화된 명령입니다. |
| **name** | 명령 이름입니다. `parameters` 특성은 매개 변수의 현재 값을 포함하는 맵입니다. |
| **currentGlobalParameters** | `parameters`와 같은 맵이지만 전역 매개 변수에 사용됩니다. |


**DeviceState** Azure Function의 경우 예제 사용자 지정 명령 이벤트는 다음과 같습니다. 이는 함수 앱에 대한 **입력** 으로 작동합니다.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>사용자 지정 명령 앱에 대한 Azure Function 출력

Azure Function의 출력이 사용자 지정 명령 앱에서 사용되는 경우 다음과 같은 형식으로 표시됩니다. 자세한 내용은 [웹 엔드포인트에서 명령 업데이트](./how-to-custom-commands-update-command-from-web-endpoint.md)를 참조하세요.

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>클라이언트 애플리케이션에 대한 Azure Function 출력

Azure Function의 출력을 클라이언트 애플리케이션에서 사용하는 경우 출력은 클라이언트 애플리케이션에 필요한 모든 형태를 사용할 수 있습니다.

**DeviceState** 엔드포인트의 경우 사용자 지정 명령 애플리케이션 대신 클라이언트 애플리케이션에서 Azure 함수의 출력을 사용합니다. Azure 함수의 예제 출력은 다음과 같습니다.
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

이 출력은 디바이스 상태를 유지 관리할 수 있도록 외부 스토리지에 작성해야 합니다. 외부 스토리지 상태는 아래의 [클라이언트 애플리케이션과 통합](#integrate-with-client-application) 섹션에서 사용됩니다.


### <a name="deploy-azure-function"></a>Azure 함수 배포

Azure Functions 앱으로 구성하고 배포할 수 있는 샘플을 제공합니다. 샘플에 대한 스토리지 계정을 만들려면 다음 단계를 따릅니다.
 
1. 디바이스 상태를 저장할 테이블 스토리지를 만듭니다. Azure Portal에서 **devicestate** 이름으로 **스토리지 계정** 유형의 새 리소스를 만듭니다.
1. **devicestate -> 액세스 키** 에서 **연결 문자열** 값을 복사합니다. 다운로드한 샘플 함수 앱 코드에 이 문자열 비밀을 추가해야 합니다.
1. 샘플 [함수 앱 코드](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start)를 다운로드합니다.
1. Visual Studio 2019에서 다운로드한 솔루션을 엽니다. **Connections.json** 에서 **STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** 을 2단계의 비밀로 바꿉니다.
1.  **DeviceStateAzureFunction** 코드를 다운로드합니다.

Azure Functions에 샘플 앱을 배포하려면 다음 단계를 수행합니다.

1. Azure Functions 앱을 [배포](../../azure-functions/index.yml)합니다.
1. 배포가 성공할 때까지 기다리고 Azure Portal의 배포된 리소스로 이동합니다. 
1. 왼쪽 창에서 **Functions** 를 선택한 다음, **DeviceState** 를 선택합니다.
1.  새 창에서 **코드 + 테스트** 를 선택한 다음, **함수 URL 가져오기** 를 선택합니다.
 
## <a name="setup-web-endpoints-in-custom-commands"></a>사용자 지정 명령에서 웹 엔드포인트 설정

Azure 함수를 기존 사용자 지정 명령 애플리케이션에 연결해 보겠습니다.
이 섹션에서는 기존 기본 **DeviceState** 엔드포인트를 사용합니다. Azure Function을 사용하여 고유한 웹 엔드포인트를 만들었거나 그렇지 않은 경우 기본값 `https://webendpointexample.azurewebsites.net/api/DeviceState` 대신 해당 값을 사용합니다.

1. 이전에 만든 사용자 지정 명령 애플리케이션을 엽니다.
1. **웹 엔드포인트** 로 이동하여 **새 웹 엔드포인트** 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![새 웹 엔드포인트](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 설정 | 제안 값 | Description |
   | ------- | --------------- | ----------- |
   | 이름 | UpdateDeviceState | 웹 엔드포인트의 이름입니다. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | 사용자 지정 명령 앱에서 통신할 엔드포인트의 URL입니다. |
   | 메서드 | POST | 엔드포인트에 허용되는 상호 작용(예: GET, POST)입니다.|
   | 헤더 | 키: 앱, 값: applicationId의 처음 8자리를 사용합니다. | 요청 헤더에 포함할 헤더 매개 변수입니다.|

    > [!NOTE]
    > - [Azure Functions](../../azure-functions/index.yml)를 사용하여 만든 예제 웹 엔드포인트는 TV 및 팬의 디바이스 상태를 저장하는 데이터베이스와 연결됩니다.
    > - 제안된 헤더는 예제 엔드포인트에만 필요합니다.
    > - 헤더 값이 예제 엔드포인트에서 고유한지 확인하려면 **applicationId** 의 처음 8자리를 사용합니다.
    > - 실제 환경에서 웹 엔드포인트는 디바이스를 관리하는 [IOT 허브](../../iot-hub/about-iot-hub.md)에 대한 엔드포인트일 수 있습니다.

1. **저장** 을 클릭합니다.

## <a name="call-web-endpoints"></a>웹 엔드포인트 호출

1. **TurnOnOff** 명령으로 이동하여 완료 규칙 아래에서 **ConfirmationResponse** 를 선택한 다음, **작업 추가** 를 선택합니다.
1. **새 작업 - 유형** 아래에서 **웹 엔드포인트 호출** 을 선택합니다.
1. **작업 편집 - 엔드포인트** 에서 만든 웹 엔드포인트인 **UpdateDeviceState** 를 선택합니다.  
1. **구성** 에서 다음 값을 입력합니다.
   > [!div class="mx-imgBorder"]
   > ![웹 엔드포인트 호출 작업 매개 변수](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 엔드포인트 | UpdateDeviceState | 이 작업에서 호출하려는 웹 엔드포인트입니다. |
   | 쿼리 매개 변수 | item={SubjectDevice}&&value={OnOff} | 웹 엔드포인트 URL에 추가할 쿼리 매개 변수입니다.  |
   | 본문 콘텐츠 | N/A | 요청의 본문 콘텐츠입니다. |

    > [!NOTE]
    > - 제안된 쿼리 매개 변수는 예제 엔드포인트에만 필요합니다.

1. **성공 시 - 실행할 작업** 에서 **음성 응답 보내기** 를 선택합니다.

    **간단한 편집기** 에서 `{SubjectDevice} is {OnOff}`를 입력합니다.

   > [!div class="mx-imgBorder"]
   > ![성공 시 - 실행할 작업 화면을 보여 주는 스크린샷](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 실행할 작업 | 음성 응답 보내기 | 웹 엔드포인트에 대한 요청이 성공하면 실행할 작업입니다. |

   > [!NOTE]
   > - `{YourWebEndpointName.FieldName}`을 사용하여 http 응답의 필드에 직접 액세스할 수도 있습니다. `{UpdateDeviceState.TV}`

1. **실패 시 - 실행할 작업** 에서 **음성 응답 보내기** 를 선택합니다.

    **간단한 편집기** 에서 `Sorry, {WebEndpointErrorMessage}`를 입력합니다.

   > [!div class="mx-imgBorder"]
   > ![실패 시 웹 엔드포인트 호출 작업](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 설정 | 제안 값 | 설명 |
   | ------- | --------------- | ----------- |
   | 실행할 작업 | 음성 응답 보내기 | 웹 엔드포인트에 대한 요청이 실패하면 실행할 작업입니다. |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}`는 선택 사항입니다. 오류 메시지를 공개하지 않으려면 이를 자유롭게 제거할 수 있습니다.
   > - 예제 엔드포인트 내에서 헤더 매개 변수 누락과 같은 일반적인 오류에 대한 자세한 오류 메시지와 함께 http 응답을 다시 보냅니다.

### <a name="try-it-out-in-test-portal"></a>테스트 포털에서 사용해 보기
- 성공 응답에서 저장, 학습 및 테스트합니다.
   > [!div class="mx-imgBorder"]
   > ![성공 시 응답을 보여 주는 스크린샷](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 실패 응답에서 쿼리 매개 변수 중 하나를 제거, 저장, 학습 및 테스트합니다.
   > [!div class="mx-imgBorder"]
   > ![성공 시 웹 엔드포인트 호출 작업](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>클라이언트 애플리케이션과 통합

[클라이언트 애플리케이션에 사용자 지정 명령 작업 보내기](./how-to-custom-commands-send-activity-to-client.md)에서 **클라이언트에 작업 보내기** 작업을 추가했습니다. 작업은 **웹 엔드포인트 호출** 작업의 성공 여부에 관계없이 클라이언트 애플리케이션에 보내집니다.
그러나 일반적으로 웹 엔드포인트 호출이 성공하는 경우에만 작업을 클라이언트 애플리케이션에 보내려고 합니다. 다음 예제에서 이 작업은 디바이스 상태가 성공적으로 업데이트되는 경우입니다.

1. 이전에 추가한 **클라이언트에 작업 보내기** 작업을 삭제합니다.
1. 웹 엔드포인트 호출을 편집합니다.
    1. **구성** 에서 **쿼리 매개 변수** 가 `item={SubjectDevice}&&value={OnOff}`인지 확인합니다.
    1. **성공 시** 에서 **실행할 작업** 을 **클라이언트에 작업 보내기** 로 변경합니다.
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
이제 웹 엔드포인트에 대한 요청이 성공하는 경우에만 작업을 클라이언트에 보냅니다.

### <a name="create-visuals-for-syncing-device-state"></a>디바이스 상태 동기화를 위한 시각적 개체 만들기

다음 XML을 **EnableMicrophoneButton** 블록 위의 `MainPage.xaml`에 추가합니다.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>디바이스 상태 동기화

`MainPage.xaml.cs`에서 `using Windows.Web.Http;`라는 참조를 추가합니다. `MainPage` 클래스에 다음 코드를 추가합니다. 이 메서드는 GET 요청을 예제 엔드포인트에 보내고 앱의 현재 디바이스 상태를 추출합니다. `<your_app_name>`을 사용자 지정 명령 웹 엔드포인트의 **헤더** 에서 사용한 것으로 변경해야 합니다.

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

1. 애플리케이션을 시작합니다.
1. [디바이스 상태 동기화]를 클릭합니다.
이전 섹션에서 `turn on tv`를 사용하여 앱을 테스트한 경우 TV 쇼가 **켜기** 로 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![디바이스 상태 동기화](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. **마이크 사용** 을 선택합니다.
1. **대화** 단추를 선택합니다.
1. `turn on the fan`이라고 말합니다. 팬의 시각적 상태가 **켜기** 로 변경됩니다.
    > [!div class="mx-imgBorder"]
    > ![팬 켜기](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 애플리케이션을 원격 기술로 내보내기](./how-to-custom-commands-integrate-remote-skills.md)
