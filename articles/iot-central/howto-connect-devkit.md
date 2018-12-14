---
title: Azure IoT Central 응용 프로그램에 DevKit 디바이스 연결 | Microsoft Docs
description: 디바이스 개발자로서 Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스를 연결하는 방법을 알아봅니다.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1bfa52d07ddc4eaf86bce17c0ad7c63493a8b5fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255730"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 MXChip IoT DevKit 디바이스 연결

이 문서에서는 디바이스 개발자로서 MXChip IoT DevKit(DevKit) 디바이스를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

1. **샘플 Devkits** 응용 프로그램 템플릿으로 만든 Azure IoT Central 응용 프로그램. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
1. DevKit 장치. DevKit 디바이스를 구매하려면 [MXChip IoT DevKit](http://mxchip.com/az3166)를 방문하세요.


## <a name="sample-devkits-application"></a>**샘플 Devkits** 응용 프로그램

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **MXChip** 장치가 포함됩니다. 

- 디바이스에 대한 측정값 **습도**, **온도**, **압력**, **Magnometer**(X, Y, Z 축을 따라 측정됨), **Accelorometer**(X, Y, Z 축을 따라 측정됨) 및 **Gyroscope**(X, Y, Z 축을 따라 측정됨)를 포함하는 원격 분석입니다.
- **장치 상태**에 대한 예제 측정을 포함하는 상태입니다.
- **단추 B 누름** 이벤트를 사용하여 이벤트 측정을 제공합니다. 
- **전압**, **현재**, **팬 속도** 및 **IR** 토글을 보여주는 설정입니다.
- 위치 속성이자 **제조 일자** 클라우드 속성인 **다이 번호** 및 **디바이스 위치**라는 디바이스 속성을 포함하는 속성입니다. 


구성에 대한 자세한 내용은 [MXChip 디바이스 템플릿 세부 정보](howto-connect-devkit.md#mxchip-device-template-details)를 참조하세요.


## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 애플리케이션에서 **MXChip** 디바이스 템플릿으로 실제 디바이스를 추가하고 디바이스 연결 정보(**범위 ID, 디바이스 ID 및 기본 키**)를 기록해 둡니다.

1. Device Explorer에서 **실제 장치**를 추가하고 **+새로 만들기 > 실제 장치**를 클릭하여 실제 장치를 추가합니다.
    * 디바이스 ID**<span style="color:Red">(소문자여야 함)</span>** 를 입력하거나 제안된 디바이스 ID를 사용합니다.
    * 디바이스 이름을 입력하거나 제안된 이름을 사용합니다.
    
    ![디바이스 추가](media\concepts-connectivity\add-device.png)


1. 디바이스 페이지에서 **연결**을 클릭하여 추가 디바이스의 **범위 ID, 디바이스 ID 및 기본 키** 같은 연결 정보를 가져옵니다.
 
    ![연결 정보](media\concepts-connectivity\device-connect.PNG)

3. DevKit 디바이스를 준비할 때 일시적으로 인터넷 연결이 끊어지므로 이러한 세부 정보를 저장해야 합니다. 


### <a name="prepare-the-devkit-device"></a>DevKit 장치 준비

> [!NOTE]
> 이전에 디바이스를 사용했고 WiFi 자격 증명을 저장했으며 다른 WiFi 네트워크, 연결 문자열 또는 원격 분석 측정값을 사용하도록 디바이스를 다시 구성하려면 보드의 **A** 및 **B** 단추를 동시에 누릅니다. 이 방법이 작동하지 않으면 **재설정** 단추를 눌러 다시 시도합니다.



#### <a name="to-prepare-the-devkit-device"></a>DevKit 디바이스를 준비하려면:


1. GitHub의 [릴리스](https://aka.ms/iotcentral-docs-MXChip-releases) 페이지에서 MXChip의 최신 사전 빌드 Azure IoT Central 펌웨어를 다운로드합니다.
1. USB 케이블을 사용하여 DevKit 장치를 개발 컴퓨터에 연결합니다. Windows에서, DevKit 장치의 저장소에 매핑된 드라이브에서 파일 탐색기 창이 열립니다. 예를 들어 드라이브 이름이 **AZ3166 (D:)** 일 수 있습니다.
1. **iotCentral.bin** 파일을 드라이브 창으로 끌어다 놓습니다. 복사가 완료되면 새 펌웨어를 사용하여 디바이스가 다시 부팅됩니다.

1. DevKit 장치가 다시 시작되면 다음 화면이 표시됩니다.

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 화면이 다른 요소를 표시하는 경우 디바이스를 다시 설정하고 디바이스에서 **A** 및 **B** 단추를 동시에 눌러 디바이스를 다시 부팅합니다. 

1. 이제 장치가 AP(액세스 지점) 모드에 있습니다. 컴퓨터 또는 모바일 디바이스에서 이 WiFi 액세스 지점에 연결할 수 있습니다.

1. 컴퓨터, 휴대폰 또는 태블릿에서 장치 화면에 표시된 WiFi 네트워크 이름에 연결합니다. 이 네트워크에 연결하면 인터넷에 액세스할 수 없습니다. 이 상태는 예상된 동작이며, 장치를 구성할 때까지 잠깐 동안 이 네트워크에만 연결됩니다.

1. 웹 브라우저를 열고 [http://192.168.0.1/start](http://192.168.0.1/start)로 이동합니다. 다음 웹 페이지가 표시됩니다.

    ![디바이스 구성 페이지](media/howto-connect-devkit/configpage.png)

    웹 페이지에서 다음을 추가합니다. 
    - WiFi 네트워크 이름 
    - WiFi 네트워크 암호
    - LCD 디바이스에 표시된 PIN 코드 
    - 디바이스의 연결 정보 **범위 ID, 디바이스 ID 및 기본 키**(단계에 따라 이 정보를 이미 저장했어야 함)      
    - 사용 가능한 모든 원격 분석 측정값을 선택합니다. 

1. **장치 구성**을 선택하면 이 페이지가 표시됩니다.

    ![구성된 장치](media/howto-connect-devkit/deviceconfigured.png)

1. 장치의 **재설정** 단추를 누릅니다.


## <a name="view-the-telemetry"></a>원격 분석 보기

DevKit 디바이스가 다시 시작되면 디바이스에 다음과 같은 화면이 표시됩니다.

* 전송된 원격 분석 메시지 수.
* 실패 횟수.
* 받은 desired 속성 수 및 보낸 reported 속성 수.

> [!NOTE]
> 디바이스가 연결 중에 반복되는 것으로 나타나면 디바이스가 IoT Central에서 ‘차단’되어 있는지 확인하고 앱에 연결할 수 있도록 디바이스를 ‘차단 해제’합니다.

디바이스를 흔들면 보낸 reported 속성 수가 증가합니다. 장치에서 **다이 번호** 장치 속성으로 임의의 숫자를 보냅니다.

원격 분석 측정값 및 reported 속성 값을 확인하고, Azure IoT Central에서 설정을 구성할 수 있습니다.

1. **Device Explorer**를 사용하여 추가한 실제 MXChip 장치에 대한 **측정값** 페이지로 이동합니다.

    ![실제 장치로 이동](media/howto-connect-devkit/realdevicenew.png)

1. **측정값** 페이지에서 MXChip 장치에서 보낸 원격 분석 데이터를 볼 수 있습니다.

    ![실제 장치의 원격 분석 데이터 보기](media/howto-connect-devkit/devicetelemetrynew.png)

1. **속성** 페이지에서는 장치에서 보고된 마지막 다이 번호 및 장치 위치를 볼 수 있습니다.

    ![디바이스 속성 보기](media/howto-connect-devkit/devicepropertynew.png)

1. **설정** 페이지에서 MXChip 장치의 설정을 업데이트할 수 있습니다.

    ![장치 설정 보기](media/howto-connect-devkit/devicesettingsnew.png)

1. **대시보드** 페이지에서는 위치 지도를 볼 수 있습니다.

    ![디바이스 대시보드 보기](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>소스 코드 다운로드

장치 코드를 탐색하고 수정하려는 경우 GitHub에서 장치 코드를 다운로드하면 됩니다. 코드를 수정하려는 경우 다음 지침에 따라 사용 중인 데스크톱 운영 체제의 [개발 환경을 준비](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)해야 합니다.

소스 코드를 다운로드하려면 데스크톱 컴퓨터에서 다음 명령을 실행합니다.

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

이전 명령은 `iot-central-firmware` 폴더에 소스 코드를 다운로드합니다. 

> [!NOTE]
> 개발 환경에 **git**이 설치되지 않은 경우 [https://git-scm.com/download](https://git-scm.com/download)에서 다운로드할 수 있습니다.

## <a name="review-the-code"></a>코드 검토

Visual Studio Code를 사용하여(개발 환경을 준비할 때 설치했음) `iot-central-firmware` 폴더 안에 있는 `AZ3166` 폴더를 엽니다. 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Azure IoT Central 응용 프로그램으로 원격 분석 데이터가 전송되는 원리를 살펴보려면 원본 폴더의 **main_telemetry.cpp** 파일을 여세요.

`buildTelemetryPayload` 함수는 장치의 센서 데이터를 사용하여 JSON 원격 분석 페이로드를 만듭니다.

`sendTelemetryPayload` 함수는 **iotHubClient.cpp**에서 Azure IoT Central 응용 프로그램이 사용하는 IoT Hub에 JSON 페이로드를 보내는 `sendTelemetry`를 호출합니다.

Azure IoT Central 응용 프로그램으로 속성 값이 보고되는 원리를 살펴보려면 원본 폴더의 **main_telemetry.cpp** 파일을 여세요.

`telemetryLoop` 함수는 가속도계가 두 번 탭을 감지하면 **doubleTap** reported 속성을 보냅니다. **iotHubClient.cpp** 원본 파일의 `sendReportedProperty` 함수를 사용합니다.

**iotHubClient.cpp** 원본 파일의 코드는 [C용 Microsoft Azure IoT SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c)의 함수를 사용하여 IoT Hub와 상호 작용합니다.

샘플 코드를 수정하고, 빌드하고, 장치에 업로드하는 방법은 `AZ3166` 폴더에 있는 **readme.md** 파일을 참조하세요.

## <a name="mxchip-device-template-details"></a>MXChip 디바이스 템플릿 세부 정보 

샘플 Devkits 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 MXChip 디바이스가 포함됩니다.

### <a name="measurements"></a>측정값

#### <a name="telemetry"></a>원격 분석 

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


#### <a name="states"></a>상태 
| 이름          | 표시 이름   | 정상 | 주의 | 위험 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 디바이스 상태   | 녹색  | 주황색  | 빨강    | 

#### <a name="events"></a>이벤트 
| 이름             | 표시 이름      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 단추 B 누름  | 

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
| 장치 속성 | 디바이스 위치   | location  | location    |
| 텍스트            | 제조 일자     | manufacturedIn   | 해당 없음       |



## <a name="next-steps"></a>다음 단계

DevKit 디바이스를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

* [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)
