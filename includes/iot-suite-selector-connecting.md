> [!div class="op_single_selector"]
> * [Windows에서 C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux에서 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js(일반)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Raspberry Pi의 Node.js](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [Raspberry Pi의 C](../articles/iot-suite/iot-suite-connecting-pi-c.md)

이 자습서에서는 원격 모니터링 [미리 구성된 솔루션](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md)에 다음과 같은 원격 분석을 보내는 **냉각기** 장치를 구현합니다.

* 온도
* 압력
* 습도

간단히 하기 위해 코드는 **냉각기**에 대한 샘플 원격 분석 값을 생성합니다. 실제 센서를 장치에 연결하고 실제 원격 분석을 보내 샘플을 확장할 수 있습니다.

샘플 장치는 또한:

* 솔루션에 메타데이터를 보내 해당 기능을 설명합니다.
* 솔루션의 **장치** 페이지에서 트리거된 작업에 응답합니다.
* 솔루션의 **장치** 페이지에서 보내는 구성 변경 내용에 응답합니다.

이 자습서를 완료하려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

장치에 대한 코드를 작성하기 전에, 미리 구성된 원격 모니터링 솔루션을 프로비전하고 이 솔루션에 새로운 사용자 지정 장치를 프로비전해야 합니다.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>미리 구성된 사용자의 원격 모니터링 솔루션 프로비전

이 자습서에서 만드는 **냉각기** 장치는 미리 구성된 [원격 모니터링](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) 솔루션의 인스턴스에 데이터를 전송합니다. Azure 계정에서 미리 구성된 원격 모니터링 솔루션을 미리 프로비전하지 않은 경우 [미리 구성된 원격 모니터링 솔루션 배포](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)를 참조하세요.

원격 모니터링 솔루션의 프로비전 프로세스가 완료되면 **시작** 을 클릭하여 브라우저에서 솔루션 대시보드를 엽니다.

![솔루션 대시보드](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>원격 모니터링 솔루션에서 장치 프로비전

> [!NOTE]
> 솔루션에 장치가 이미 프로비전되어 있으면 이 단계를 건너뜁니다. 클라이언트 응용 프로그램을 만들 때 장치 자격 증명이 필요합니다.

미리 구성된 솔루션에 연결하는 장치는 유효한 자격 증명을 사용하여 IoT Hub에 자신을 식별할 수 있어야 합니다. 솔루션 **장치** 페이지에서 장치 자격 증명을 검색할 수 있습니다. 이 자습서의 뒷부분에서는 클라이언트 응용 프로그램에 있는 장치 자격 증명을 포함합니다.

원격 모니터링 솔루션에 장치를 추가하려면 솔루션의 **장치** 페이지에서 다음 단계를 완료합니다.

1. **프로비전**을 선택한 다음 **장치 유형**으로 **물리적**을 선택합니다.

    ![실제 장치 프로비전](media/iot-suite-selector-connecting/devicesprovision.png)

1. 장치 ID로 **물리적 냉각기**를 입력합니다. **대칭 키** 및 **자동 생성 키** 옵션을 선택합니다.

    ![장치 옵션 선택](media/iot-suite-selector-connecting/devicesoptions.png)

미리 구성된 솔루션에 연결하는 데 사용해야 하는 자격 증명을 찾으려면 브라우저에서 Azure Portal로 이동합니다. 구독에 로그인합니다.

1. 원격 모니터링 솔루션에서 사용하는 Azure 서비스를 포함하는 리소스 그룹을 찾습니다. 리소스 그룹은 프로비전한 원격 모니터링 솔루션과 동일한 이름을 갖습니다.

1. 이 리소스 그룹에서 IoT 허브로 이동합니다. 그런 다음 **장치 탐색기**를 선택합니다.

    ![장치 탐색기](media/iot-suite-selector-connecting/deviceexplorer.png)

1. 원격 모니터링 솔루션의 **장치** 페이지에서 만든 **장치 ID**를 선택합니다.

1. **장치 ID**와 **기본 키** 값을 기록해 둡니다. 장치를 솔루션에 연결하는 코드를 추가할 때 이러한 값을 사용합니다.

이제 미리 구성된 원격 모니터링 솔루션에서 물리적 장치를 프로비전했습니다. 다음 섹션에서는 장치 자격 증명을 사용하여 솔루션에 연결하는 클라이언트 응용 프로그램을 구현합니다.

클라이언트 응용 프로그램은 기본 제공 **냉각기** 장치 모델을 구현합니다. 미리 구성된 솔루션 장치 모델은 장치에 대해 다음을 지정합니다.

* 장치가 솔루션에 보고하는 속성 예를 들어, **냉각기** 장치는 해당 펌웨어 및 위치에 대한 정보를 보고합니다.
* 장치가 솔루션에 보내는 원격 분석의 유형 예를 들어, **냉각기** 장치는 온도, 습도 및 압력 값을 보냅니다.
* 장치에서 실행되도록 솔루션에서 예약할 수 있는 메서드 예를 들어, **냉각기** 장치는 **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease** 및 **IncreasePressuree** 메서드를 구현해야 합니다.