---
title: "Azure IoT에 Raspberry Pi(노드) 연결 - 단원 3: 샘플 실행 | Microsoft Docs"
description: "IoT Hub에 메시지를 보내고 LED를 깜빡이는 샘플 응용 프로그램을 Raspberry Pi 3에 배포하고 실행합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "깜박임 LED 클라우드 Pi, 클라우드에서 LED 깜빡이기"
ms.assetid: 427d8e5e-8af8-4249-8607-44edc90a4972
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d33229fea3ed8af636f744992cb8ed321418da85
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>샘플 응용 프로그램을 실행하여 장치-클라우드 메시지 보내기
## <a name="what-you-will-do"></a>수행할 사항
이 문서는 IoT Hub에 메시지를 보내는 샘플 응용 프로그램을 Raspberry Pi 3에 배포하고 실행하는 방법을 보여줍니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
Gulp 도구를 사용하여 Pi에서 샘플 Node.js 응용 프로그램을 배포하고 실행하는 방법을 알아봅니다.

## <a name="what-you-need"></a>필요한 항목
* 이 작업을 시작하기 전에 [IoT Hub 메시지를 처리하고 저장하기 위해 Azure 함수 앱 및 저장소 계정 만들기](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)를 완료해야 합니다.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT Hub 및 장치 연결 문자열 가져오기
장치 연결 문자열은 Pi를 IoT Hub에 연결하는 데 사용됩니다. IoT Hub 연결 문자열은 IoT Hub에 연결할 수 있는 장치를 관리하기 위해 IoT Hub의 ID 레지스트리에 연결하는 데 사용됩니다. 

* 다음 Azure CLI 명령을 실행하여 리소스 그룹에 있는 모든 IoT Hub를 나열합니다.

```bash
az iot hub list -g iot-sample --query [].name
```

값을 변경하지 않았다면 `iot-sample`을 `{resource group name}` 값으로 사용합니다.

* 다음 Azure CLI 명령을 실행하여 IoT Hub 연결 문자열을 가져옵니다.

```bash
az iot hub show-connection-string --name {my hub name} -g iot-sample
```

`{my hub name}`은 IoT Hub를 만들고 Pi를 등록할 때 지정한 이름입니다.

* 다음 명령을 실행하여 장치 연결 문자열을 가져옵니다.

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myraspberrypi -g iot-sample
```

값을 변경하지 않았다면 `myraspberrypi`을 `{device id}` 값으로 사용합니다.

## <a name="configure-the-device-connection"></a>장치 연결 구성
1. 다음 명령을 실행하여 구성 파일을 초기화합니다.
   
   ```bash
   npm install
   gulp init
   ```
2. 다음 명령을 실행하여 Visual Studio Code에서 장치 구성 파일 `config-raspberrypi.json`을 엽니다.
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
  
   ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. `config-raspberrypi.json` 파일에서 다음 내용을 바꿉니다.
   
   * **[device hostname or IP address]**를 장치 IP 주소 또는 `device-discovery-cli`에서 가져온 호스트 이름 또는 장치를 구성할 때 상속된 값으로 바꿉니다.
   * **[IoT device connection string]**을 가져온 `device connection string`으로 바꿉니다.
   * **[IoT hub connection string]**을 가져온 `iot hub connection string`으로 바꿉니다.

컴퓨터에서 샘플 응용 프로그램을 배포할 수 있도록 `config-raspberrypi.json` 파일을 업데이트합니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Pi에 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>샘플 응용 프로그램 작동 확인
Pi에 연결된 LED가&2;초마다 깜빡이는 것을 볼 수 있습니다. LED가 깜빡일 때마다 샘플 응용 프로그램은 IoT Hub에 메시지를 전송하고 해당 메시지가 IoT Hub에 성공적으로 전송되었는지 확인합니다. 또한 IoT Hub가 수신한 각 메시지가 콘솔 창에 출력됩니다. 샘플 응용 프로그램은 메시지를 20개 보낸 후에 자동으로 종료됩니다.

![보낸 메시지와 수신한 메시지가 있는 샘플 응용 프로그램](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="summary"></a>요약
깜빡이는 샘플 응용 프로그램을 새로 배포하고 실행하여 IoT Hub에 장치-클라우드 메시지를 보냈습니다. 이제 저장소 계정에 메시지가 작성될 때 해당 메시지를 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Storage에 유지되는 메시지 읽기](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)


