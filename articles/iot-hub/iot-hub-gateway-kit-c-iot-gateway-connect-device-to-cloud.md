---
title: "IoT 게이트웨이를 사용하여 장치를 Azure IoT Hub에 연결 | Microsoft Docs"
description: "Intel NUC를 IoT 게이트웨이로 사용하여 클라우드에서 TI SensorTag를 연결하고 Azure IoT Hub로 센서 데이터를 보내는 방법을 알아봅니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 게이트웨이로 장치를 클라우드에 연결"
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>IoT 게이트웨이를 사용하여 장치를 클라우드에 연결 - SensorTag-Azure IoT Hub

> [!NOTE]
> 이 자습서를 시작하기 전에 먼저 [Intel NUC를 IoT 게이트웨이로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)을 완료해야 합니다. [Intel NUC를 IoT 게이트웨이로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)에서 Intel NUC 장치를 IoT 게이트웨이로 설정했습니다.

## <a name="what-you-will-learn"></a>알아볼 내용

IoT 게이트웨이를 사용하여 Texas Instruments SensorTag(CC2650STK)를 Azure IoT Hub에 연결하는 방법을 알아봅니다. IoT 게이트웨이는 SensorTag에서 수집된 온도 및 습도 데이터를 Azure IoT Hub로 보냅니다.

## <a name="what-you-will-do"></a>수행할 사항

- IoT Hub를 만듭니다.
- IoT Hub에 SensorTag용 장치를 등록합니다.
- IoT 게이트웨이와 SensorTag 간의 연결을 설정합니다.
- BLE 샘플 응용 프로그램을 실행하여 IoT Hub로 SensorTag 데이터를 보냅니다.

## <a name="what-you-need"></a>필요한 항목

- [Intel NUC를 IoT 게이트웨이로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) 자습서를 완료하여 Intel NUC를 IoT 게이트웨이로 설정.
- * 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
- 호스트 컴퓨터에서 실행되는 SSH 클라이언트 - Windows의 경우 PuTTY를 사용하는 것이 좋습니다. Linux와 macOS의 경우 이미 SSH 클라이언트와 함께 제공됩니다.
- SSH 클라이언트에서 게이트웨이에 액세스하기 위한 IP 주소와 사용자 이름 및 암호.
- 인터넷 연결.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> 여기서 SensorTag용 새 장치 등록

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>IoT 게이트웨이와 SensorTag 간의 연결 설정

이 섹션에서는 다음 작업을 수행합니다.

- Bluetooth 연결용 SensorTag MAC 주소를 가져옵니다.
- IoT 게이트웨이에서 SensorTag로 Bluetooth 연결을 시작합니다.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Bluetooth 연결용 SensorTag MAC 주소 가져오기

1. 호스트 컴퓨터에서 SSH 클라이언트를 실행하고 IoT 게이트웨이에 연결합니다.
1. 다음 명령을 실행하여 Bluetooth를 차단 해제합니다.

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. IoT 게이트웨이에서 Bluetooth 서비스를 시작하고 다음 명령을 실행하여 Bluetooth를 구성하는 Bluetooth 셸을 입력합니다.

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Bluetooth shell에서 다음 명령을 실행하여 Bluetooth 컨트롤러의 전원을 켭니다.

   ```bash
   power on
   ```

   ![bluetoothctl을 사용하여 IoT 게이트웨이에서 Bluetooth 컨트롤러 전원 켜기](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. 다음 명령을 실행하여 주변의 Bluetooth 장치 검색을 시작합니다.

   ```bash
   scan on
   ```

   ![bluetoothctl을 사용하여 주변의 Bluetooth 장치 검색](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. SensorTag의 페어링 단추를 누릅니다. SensorTag의 녹색 LED가 깜박입니다.
1. Bluetooth shell에 SensorTag가 표시됩니다. SensorTag의 MAC 주소를 기록해 둡니다. 이 예에서는 SensorTag의 MAC 주소가 `24:71:89:C0:7F:82`입니다.
1. 다음 명령을 실행하여 검색을 끕니다.

   ```bash
   scan off
   ```

   ![bluetoothctl을 사용하여 주변의 Bluetooth 장치 검색 중지](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>IoT 게이트웨이에서 SensorTag로 Bluetooth 연결 시작

1. 다음 명령을 실행하여 SensorTag에 연결합니다.

   ```bash
   connect <MAC address>
   ```

   ![bluetoothctl을 사용하여 SensorTag에 연결](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. 다음 명령을 실행하여 SensorTag와의 연결을 끊고 Bluetooth 셸을 종료합니다.

   ```bash
   disconnect
   exit
   ```

   ![bluetoothctl을 사용하여 SensorTag와의 연결 해제](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

SensorTag와 IoT 게이트웨이와 간의 연결이 설정되었습니다.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>BLE 샘플 응용 프로그램을 실행하여 IoT Hub로 SensorTag 데이터 전송

BLE(Bluetooth Low Energy) 응용 프로그램 예제는 Azure IoT Edge에서 제공됩니다. 샘플 응용 프로그램은 BLE 연결에서 데이터를 수집하여 IoT hub로 보냅니다. 샘플 응용 프로그램을 실행하려면 다음 작업을 수행해야 합니다.

1. 샘플 응용 프로그램을 구성합니다.
1. IoT 게이트웨이에서 샘플 응용 프로그램을 실행합니다.

### <a name="configure-the-sample-application"></a>샘플 응용 프로그램 구성

1. 다음 명령을 실행하여 샘플 응용 프로그램의 폴더로 이동합니다.

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. 다음 명령을 실행하여 구성 파일을 엽니다.

   ```bash
   vi ble_gateway.json
   ```

1. 구성 파일에서 다음 값을 입력합니다.

   **IoTHubName**: IoT Hub의 이름입니다.

   **IoTHubSuffix**: 앞에서 기록해 둔 장치 연결 문자열의 기본 키에서 IoTHubSuffix를 가져옵니다. IoT Hub 연결 문자열의 기본 키가 아닌 장치 연결 문자열의 기본 키를 가져와야 합니다. 장치 연결 문자열의 기본 키는 `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY` 형식입니다.

   **Transport**: 기본값은 `amqp`입니다. 이 값은 전송 중 프로토콜을 표시합니다. `http`, `amqp` 또는 `mqtt`입니다.

   **macAddress**: 기록해 둔 SensorTag의 MAC 주소입니다.

   **deviceID**: IoT Hub에서 만든 장치 ID입니다.

   **deviceKey**: 장치 연결 문자열의 기본 키입니다.

   ![BLE 샘플 응용 프로그램의 구성 파일 완료](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. `ESC`를 누르고 `:wq`를 입력하여 파일을 저장합니다.

### <a name="run-the-sample-application"></a>샘플 응용 프로그램 실행

1. SensorTag 전원이 켜져 있는지 확인합니다.
1. 다음 명령을 실행합니다.

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>다음 단계

[IoT 게이트웨이를 사용하여 Azure IoT Edge를 통해 센서 데이터 변환](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
