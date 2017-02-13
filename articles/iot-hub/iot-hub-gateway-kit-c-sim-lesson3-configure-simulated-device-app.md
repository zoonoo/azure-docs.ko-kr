---
title: "데이터를 Azure IoT Hub에 보내기 위해 시뮬레이트된 장치 샘플 앱 실행 | Microsoft Docs"
description: "시뮬레이트된 장치 샘플 앱을 실행하여 온도 데이터를 IoT Hub에 보내기"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "데이터를 클라우드로"
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 94bffa363f7979b7dc37363328a358f0b139307d


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>시뮬레이트된 장치 샘플 앱 구성 및 실행

## <a name="what-you-will-do"></a>수행할 사항

- 샘플 리포지토리를 복제합니다.
- Azure CLI를 사용하여 시뮬레이트된 장치 샘플 응용 프로그램에 대한 IoT Hub 및 논리적 장치 정보를 가져옵니다. 시뮬레이트된 장치 샘플 응용 프로그램을 구성하고 실행합니다.

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-sim-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

이 문서에서는 다음에 대해 알아봅니다.

- 시뮬레이트된 장치 샘플 응용 프로그램을 구성하고 실행하는 방법

## <a name="what-you-need"></a>필요한 항목

다음을 완료해야 합니다.

- [IoT Hub 만들기 및 장치 등록](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>호스트 컴퓨터에 샘플 리포지토리 복제

샘플 리포지토리를 복제하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

1. Windows에서 명령 프롬프트를 열거나 macOS 또는 Ubuntu에서 터미널을 엽니다.
2. 다음 명령을 실행합니다.

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>시뮬레이트된 장치 및 NUC 구성

1. 다음 명령을 실행하여 Visual Studio Code에서 구성 파일인 `config.json` 파일을 엽니다.

   ```bash
   code config.json
   ```

2. `"has_sensortag": true`을 `"has_sensortag": false`로 바꿉니다.

   ![TI SensorTag 장치가 없는 구성](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. 다음 명령을 실행하여 구성 파일을 초기화합니다.

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. 다음 명령을 실행하여 Visual Studio Code에서 `config-gateway.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. 다음 코드 줄을 찾아 `[device hostname or IP address]`를 Intel NUC의 IP 주소 또는 호스트 이름으로 바꿉니다.
   ![config gateway의 스크린샷](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>IoT Hub 논리적 장치의 연결 문자열 가져오기

논리적 장치의 Azure IoT Hub 연결 문자열을 가져오려면 호스트 컴퓨터에서 다음 명령을 실행합니다.

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}`은 사용한 IoT Hub 이름입니다. 단원 2에서 값을 변경하지 않았다면 iot-gateway를 `{resource group name}` 값으로 사용하고 mydevice를 `{device id}` 값으로 사용합니다.

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>시뮬레이트된 장치 클라우드 업로드 샘플 응용 프로그램 구성

시뮬레이트된 장치 클라우드 업로드 샘플 응용 프로그램을 구성하고 실행하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 Visual Studio Code에서 `config-sensortag.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![config sensortag의 스크린샷](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. 코드에서 다음 내용을 바꿉니다.
   - `[IoT hub name]`을 IoT Hub 이름으로 바꿉니다.
   - `[IoT device connection string]`을 IoT Hub 논리적 장치의 연결 문자열로 바꿉니다.

3. 응용 프로그램을 실행합니다.

   다음 명령을 실행하여 응용 프로그램을 배포하고 실행합니다.

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>샘플 응용 프로그램 작동 확인

이제 다음과 유사한 출력이 표시됩니다.

![시뮬레이트된 장치 샘플 응용 프로그램 출력](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

응용 프로그램은 40초 동안 온도 데이터를 IoT Hub로 전송합니다.

## <a name="summary"></a>요약

시뮬레이트된 장치로 IoT Hub에 데이터를 보내는 시뮬레이트된 장치 클라우드 업로드 샘플 응용 프로그램을 성공적으로 구성하고 실행했습니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub에서 메시지 읽기](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)


<!--HONumber=Dec16_HO3-->


