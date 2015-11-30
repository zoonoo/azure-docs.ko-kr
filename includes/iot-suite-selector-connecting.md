> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## 시나리오 개요

이 시나리오에서는 원격 모니터링 [미리 구성된 솔루션][lnk-what-are-preconfig-solutions]에 다음과 같은 원격 분석 데이터를 보낼 장치를 만듭니다.

- 외부 온도
- 내부 온도
- 습도

간소함을 위하여 장치의 코드가 샘플 값을 생성하지만, 사용자는 자신의 장치에 실제 센서를 연결하고 실제 원격 분석 데이터를 보내어 샘플을 확장할 것을 권장합니다.

## 시작하기 전에

장치에 대한 코드를 작성하기 전에, 미리 구성된 원격 모니터링 솔루션을 프로비전하고 이 솔루션에 장치를 프로비전해야 합니다.

### 미리 구성된 사용자의 원격 모니터링 솔루션 프로비전

여기에서 만드는 장치는 미리 구성된 [원격 모니터링][lnk-remote-monitoring] 솔루션의 인스턴스에 데이터를 전송하게 됩니다. Azure 계정을 만들고 IoT Suite에 프로비전하려면 [Azure IoT Suite 시작][lnk-getstarted]을 방문하세요. 새 솔루션을 만드는 경우 **원격 모니터링**을 선택합니다.

원격 모니터링 솔루션이 프로비전되면 **시작**을 클릭하여 솔루션 대시보드를 엽니다.

![][img-dashboard]

### 원격 모니터링 솔루션에서 장치 프로비전

> [AZURE.NOTE]솔루션에 장치가 이미 프로비전되어 있으면 이 단계를 건너뜁니다. 클라이언트 응용 프로그램을 만들 때 장치 자격 증명을 알아야 합니다.

미리 구성된 솔루션에 연결하는 장치는 유효한 자격 증명을 사용하여 자신을 식별할 수 있어야 합니다. 장치 자격 증명을 솔루션 대시보드에서 가져와서 클라이언트 응용 프로그램에 포함시킬 수 있습니다.

원격 모니터링 솔루션에 새 장치를 추가하려면 솔루션 대시보드에서 다음 단계를 완료합니다.

1.  대시보드의 왼쪽 아래 모서리에서 **장치 추가**를 클릭합니다.

    ![][1]

2.  **사용자 지정 장치** 패널에서 **새로 추가**를 클릭합니다.

    ![][2]

3.  **나만의 장치 ID 정의**를 선택하고 장치 ID(예: **mydevice**)를 입력하고 **ID 확인**을 클릭하여 해당 이름이 이미 사용되고 있는지 확인한 후 **만들기**를 클릭하여 장치를 프로비전합니다.

    ![][3]

5. 장치 자격 증명(장치 ID, IoT Hub 호스트 이름, 장치 키)을 기록해 둡니다. 이 정보는 장치를 원격 모니터링 솔루션에 연결할 때 클라이언트 응용 프로그램에 필요합니다. **완료**를 클릭합니다.

    ![][4]

6. 장치가 장치 섹션에 제대로 표시되는지 확인합니다. 장치가 원격 모니터링 솔루션에 연결될 때까지 상태는 **보류 중**으로 표시됩니다.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: http://www.microsoft.com/server-cloud/internet-of-things/getting-started.aspx
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md

<!---HONumber=Nov15_HO4-->