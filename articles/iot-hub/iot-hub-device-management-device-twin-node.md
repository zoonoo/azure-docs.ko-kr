---
title: IoT Hub 장치 관리 쌍 | Microsoft Docs
description: 장치 쌍을 사용하는 방법을 설명하는 Azure IoT Hub 장치 관리 자습서.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# 자습서: Node.js로 장치 쌍을 사용하는 방법(미리 보기)
[!INCLUDE [iot-hub-device-management-twin-selector](../../includes/iot-hub-device-management-twin-selector.md)]

## 소개
Azure IoT Hub 장치 관리는 물리적 장치의 서비스 쪽 표시인 장치 쌍을 도입합니다. 다음은 장치 쌍의 여러 가지 구성 요소를 보여 주는 다이어그램입니다.

![][img-twin]

이 자습서에서는 장치 속성에 집중합니다. 다른 구성 요소에 대한 자세한 내용은 [Azure IoT Hub 장치 관리 개요][lnk-dm-overview]를 참조하세요.

장치 속성은 물리적 장치를 설명하는 미리 정의된 속성 사전입니다. 물리적 장치는 각 장치 속성의 마스터이며 각 해당 값의 신뢰할 수 있는 저장소입니다. 이러한 속성에 대한 ‘궁극적으로 일관성 있는' 표현이 클라우드의 장치 쌍에 저장됩니다. 일관성 및 최신 상태는 아래에서 설명하는 동기화 설정에 따라 달라집니다. 장치 속성의 몇 가지 예로 펌웨어 버전, 배터리 수준, 제조 업체 이름이 포함됩니다.

## 장치 속성 동기화
물리적 장치는 장치 속성에 대한 신뢰할 수 있는 출처입니다. 물리적 장치에 대해 선택된 값은 [LWM2M][lnk-lwm2m]에서 설명하는 *관찰/알림* 패턴을 통해 IoT Hub의 장치 쌍에 자동으로 동기화됩니다.

물리적 장치가 IoT Hub에 연결할 때 선택된 장치 속성에 대한 *관찰*이 시작됩니다. 그런 다음 물리적 장치가 장치 속성 변경 내용을 IoT Hub에 *알립니다*. 이력을 구현하려면 **pmin**(알림 간의 최소 시간)을 5분으로 설정합니다. 즉, 각 속성에 대해 물리적 장치는 변경 내용이 있더라도 5분에 한 번 이상 자주 IoT Hub에 알리지 않습니다. 최신 상태를 보장하려면 **pmax**를 6시간으로 설정합니다. 즉, 각 속성에 대해 물리적 장치는 해당 기간 이내에 속성이 변경되지 않더라도 최소 6시간에 한 번 IoT Hub에 알립니다.

물리적 장치의 연결이 끊어지면 동기화가 중지됩니다. 동기화는 장치가 서비스에 다시 연결될 때 다시 시작됩니다. 언제나 속성에 대한 마지막 업데이트 시간을 확인하여 최신 상태를 확인할 수 있습니다.

자동으로 관찰되는 장치 속성의 전체 목록은 다음과 같습니다.

![][img-observed]

## 장치 쌍 샘플 실행
다음 샘플은 [Azure IoT Hub 장치 관리 시작][lnk-get-started] 자습서 기능을 확장합니다. 먼저 여러 가지 시뮬레이션 장치를 실행하고 장치 쌍을 사용하여 시뮬레이션된 장치에 대한 속성을 읽고 변경합니다.

### 필수 조건
이 샘플을 실행하기 전에 [Azure IoT Hub 장치 관리 시작][lnk-get-started]의 단계를 완료했어야 합니다. 즉, 시뮬레이션된 장치가 실행 중이어야 합니다. 이전에 프로세스를 완료한 경우 시뮬레이션된 장치를 지금 다시 시작하세요.

### 샘플 시작
샘플을 시작하려면 ```jobClient_devicePropertyReadWrite.js```을(를) 실행해야 합니다. 그러면 장치 쌍 및 물리적 장치에서 장치 속성을 읽습니다. 또한 물리적 장치에 대한 자이 속성도 변경합니다. 아래 단계에 따라 샘플을 시작하세요.

1. **azure-iot-sdks** 리포지토리를 복제한 루트 폴더에서 **azure-iot-sdks/node/service/samples** 디렉터리로 이동합니다.
2. **jobClient\_devicePropertyReadWrite.js**를 열고 자리 표시자를 IoT Hub 연결 문자열로 바꿉니다.
3. `node jobClient_devicePropertyReadWrite.js`을 실행합니다.

장치 쌍의 사용을 보여 주는 명령줄 창의 출력이 표시됩니다. 샘플이 다음 프로세스를 진행합니다.

1. 단순 읽기: 장치 쌍의 `BatteryLevel` 및 `Timezone` 장치 속성을 출력합니다.
2. 심층 읽기: 물리적 장치에서 `Timezone` 장치 속성을 읽습니다.
3. 단순 읽기: 장치 쌍의 `BatteryLevel` 및 `Timezone` 장치 속성을 출력합니다.
4. 심층 쓰기: 물리적 장치에 대한 **표준 시간대** 장치 속성을 씁니다.
5. 심층 읽기: 물리적 장치에서 **표준 시간대** 장치 속성을 읽어서 변경되었는지 확인합니다.
6. 단순 읽기: 장치 쌍의 `BatteryLevel` 및 `Timezone` 장치 속성을 출력합니다.

### 단순 읽기
*단순* 및 *심층* 읽기/쓰기 간에 차이점이 있습니다. 단순 읽기는 Azure IoT Hub에 저장된 장치 쌍에서 요청한 속성의 값을 반환합니다. 이는 이전 알림 작업에서 나온 값입니다. 물리적 장치는 장치 속성에 대한 신뢰할 수 있는 출처이므로 단순 읽기를 수행할 수 없습니다. 단순 읽기는 단지 장치 쌍에서 속성을 읽기만 합니다.

```
deviceInfo.deviceProperties.BatteryLevel.value
```

이러한 값의 최신 상태를 결정하려면 마지막 업데이트된 시간을 확인할 수 있습니다.

```
deviceInfo.deviceProperties.BatteryLevel.lastUpdatedTime
```

마찬가지로 장치 쌍에만 저장된 서비스 속성을 읽을 수 있습니다. 그러한 속성은 장치에 동기화되지 않습니다.

### 심층 읽기
심층 읽기는 장치 작업을 시작하고 물리적 장치에서 요청한 속성의 값을 읽습니다. 장치 작업은 [Azure IoT 장치 관리 개요][lnk-dm-overview]에 소개되어 있으며 [자습서: 장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-dm-jobs]에 자세히 설명되어 있습니다. 최신 상태는 알림 간격의 제한을 받지 않으므로 심층 읽기는 장치 속성에 대한 더 최신의 값을 제공합니다. 작업은 물리적 장치에 메시지를 보내고 지정된 속성에 대해서만 최신 값으로 장치 쌍을 업데이트합니다. 전체 장치 쌍을 새로 고치지 않습니다.

```
scheduleDevicePropertyRead(jobId, deviceIds, propertyNames, done)
```

서비스 속성 또는 태그는 장치에 동기화되지 않으므로 이들에 대해서는 심층 읽기를 수행할 수 없습니다.

### 심층 쓰기
쓰기 가능 장치 속성을 변경하려는 경우 장치 작업을 시작하고 물리적 장치에 대한 값을 쓰는 심층 읽기를 사용할 수 있습니다. 일부 장치 속성은 쓰기가 불가능합니다. 전체 목록은 [Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개][lnk-dm-library]의 부록 A를 참조하세요.

작업은 지정된 속성을 업데이트하라는 메시지를 물리적 장치에 보냅니다. 장치 쌍은 작업이 완료될 때 즉시 업데이트되지 않습니다. 다음 알림 간격까지 기다려야 합니다. 동기화가 이루어진 후 단순 읽기를 사용하여 장치의 변경 내용을 확인할 수 있습니다.

```
scheduleDevicePropertyWrite(jobId, deviceIds, properties, done)
```

### 장치 시뮬레이터 구현 세부 정보
관찰/알림 패턴 및 심층 읽기/쓰기를 구현하기 위해 장치 쪽에서 수행해야 하는 작업을 알아보겠습니다.

장치 속성의 동기화는 Azure IoT Hub DM 클라이언트 라이브러리를 통해 완전히 처리되므로 일정한 간격으로 장치 속성(이 예제의 경우 배터리 수준)을 설정하는 API를 호출하기만 하면 됩니다. 서비스가 심층 읽기를 수행하는 경우 설정한 마지막 값이 반환됩니다. 서비스가 심층 쓰기를 수행하는 경우 이 Set 메서드가 호출됩니다. **iotdm\_simple\_sample.c**에서 이에 대한 예제를 볼 수 있습니다.

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

Set 메서드를 사용하는 대신 콜백을 구현할 수 있습니다. 이 옵션에 대한 추가 정보는 [Azure IoT Hub 장치 관리 라이브러리 소개][lnk-dm-library]를 참조하세요.

## 다음 단계
Azure IoT Hub 장치 관리 기능에 대해 자세히 알아보려면 이 자습서를 차례로 실행해 볼 수 있습니다.

* [쿼리를 사용하여 장치 쌍을 찾는 방법][lnk-tutorial-queries]
* [장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-tutorial-jobs]
* [IoT 게이트웨이 뒤에서 관리되는 장치 사용][lnk-dm-gateway]
* [Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개][lnk-library-c]
* 장치 관리 클라이언트 라이브러리는 [Intel Edison 장치][lnk-edison]를 사용하여 종단 간 샘플을 제공합니다.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [솔루션 디자인][lnk-design]
* [개발자 가이드][lnk-devguide]
* [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
* [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample

[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->