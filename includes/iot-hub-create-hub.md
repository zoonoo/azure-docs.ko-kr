1. [Azure Portal][lnk-portal]에 로그인합니다.
1. **리소스 만들기** > **사물 인터넷** > **IoT Hub**를 선택합니다.
   
    ![IoT Hub를 탐색하는 Azure Portal 스크린 샷][1]

1. **IoT Hub** 창에서 IoT Hub에 다음 정보를 입력합니다.

   * **이름**: IoT Hub에 대한 이름을 만듭니다. 입력한 이름이 올바른 경우 녹색 확인 표시가 나타납니다.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **가격 및 크기 계층**: 이 자습서에서는 **F1 - 체험** 계층을 선택합니다. 자세한 내용은 [가격 및 크기 계층][lnk-pricing]을 참조하세요.

   * **리소스 그룹**: IoT Hub를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][lnk-resource-groups]를 참조하세요.

   * **위치**: 가장 가까운 위치를 선택합니다.

   * **대시보드에 고정**: 대시보드에서 IoT Hub에 쉽게 액세스하려면 이 옵션을 선택합니다.

    ![IoT Hub 창][2]

1. **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행 상황을 모니터링할 수 있습니다.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md