---
title: "IoT 게이트웨이에서 Azure IoT Edge를 통해 데이터 변환 | Microsoft Docs"
description: "IoT 게이트웨이를 사용하여 Azure IoT Edge의 사용자 지정 모듈을 통해 센서 데이터의 형식을 변환합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT 게이트웨이 데이터 변환, IoT 게이트웨이 데이터 변환"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>IoT 게이트웨이를 사용하여 Azure IoT Edge를 통해 센서 데이터 변환

> [!NOTE]
> 이 자습서를 시작하기 전에 순서대로 다음 단원을 완료했는지 확인합니다.
> * [Intel NUC를 IoT 게이트웨이로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [IoT 게이트웨이를 사용하여 클라우드에 작업 연결 - SensorTag에서 Azure IoT Hub로](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Iot 게이트웨이의 한 가지 목적은 먼저 수집된 데이터를 처리한 후에 클라우드로 보내는 것입니다. Azure IoT Edge에서는 데이터 처리 워크플로를 구성하기 위해 만들고 어셈블할 수 있는 모듈이 도입되었습니다. 모듈은 메시지를 받고, 관련 작업을 수행한 다음, 다른 모듈에서 처리하도록 해당 메시지를 보냅니다.

## <a name="what-you-learn"></a>학습 내용

SensorTag의 메시지를 다른 형식으로 변환하는 모듈을 만드는 방법에 대해 알아봅니다.

## <a name="what-you-do"></a>수행할 작업

* 받은 메시지를 .json 형식으로 변환하는 모듈을 만듭니다.
* 모듈을 컴파일합니다.
* Azure IoT Edge의 BLE 샘플 응용 프로그램에 모듈을 추가합니다.
* 샘플 응용 프로그램을 실행합니다.

## <a name="what-you-need"></a>필요한 항목

* 이전에 순서대로 완료된 다음 자습서:
  * [Intel NUC를 IoT 게이트웨이로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [IoT 게이트웨이를 사용하여 클라우드에 작업 연결 - SensorTag에서 Azure IoT Hub로](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* 호스트 컴퓨터에서 실행되는 SSH 클라이언트 - Windows의 경우 PuTTY를 사용하는 것이 좋습니다. Linux와 macOS의 경우 이미 SSH 클라이언트와 함께 제공됩니다.
* SSH 클라이언트에서 게이트웨이에 액세스하기 위한 IP 주소와 사용자 이름 및 암호.
* 인터넷 연결

## <a name="create-a-module"></a>모듈 만들기

1. 호스트 컴퓨터에서 SSH 클라이언트를 실행하고 IoT 게이트웨이에 연결합니다.
1. 다음 명령을 실행하여 변환 모듈의 소스 파일을 GitHub에서 IoT 게이트웨이의 홈 디렉터리로 복제합니다.

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   이 모듈은 C 프로그래밍 언어로 작성된 네이티브 Azure Edge 모듈이며, 받은 메시지의 형식을 다음 형식으로 변환합니다.

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>모듈 컴파일

모듈을 컴파일하려면 다음 명령을 실행합니다.

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

컴파일이 완료되면 `libmy_module.so` 파일이 만들어집니다. 이 파일의 절대 경로를 기록해 두세요.

## <a name="add-the-module-to-the-ble-sample-application"></a>BLE 샘플 응용 프로그램에 모듈 추가

1. 다음 명령을 실행하여 samples 폴더로 이동합니다.

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. 다음 명령을 실행하여 구성 파일을 엽니다.

   ```bash
   vi ble_gateway.json
   ```

1. `modules` 섹션에 다음 코드를 삽입하여 모듈을 추가합니다.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. 코드의 `[Your libmy_module.so path]`를 libmy_module.so 파일의 절대 경로로 바꿉니다.
1. `links` 섹션의 코드를 다음 코드로 바꿉니다.

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. `ESC`를 누른 다음 `:wq`를 입력하여 파일을 저장합니다.

## <a name="run-the-sample-application"></a>샘플 응용 프로그램 실행

1. SensorTag의 전원을 켭니다.
1. 다음 명령을 실행하여 SSL_CERT_FILE 환경 변수를 설정합니다.

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. 다음 명령을 실행하여 추가된 모듈로 샘플 응용 프로그램을 실행합니다.

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>다음 단계

IoT 게이트웨이를 사용하여 SensorTag의 메시지를 .json 형식으로 성공적으로 변환했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
