## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

IoT 허브를 만들었으므로 IoT 허브에 장치 및 응용 프로그램을 연결하는 데 사용하는 중요한 정보를 찾습니다. 

1. IoT Hub를 만든 후 대시보드에서 클릭합니다. **호스트 이름**을 기록해둔 다음 **공유 액세스 정책**을 클릭합니다.

   ![IoT Hub의 호스트 이름 가져오기](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. **공유 액세스 정책** 창에서 **iothubowner** 정책을 클릭한 다음 IoT Hub의 **연결 문자열**을 복사하여 기록해 둡니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](../articles/iot-hub/iot-hub-devguide-security.md)를 참조하세요.

> [!NOTE] 
이 설치 자습서에는 이 iothubowner 연결 문자열이 필요 없습니다. 그러나 이 설치를 완료한 후 다른 IoT 시나리오의 일부 자습서에는 필요할 수 있습니다.

   ![IoT Hub 연결 문자열 가져오기](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>장치의 IoT Hub에서 장치 등록

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub를 엽니다.

2. **IoT 장치**를 클릭합니다.
3. IoT 장치 창에서 **추가**를 클릭하여 IoT Hub에 장치를 추가합니다. 그런 다음 아래 작업을 수행합니다.

   **장치 ID**: 새 장치의 ID를 입력합니다. 장치 ID는 대/소문자를 구분합니다.

   **인증 형식**: **대칭 키**를 선택합니다.

   **자동 생성 키**:이 확인란을 선택합니다.

   **IoT Hub에 장치 연결**: **사용**을 클릭합니다.

   ![IoT 허브의 IoT 장치에 장치 추가](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-iot-devices-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. **저장**을 클릭합니다.
5. 장치가 만들어진 후에 **IoT 장치** 창에서 장치를 엽니다.

   ![IoT Hub의 IoT 장치 목록](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_device-list-in-iot-devices-portal.png)

6. 연결 문자열의 기본 키를 기록해 둡니다.

   ![장치 연결 문자열 가져오기](../articles/iot-hub/media/iot-hub-create-hub-and-device/8_get-device-connection-string-in-iot-devices-portal.png)
