---
title: Raspberry Pi를 Azure IoT Central 애플리케이션에 연결(Python) | Microsoft Docs
description: 장치 개발자가 Python을 사용 하 여 Azure IoT Central 응용 프로그램을 Raspberry Pi를 연결 하는 방법입니다.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eccc4100c89c971e264b9b915cd17b9f5ce4477b
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617449"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Azure IoT Central 애플리케이션에 Raspberry Pi 연결(Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

이 문서에서는 장치 개발자로서 Python 프로그래밍 언어를 사용하여 Raspberry Pi를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음 구성 요소가 필요합니다.

* **샘플 Devkits** 애플리케이션 템플릿으로 만든 Azure IoT Central 애플리케이션. 자세한 내용은 참조는 [애플리케이션 빠른 시작 만들기](quick-deploy-iot-central.md)를 참조하세요.
* Raspbian 운영 체제를 실행하는 Raspberry Pi 디바이스. Raspberry Pi를 인터넷에 연결할 수 있어야 합니다. 자세한 내용은 [Raspberry Pi 설정](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)합니다.

## <a name="sample-devkits-application"></a>**샘플 Devkits** 애플리케이션

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Raspberry Pi** 장치가 포함됩니다.

- 디바이스가 수집하는 다음 측정값을 포함하는 원격 분석입니다.
  - 습도
  - 온도
  - 압력
  - 지자기 센터(X, Y, Z)
  - 가속도계(X, Y, Z)
  - 자이로스코프(X, Y, Z)
- 설정
  - 전압
  - Current
  - 팬 속도
  - IR 토글.
- properties
  - Die 번호 디바이스 속성
  - 위치 클라우드 속성

장치 템플릿 구성의 전체 세부 정보를 참조 하세요. 합니다 [Raspberry Pi 장치 템플릿 세부 정보](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)합니다.

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 응용 프로그램에서 실제 장치를 추가 합니다 **Raspberry Pi** 장치 템플릿. 기록해 장치 연결 세부 정보 (**범위 ID**를 **장치 ID**, 및 **기본 키**). 자세한 내용은 [Azure IoT Central 애플리케이션에 실제 장치 추가](tutorial-add-device.md)를 참조하세요.

### <a name="configure-the-raspberry-pi"></a>Raspberry Pi 구성

다음 단계에서는 GitHub에서 샘플 Python 애플리케이션을 다운로드하고 구성하는 방법을 설명합니다. 이 애플리케이션 예제는 다음과 같은 일을 합니다.

* Azure IoT Central에 원격 분석 데이터 및 속성 값을 보냅니다.
* Azure IoT Central에서 수행된 설정 변경에 응답합니다.

장치를 구성 하려면 [GitHub의 단계별 지침에 따라](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md)합니다.

1. 장치를 구성할 때 장치는 Azure IoT Central을 원격 분석 측정을 보내기 시작 합니다.
1. Azure IoT Central 애플리케이션에서, Raspberry Pi에서 실행되는 코드가 애플리케이션과 상호 작용하는 방식을 살펴볼 수 있습니다.

    * 실제 디바이스의 **측정값** 페이지에서, Raspberry Pi가 보낸 원격 분석 데이터를 볼 수 있습니다.
    * 에 **설정을** 페이지 전압 및 팬 속도 같은 Raspberry Pi에서 설정을 변경할 수 있습니다. Raspberry Pi의 변경으로 인해, 설정으로 표시 **동기화**합니다.

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi 장치 템플릿 세부 정보

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Raspberry Pi** 장치가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
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

| 표시 이름 | 필드 이름 | Units | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| 표시 이름 | 필드 이름 | 온 텍스트 | 오프 텍스트 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 켜기      | 끄기      | 꺼짐     |

### <a name="properties"></a>properties

| Type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | location   | N/A       |

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계에 알아보려면는 이제 Azure IoT Central 응용 프로그램을 Raspberry Pi를 연결 하는 방법을 배웠으므로 하는 방법 [템플릿으로 사용자 지정 장치 설정](howto-set-up-template.md) 사용자 고유의 IoT 장치에 대 한 합니다.
