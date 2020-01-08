---
title: Raspberry Pi를 Azure IoT Central 애플리케이션에 연결(Python) | Microsoft Docs
description: 장치 개발자는 Python을 사용 하 여 Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 설명 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3daa567a916bd0abeb407028c7d06bd1f2bd464b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454104"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Azure IoT Central 애플리케이션에 Raspberry Pi 연결(Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 디바이스 개발자로서 Python 프로그래밍 언어를 사용하여 Raspberry Pi를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음 구성 요소가 필요합니다.

* **레거시 응용** 프로그램 응용 프로그램 템플릿에서 만든 Azure IoT Central 응용 프로그램입니다. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
* Raspbian 운영 체제를 실행하는 Raspberry Pi 디바이스. Raspberry Pi는 인터넷에 연결할 수 있어야 합니다. 자세한 내용은 [Raspberry Pi 설정](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)을 참조 하세요.

> [!TIP]
> Raspberry Pi 장치를 설정 하 고 연결 하는 방법에 대 한 자세한 내용은 [Raspberry pi 시작](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi) 을 참조 하세요.

## <a name="add-a-device-template"></a>디바이스 템플릿 추가

Azure IoT Central 응용 프로그램에서 다음과 같은 특징을 가진 새 **Raspberry Pi** 장치 템플릿을 추가 합니다.

- 디바이스가 수집하는 다음 측정값을 포함하는 원격 분석입니다.
  - 습도
  - 온도
  - 압력
  - 지자기 센터(X, Y, Z)
  - 가속도계(X, Y, Z)
  - 자이로스코프(X, Y, Z)
- 설정
  - 전압
  - 현재
  - 팬 속도
  - IR 토글.
- 속성
  - Die 번호 디바이스 속성
  - 위치 클라우드 속성

1. 장치 템플릿 ![장치 템플릿에서 **+ 새로 만들기** 를 선택](media/howto-connect-raspberry-pi-python/adddevicetemplate.png)
   

2. **Raspberry pi** 를 선택 하 고 Raspberry pi 장치 템플릿을 만들어 장치 템플릿 추가 ![](media/howto-connect-raspberry-pi-python/newdevicetemplate.png)

장치 템플릿 구성에 대 한 자세한 내용은 [Raspberry Pi 장치 템플릿 정보](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)를 참조 하세요.

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 응용 프로그램에서 **Raspberry Pi** 장치 템플릿의 실제 장치를 추가 합니다. 장치 연결 정보 (**범위 ID**, **장치 ID**및 **기본 키**)를 기록해 둡니다. 자세한 내용은 [Azure IoT Central 애플리케이션에 실제 디바이스 추가](tutorial-add-device.md)를 참조하세요.

### <a name="configure-the-raspberry-pi"></a>Raspberry Pi 구성

다음 단계에서는 GitHub에서 샘플 Python 애플리케이션을 다운로드하고 구성하는 방법을 설명합니다. 이 애플리케이션 예제는 다음과 같은 일을 합니다.

* Azure IoT Central에 원격 분석 데이터 및 속성 값을 보냅니다.
* Azure IoT Central에서 수행된 설정 변경에 응답합니다.

1. Raspberry Pi 데스크톱에서 또는 SSH를 사용 하 여 원격으로 Raspberry Pi의 셸 환경에 연결 합니다.

1. 다음 명령을 실행 하 여 Python 클라이언트 IoT Central를 설치 합니다.

    ```bash
    pip install iotc
    ```

1. 샘플 Python 코드를 다운로드 합니다.

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. 다운로드 한 `app.py` 파일을 편집 하 고 `DEVICE_ID`, `SCOPE_ID`및 `PRIMARY/SECONDARY device KEY` 자리 표시자를 이전에 적어 둔 연결 값으로 바꿉니다. 변경 내용을 저장합니다.

    > [!TIP]
    > Raspberry Pi의 셸에서 **nano** 또는 **vi** 텍스트 편집기 중 하나를 사용할 수 있습니다.

1. 다음 명령을 사용하여 샘플을 실행합니다.

    ```bash
    python app.py
    ```

    Raspberry Pi는 원격 분석 측정을 Azure IoT Central로 보내기 시작 합니다.

1. Azure IoT Central 애플리케이션에서, Raspberry Pi에서 실행되는 코드가 애플리케이션과 상호 작용하는 방식을 살펴볼 수 있습니다.

    * 실제 디바이스의 **측정값** 페이지에서, Raspberry Pi가 보낸 원격 분석 데이터를 볼 수 있습니다.
    * **속성** 페이지에서 **주사위 번호** 장치 속성을 볼 수 있습니다.
    * **설정** 페이지에서 전압 및 팬 속도와 같은 Raspberry Pi에 대 한 설정을 변경할 수 있습니다. Raspberry Pi에서 변경을 승인 하면 설정이 **동기화**된 것으로 표시 됩니다.

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi 장치 템플릿 정보

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Raspberry Pi** 디바이스가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | 단위  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | 단위 | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| 현재      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| 표시 이름 | 필드 이름 | 온 텍스트 | 오프 텍스트 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 켜기      | OFF      | 해제     |

### <a name="properties"></a>속성

| 유형            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | 위치   | N/A       |

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에 Raspberry Pi를 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정](howto-set-up-template.md) 하는 방법을 배우는 것입니다.
