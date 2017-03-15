## <a name="create-a-device-identity"></a>장치 ID 만들기
이 섹션에서는 [IoT Hub Explorer][iot-hub-explorer]라는 Node.js 도구를 사용하여 이 자습서에 대한 장치 ID를 만듭니다.

1. 명령줄 환경에서 다음을 실행 합니다.
   
    ```
    npm install -g iothub-explorer@latest
    ```
2. 그런 후 다음 명령을 실행하여 허브에 로그인하고 `{iot hub connection string}`을 이전에 복사한 IoT Hub 연결 문자열로 대체합니다.

    ```
    iothub-explorer login "{iot hub connection string}"
    ```
3. 마지막으로 다음 명령으로 `myDeviceId`라는 새 장치 ID를 만듭니다.
   
    ```
    iothub-explorer create myDeviceId --connection-string
    ```

결과에서 장치 연결 문자열을 기록해 둡니다. 이 장치 연결 문자열은 장치 앱이 장치로 IoT Hub에 연결하는 데 사용됩니다.

![][img-identity]

장치 ID를 프로그래밍 방식으로 만드는 방법은 [IoT Hub 시작][lnk-getstarted]을 참조하세요.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
