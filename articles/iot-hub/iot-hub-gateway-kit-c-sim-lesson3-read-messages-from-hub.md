---
title: "시뮬레이션된 장치 및 Azure IoT 게이트웨이 - 단원 3: 메시지 읽기 | Microsoft Docs"
description: "IoT Hub에서 메시지를 읽으려면 호스트 컴퓨터에서 샘플 코드를 실행합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "클라우드의 데이터, 클라우드 데이터 수집, iot 클라우드 서비스, iot 데이터"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-from-your-iot-hub"></a>IoT Hub에서 메시지 읽기

## <a name="what-you-will-do"></a>수행할 사항

- IoT Hub에서 메시지를 읽으려면 호스트 컴퓨터에서 샘플 코드를 실행합니다.

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-sim-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

Gulp 도구를 사용하여 IoT Hub에서 메시지를 읽는 방법

## <a name="what-you-need"></a>필요한 항목

- [시뮬레이트된 장치 클라우드 업로드 샘플 응용 프로그램 구성 및 실행](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)의 시뮬레이트된 장치 샘플

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT Hub 및 장치 연결 문자열 가져오기

장치 연결 문자열은 시뮬레이트된 장치를 IoT Hub에 연결하는 데 사용됩니다. IoT Hub 연결 문자열은 IoT Hub에 연결할 수 있는 장치를 관리하기 위해 IoT Hub의 ID 레지스트리에 연결하는 데 사용됩니다.

- 다음 명령을 실행하여 리소스 그룹에 있는 모든 IoT Hub를 나열합니다.

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   변경하지 않았다면 `iot-gateway`을 `{resource group name}` 값으로 사용합니다.
- 다음 명령을 실행하여 IoT Hub 연결 문자열을 가져옵니다.

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}`은 단원 2에서 지정했던 이름입니다.

## <a name="configure-the-device-connection-for-the-sample-code"></a>샘플 코드에 대한 장치 연결 구성

다음 단계를 수행 `config-azure.json`에 있는 IoT hub 및 장치 연결 구성을 업데이트합니다.

1. 콘솔 창에서 다음 명령을 실행하여 Visual Studio 코드에서 `config-azure.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. `config-azure.json` 파일에서 다음 내용을 바꿉니다.

   ![config azure의 스크린 샷](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   `[IoT hub connection string]`을 IoT Hub 연결 문자열로 바꿉니다.

## <a name="read-messages-from-your-iot-hub"></a>IoT Hub에서 메시지 읽기

시뮬레이트된 장치 샘플 응용 프로그램을 실행하고 다음 명령으로 IoT Hub 메시지를 읽습니다.

```bash
gulp run --iot-hub
```

명령은 2초마다 IoT 허브로 메시지를 보내는 응용 프로그램을 실행합니다. 또한 메시지를 수신할 하위 프로세스를 생성합니다.

보내고 받는 메시지는 모두 호스트 시스템의 동일한 콘솔 창에 즉시 표시됩니다. 응용 프로그램은 40초에서 종료됩니다.

![보낸 메시지와 수신한 메시지가 있는 시뮬레이트된 샘플 응용 프로그램](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>요약

시뮬레이트된 장치를 사용하여 IoT Hub에 데이터를 보내는 샘플 응용 프로그램을 성공적으로 실행했습니다. IoT Hub에 보낸 메시지도 읽었습니다.

## <a name="next-steps"></a>다음 단계
[Azure 함수 앱 및 Azure Storage 계정 만들기](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)



