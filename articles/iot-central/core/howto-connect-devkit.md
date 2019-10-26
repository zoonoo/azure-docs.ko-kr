---
title: Azure IoT Central 애플리케이션에 DevKit 디바이스 연결 | Microsoft Docs
description: 디바이스 개발자로서 Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스를 연결하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 24ce6de63d06402e24abdeb33996a3853175ce91
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954410"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스 연결

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 디바이스 개발자로서 MXChip IoT DevKit(DevKit) 디바이스를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 다음 리소스가 필요 합니다.

1. **샘플 Devkits** 애플리케이션 템플릿으로 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
1. DevKit 디바이스. DevKit 디바이스를 구매하려면 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 방문하세요.

## <a name="sample-devkits-application"></a>샘플 Devkits 애플리케이션

**샘플 devkits** 응용 프로그램 템플릿에서 만든 응용 프로그램에는 다음과 같은 장치 특성을 정의 하는 **MXChip** 장치 템플릿이 포함 되어 있습니다.

- **습도**, **온도**, **압력**, **지자기 센터** (x, y, z 축을 따라 측정), **가 속도계** (x, y, z 축을 따라 측정 됨) 및 **자이로스코프가** (x, y, z 축을 따라 측정 됨)에 대 한 원격 분석 측정.
- **장치 상태**에 대 한 상태 측정입니다.
- **단추 B를 누른**상태에서 이벤트를 측정 합니다.
- **전압**, **전류**, **팬 속도**및 **IR** 전환에 대 한 설정입니다.
- 장치 속성 **주사위 번호** 및 **장치 위치**(위치 속성)입니다.
- **에서 제조**되는 클라우드 속성
- 명령 **에코** 및 **카운트다운**. 실제 장치에 **Echo** 명령이 수신 되 면 장치 디스플레이에 전송 된 값이 표시 됩니다. 실제 장치에 **카운트다운** 명령이 수신 되 면 LED는 패턴을 순환 하 고 장치는 카운트다운 값을 IoT Central으로 다시 보냅니다.

구성에 대 한 자세한 내용은 [MXChip Device template details](#mxchip-device-template-details) 항목을 참조 하세요.

## <a name="add-a-real-device"></a>실제 디바이스 추가

### <a name="get-your-device-connection-details"></a>장치 연결 정보 가져오기

Azure IoT Central 응용 프로그램에서 **MXChip** 장치 템플릿의 실제 장치를 추가 하 고 장치 연결 정보: **범위 ID, 장치 ID 및 기본 키**를 적어둡니다.

1. Device Explorer에서 **실제 장치** 를 추가 하 고 **+ 새로 > 만들기** 를 선택 하 여 실제 장치를 추가 합니다.

    * 소문자 **장치 id**를 입력 하거나 제안 된 **장치 id**를 사용 합니다.
    * **장치 이름을**입력 하거나 제안 된 이름을 사용 합니다.

    ![디바이스 추가](media/howto-connect-devkit/add-device.png)

1. 장치 연결 정보, **범위 ID**, **장치 id**및 **기본 키**를 가져오려면 장치 페이지에서 **연결** 을 선택 합니다.

    ![연결 세부 정보](media/howto-connect-devkit/device-connect.png)

1. 연결 정보를 적어 둡니다. 다음 단계에서 DevKit 장치를 준비할 때 인터넷에서 일시적으로 연결을 끊었습니다.

### <a name="prepare-the-devkit-device"></a>DevKit 디바이스 준비

이전에 장치를 사용 하 고 다른 WiFi 네트워크, 연결 문자열 또는 원격 분석 측정을 사용 하도록 다시 구성 하려면 **a** 및 **B** 단추를 모두 동시에 누릅니다. 작동 하지 않는 경우 다시 **설정** 단추를 누르고 다시 시도 합니다.

#### <a name="to-prepare-the-devkit-device"></a>DevKit 디바이스를 준비하려면

1. GitHub의 [릴리스](https://aka.ms/iotcentral-docs-MXChip-releases) 페이지에서 MXChip의 최신 사전 빌드 Azure IoT Central 펌웨어를 다운로드합니다.
1. USB 케이블을 사용하여 DevKit 디바이스를 개발 컴퓨터에 연결합니다. Windows에서, DevKit 디바이스의 스토리지에 매핑된 드라이브에서 파일 탐색기 창이 열립니다. 예를 들어 드라이브 이름이 **AZ3166 (D:)** 일 수 있습니다.
1. **iotCentral.bin** 파일을 드라이브 창으로 끌어다 놓습니다. 복사가 완료되면 새 펌웨어를 사용하여 디바이스가 다시 부팅됩니다.

1. DevKit 디바이스가 다시 시작되면 다음 화면이 표시됩니다.

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 화면이 다른 요소를 표시하는 경우 디바이스를 다시 설정하고 디바이스에서 **A** 및 **B** 단추를 동시에 눌러 디바이스를 다시 부팅합니다.

1. 이제 디바이스가 AP(액세스 지점) 모드에 있습니다. 컴퓨터 또는 모바일 디바이스에서 이 WiFi 액세스 지점에 연결할 수 있습니다.

1. 컴퓨터, 휴대폰 또는 태블릿에서 디바이스 화면에 표시된 WiFi 네트워크 이름에 연결합니다. 이 네트워크에 연결 하면 인터넷에 액세스할 수 없습니다. 이 상태는 정상적인 상태 이며 장치를 구성 하는 동안 잠시 동안만이 네트워크에 연결 됩니다.

1. 웹 브라우저를 열고 [http://192.168.0.1/start](http://192.168.0.1/start)로 이동합니다. 다음 웹 페이지가 표시됩니다.

    ![디바이스 구성 페이지](media/howto-connect-devkit/configpage.png)

    웹 페이지에서 다음을 입력 합니다.
    - WiFi 네트워크의 이름
    - 사용자의 WiFi 네트워크 암호
    - 장치 디스플레이에 표시 되는 PIN 코드
    - 연결 정보 **범위 ID**, **장치 Id**및 장치의 **기본 키** (단계를 수행 하 여이를 이미 저장 했어야 함)
    - 사용 가능한 원격 분석 측정을 모두 선택 합니다.

1. **디바이스 구성**을 선택하면 이 페이지가 표시됩니다.

    ![구성된 디바이스](media/howto-connect-devkit/deviceconfigured.png)

1. 디바이스의 **재설정** 단추를 누릅니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

DevKit 디바이스가 다시 시작되면 디바이스에 다음과 같은 화면이 표시됩니다.

* 전송된 원격 분석 메시지 수.
* 실패 횟수.
* 받은 desired 속성 수 및 보낸 reported 속성 수.

> [!NOTE]
> 장치에서 연결을 시도할 때 루프가 반복 되는 것으로 표시 되 면 장치가 IoT Central에서 **차단** 되었는지 확인 하 고 장치를 **차단 해제** 하 여 앱에 연결할 수 있습니다.

보고 된 속성을 보내기 위해 장치를 흔듭니다. 디바이스에서 **다이 번호** 디바이스 속성으로 임의의 숫자를 보냅니다.

원격 분석 측정값 및 reported 속성 값을 확인하고, Azure IoT Central에서 설정을 구성할 수 있습니다.

1. **Device Explorer**를 사용하여 추가한 실제 MXChip 디바이스에 대한 **측정값** 페이지로 이동합니다.

    ![실제 디바이스로 이동](media/howto-connect-devkit/realdevicenew.png)

1. **측정값** 페이지에서 MXChip 디바이스에서 보낸 원격 분석 데이터를 볼 수 있습니다.

    ![실제 디바이스의 원격 분석 데이터 보기](media/howto-connect-devkit/devicetelemetrynew.png)

1. **속성** 페이지에서는 디바이스에서 보고된 마지막 다이 번호 및 디바이스 위치를 볼 수 있습니다.

    ![디바이스 속성 보기](media/howto-connect-devkit/devicepropertynew.png)

1. **설정** 페이지에서 MXChip 디바이스의 설정을 업데이트할 수 있습니다.

    ![디바이스 설정 보기](media/howto-connect-devkit/devicesettingsnew.png)

1. **명령** 페이지에서 **Echo** 및 **카운트다운** 명령을 호출할 수 있습니다.

    ![Call 명령](media/howto-connect-devkit/devicecommands.png)

1. **대시보드** 페이지에서는 위치 지도를 볼 수 있습니다.

    ![디바이스 대시보드 보기](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>소스 코드 다운로드

디바이스 코드를 탐색하고 수정하려는 경우 GitHub에서 디바이스 코드를 다운로드하면 됩니다. 코드를 수정하려는 경우 다음 지침에 따라 사용 중인 데스크톱 운영 체제의 [개발 환경을 준비](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)해야 합니다.

소스 코드를 다운로드하려면 데스크톱 컴퓨터에서 다음 명령을 실행합니다.

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

이전 명령은 `iot-central-firmware` 폴더에 소스 코드를 다운로드합니다.

> [!NOTE]
> 개발 환경에 **git**이 설치되지 않은 경우 [https://git-scm.com/download](https://git-scm.com/download)에서 다운로드할 수 있습니다.

## <a name="review-the-code"></a>코드 검토

Visual Studio Code를 사용 하 여 `iot-central-firmware` 폴더의 `MXCHIP/mxchip_advanced` 폴더를 엽니다.

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

원격 분석이 Azure IoT Central 응용 프로그램으로 전송 되는 방법을 보려면 `src` 폴더에서 **원격 분석 .cpp** 파일을 엽니다.

- `TelemetryController::buildTelemetryPayload` 함수는 디바이스의 센서 데이터를 사용하여 JSON 원격 분석 페이로드를 만듭니다.

- 함수 `TelemetryController::sendTelemetryPayload`는 **Azureiotclient** 에서 `sendTelemetry`를 호출 하 여 Azure IoT Central 응용 프로그램에서 사용 하는 IoT Hub JSON 페이로드를 보냅니다.

Azure IoT Central 응용 프로그램에 속성 값을 보고 하는 방법을 보려면 `src` 폴더에서 **원격 분석 .cpp** 파일을 엽니다.

- `TelemetryController::loop` 함수는 보고 된 **위치** 속성을 약 30 초 마다 보냅니다. 이 클래스는 **Azureiotclient .cpp** 소스 파일에서 `sendReportedProperty` 함수를 사용 합니다.

- `TelemetryController::loop` 함수는 장치가 속도계가 두 번 탭을 검색할 때 보고 된 **dieNumber** 속성을 보냅니다. 이 클래스는 **Azureiotclient .cpp** 소스 파일에서 `sendReportedProperty` 함수를 사용 합니다.

장치가 IoT Central 응용 프로그램에서 호출 되는 명령에 응답 하는 방법을 보려면 `src` 폴더에서 **registeredMethodHandlers** 파일을 엽니다.

- **DmEcho** 함수는 **echo** 명령에 대 한 처리기입니다. 장치의 화면에서 페이로드의 **displayedValue** 을 표시 합니다.

- **Dmcountdown** 함수는 **카운트다운** 명령에 대 한 처리기입니다. 장치의 LED 색을 변경 하 고 보고 된 속성을 사용 하 여 카운트다운 값을 IoT Central 응용 프로그램으로 다시 보냅니다. 보고 된 속성의 이름은 명령과 동일 합니다. 함수는 **Azureiotclient .cpp** 소스 파일에서 `sendReportedProperty` 함수를 사용 합니다.

**Azureiotclient .cpp** 소스 파일의 코드는 [Microsoft Azure IoT sdk 및 C 용 라이브러리](https://github.com/Azure/azure-iot-sdk-c) 의 함수를 사용 하 여 IoT Hub와 상호 작용 합니다.

샘플 코드를 수정하고, 빌드하고, 디바이스에 업로드하는 방법은 `MXCHIP/mxchip_advanced` 폴더에 있는 **readme.md** 파일을 참조하세요.

## <a name="mxchip-device-template-details"></a>MXChip 디바이스 템플릿 세부 정보

샘플 Devkits 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 MXChip 디바이스가 포함됩니다.

### <a name="measurements"></a>측정값

#### <a name="telemetry"></a>원격 분석

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

#### <a name="states"></a>상태 
| name          | 표시 이름   | 정상 | 주의 | 위험 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 디바이스 상태   | 녹색  | Orange  | 빨강    | 

#### <a name="events"></a>이벤트 
| name             | 표시 이름      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 단추 B 누름  | 

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | 단위 | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| 표시 이름 | 필드 이름 | 온 텍스트 | 오프 텍스트 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 켜기      | 끄기      | 해제     |

### <a name="properties"></a>properties

| Type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number    |
| 디바이스 속성 | 디바이스 위치   | location  | location    |
| 텍스트            | 제조 일자     | manufacturedIn   | N/A       |

### <a name="commands"></a>명령

| 표시 이름 | 필드 이름 | 반환 형식 | 입력 필드 표시 이름 | 입력 필드 이름 | 입력 필드 형식 |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| 울림         | echo       | text        | 표시할 값         | displayedValue   | text             |
| 다운    | 다운  | number      | 개수               | countFrom        | number           |

## <a name="next-steps"></a>다음 단계

이제 MXChip IoT DevKit를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정](howto-set-up-template.md) 하는 방법을 배우는 것입니다.
