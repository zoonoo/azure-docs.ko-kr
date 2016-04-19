## IoT Hub 만들기

시뮬레이션된 장치를 연결하려면 IoT Hub를 만들어야 합니다. 다음 단계는 Azure 포털을 사용하여 이 작업을 완료하는 방법을 보여줍니다.

1. [Azure 포털][lnk-portal]에 로그인합니다.

2. 점프 모음에서 **새로 만들기**를 클릭하고 **사물 인터넷**을 클릭한 다음 **Azure IoT Hub**를 클릭합니다.

    ![][1]

3. **IoT Hub** 블레이드에서 IoT Hub의 구성을 선택합니다.

    ![][2]

    * **이름** 상자에 IoT Hub의 이름을 입력합니다. **이름**의 유효하고 사용 가능하면 **이름** 상자에 녹색 확인 표시가 나타납니다.
    * [가격 및 크기 조정 계층][lnk-pricing]을 선택합니다. 이 자습서에는 특정 계층이 필요하지 않습니다. 이 자습서에서는 무료 F1 계층을 사용하는 것이 좋습니다.
    * **리소스 그룹**에서 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][lnk-resource-groups]를 참조하세요.
    * **위치**에서 IoT hub를 호스트하는 위치를 선택합니다. 이 자습서에서는 가장 가까운 위치를 선택하는 것이 좋습니다.

4. IoT hub 구성 옵션을 선택한 경우 **만들기**를 클릭합니다. Azure가 IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 시작 보드 또는 알림 패널에서 진행률을 모니터링할 수 있습니다.

    ![][3]

5. IoT Hub가 성공적으로 만들어지면 포털에서 IoT Hub에 대한 새 타일을 클릭하여 새 IoT Hub에 대한 블레이드를 엽니다. **호스트 이름**을 기록해둔 다음 **키** 아이콘을 클릭합니다.

    ![][4]

6. **공유 액세스 정책** 블레이드에서 **iothubowner** 정책을 클릭한 다음 **iothubowner** 블레이드에 있는 연결 문자열을 복사하여 기록해둡니다. 자세한 내용은 "Azure IoT Hub 개발자 가이드"에 있는 [액세스 제어][lnk-access-control]를 참조하세요.

    ![][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol

<!---HONumber=AcomDC_0413_2016-->