## <a name="create-an-iot-hub"></a>IoT Hub 만들기
시뮬레이션된 장치 앱을 연결할 IoT Hub를 만듭니다. 다음 단계는 Azure 포털을 사용하여 이 작업을 완료하는 방법을 보여줍니다.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

IoT 허브를 만들었으므로 IoT 허브에 장치 및 응용 프로그램을 연결하는 데 사용하는 중요한 정보를 찾습니다. 

1. IoT Hub가 성공적으로 만들어지면 Azure Portal에서 IoT Hub에 대한 새 타일을 클릭하여 새 IoT Hub에 대한 속성 창을 엽니다. **호스트 이름**을 기록해둔 다음 **공유 액세스 정책**을 클릭합니다.
   
    ![새 IoT Hub 창][4]
1. **공유 액세스 정책**에서 **iothubowner** 정책을 클릭한 다음 **iothubowner** 창에 있는 IoT Hub 연결 문자열을 복사하여 기록해둡니다. 자세한 내용은 "IoT Hub 개발자 가이드"에 있는 [액세스 제어][lnk-access-control]를 참조하세요.
   
    ![공유 액세스 정책][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
