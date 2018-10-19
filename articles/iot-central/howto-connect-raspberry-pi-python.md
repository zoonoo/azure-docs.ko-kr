---
title: Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결(Python) | Microsoft Docs
description: 장치 개발자로서 Python을 사용하여 Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: b5632db57e902eef76860f85de6e76f85861090a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728966"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Azure IoT Central 응용 프로그램에 Raspberry Pi 연결(Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

이 문서에서는 장치 개발자로서 Python 프로그래밍 언어를 사용하여 Raspberry Pi를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **샘플 Devkits** 응용 프로그램 템플릿으로 만든 Azure IoT Central 응용 프로그램. 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조하세요.
* Raspbian 운영 체제를 실행하는 Raspberry Pi 장치. GUI 환경에 액세스하려면 Raspberry Pi에 연결된 모니터, 키보드 및 마우스가 필요합니다. Raspberry Pi가 [인터넷에 연결](https://www.raspberrypi.org/learning/software-guide/wifi/)할 수 있어야 합니다.
* 필요에 따라 Raspberry Pi용 [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) 추가 항목 보드를 사용할 수도 있습니다. 이 보드는 다양한 센서에서 원격 분석 데이터를 수집하여 Azure IoT Central 응용 프로그램에 보냅니다. **Sense Hat** 보드가 없는 경우 Raspberry Pi 이미지의 일부분으로 제공되는 에뮬레이터를 대신 사용해도 됩니다.

## <a name="sample-devkits-application"></a>**샘플 Devkits** 응용 프로그램

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **Raspberry Pi** 장치가 포함됩니다. 

- 장치에 대한 측정값 **습도**, **온도**, **압력**, **Magnometer**(X, Y, Z 축을 따라 측정됨), **Accelorometer**(X, Y, Z 축을 따라 측정됨) 및 **Gyroscope**(X, Y, Z 축을 따라 측정됨)를 포함하는 원격 분석입니다.
- **전압**, **현재**, **팬 속도** 및 **IR** 토글을 보여주는 설정입니다.
- 장치 속성 **다이 번호** 및 **위치** 클라우드 속성을 포함하는 속성입니다.


장치 템플릿 구성에 대한 자세한 내용은 [Raspberry PI 장치 템플릿 세부 정보](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details) 참조
    

## <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 응용 프로그램에서 **Raspberry Pi** 장치 템플릿으로 실제 장치를 추가하고 장치 연결 정보(**범위 ID, 장치 ID, 기본 키**)를 기록해 둡니다. 자세한 내용은 [Azure IoT Central 응용 프로그램에 실제 장치 추가](tutorial-add-device.md)를 참조하세요.


### <a name="configure-the-raspberry-pi"></a>Raspberry Pi 구성

다음 단계에서는 GitHub에서 샘플 Python 응용 프로그램을 다운로드하고 구성하는 방법을 설명합니다. 이 응용 프로그램 예제는 다음과 같은 일을 합니다.

* Azure IoT Central에 원격 분석 데이터 및 속성 값을 보냅니다.
* Azure IoT Central에서 수행된 설정 변경에 응답합니다.

장치를 구성하려면 [GitHub의 단계별 지침을 따르세요.](http://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> Raspberry Pi Python 샘플에 대한 자세한 내용은 GitHub의 [Readme](http://aka.ms/iotcentral-docs-Raspi-releases) 파일을 참조하세요.


1. 구성된 장치는 잠시 후부터 Azure IoT Central에 데이터 전송을 시작합니다.
1. Azure IoT Central 응용 프로그램에서, Raspberry Pi에서 실행되는 코드가 응용 프로그램과 상호 작용하는 방식을 살펴봅니다.

    * 실제 장치의 **측정값** 페이지에서, Raspberry Pi가 보낸 원격 분석 데이터를 볼 수 있습니다. **Sense HAT 에뮬레이터**를 사용하는 경우 Raspberry Pi의 GUI에서 원격 분석 값을 수정할 수 있습니다.
    * **속성** 페이지에서, 보고된 **다이 번호** 속성 값을 볼 수 있습니다.
    * **설정** 페이지에서, 전압, 팬 속도 등 Raspberry Pi의 다양한 설정을 변경할 수 있습니다. Raspberry Pi가 변경 내용을 확인하면 Azure IoT Central에서 설정이 **동기화됨**으로 표시됩니다.


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI 장치 템플릿 세부 정보

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **Raspberry Pi** 장치가 포함됩니다.

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

| type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 장치 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | location   | 해당 없음       |

## <a name="next-steps"></a>다음 단계

Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

* [Azure IoT Central에 일반 Node.js 클라이언트 응용 프로그램 연결](howto-connect-nodejs.md)
