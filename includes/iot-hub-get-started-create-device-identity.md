## <a name="create-a-device-identity"></a>장치 ID 만들기

이 섹션에서는 [iothub-explorer][iot-hub-explorer]라는 Node.js 도구를 사용하여 이 자습서에서 사용할 장치 ID를 만듭니다. 장치 ID는 대/소문자를 구분합니다.

1. 명령줄 환경에서 다음을 실행 합니다.

    `npm install -g iothub-explorer@latest`

1. 그런 다음, 다음 명령을 실행하여 허브에 로그인합니다. `{iot hub connection string}`을 이전에 복사한 IoT Hub 연결 문자열로 대체합니다.

    `iothub-explorer login "{iot hub connection string}"`

1. 마지막으로 다음 명령으로 `myDeviceId`라는 새 장치 ID를 만듭니다.

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

결과에서 장치 연결 문자열을 기록해 둡니다. 이 장치 연결 문자열은 장치 앱이 장치로 IoT Hub에 연결하는 데 사용됩니다.

![][img-identity]

[IoT Hub 시작][lnk-getstarted]을 참조하여 장치 ID를 프로그래밍 방식으로 만듭니다.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/quickstart-send-telemetry-dotnet.md
