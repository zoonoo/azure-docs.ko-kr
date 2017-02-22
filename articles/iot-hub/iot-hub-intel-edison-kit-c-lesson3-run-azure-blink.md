---
title: "Azure IoT에 Intel Edison(C) 연결 - 단원 3: 메시지 전송 | Microsoft Docs"
description: "IoT Hub에 메시지를 보내고 LED를 깜빡이는 샘플 응용 프로그램을 Intel Edison에 배포하고 실행합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 클라우드 서비스, arduino 클라우드, arduino 클라우드에 데이터 전송"
ms.assetid: 12672b64-795a-4dfc-86fd-df53ed3eeef7
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 5c63363660845f970b03f67363cc495ef8cb94c7


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>샘플 응용 프로그램을 실행하여 장치-클라우드 메시지 보내기
## <a name="what-you-will-do"></a>수행할 사항
이 문서는 IoT Hub에 메시지를 보내는 샘플 응용 프로그램을 Intel Edison에 배포하고 실행하는 방법을 보여줍니다. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
Gulp 도구를 사용하여 Edison에서 샘플 C 응용 프로그램을 배포하고 실행하는 방법을 알아봅니다.

## <a name="what-you-need"></a>필요한 항목
* 이 작업을 시작하기 전에 [IoT Hub 메시지를 처리하고 저장하기 위해 Azure 함수 앱 및 저장소 계정 만들기][process-and-store-iot-hub-messages]를 완료해야 합니다.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT Hub 및 장치 연결 문자열 가져오기
장치 연결 문자열은 Edison을 IoT Hub에 연결하는 데 사용됩니다. IoT Hub 연결 문자열은 IoT Hub를 장치 ID에 연결하는 데 사용됩니다. 이 ID는 IoT Hub에서 Edison을 나타냅니다.

* 다음 Azure CLI 명령을 실행하여 리소스 그룹에 있는 모든 IoT Hub를 나열합니다.

```bash
az iot hub list -g iot-sample --query [].name
```

값을 변경하지 않았다면 `iot-sample`을 `{resource group name}` 값으로 사용합니다.

* 다음 Azure CLI 명령을 실행하여 IoT Hub 연결 문자열을 가져옵니다.

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}`은 IoT Hub를 만들고 Edison을 등록할 때 지정한 이름입니다.

* 다음 명령을 실행하여 장치 연결 문자열을 가져옵니다.

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myinteledison
```

값을 변경하지 않았다면 `myinteledison`을 `{device id}` 값으로 사용합니다.

## <a name="configure-the-device-connection"></a>장치 연결 구성
1. 다음 명령을 실행하여 구성 파일을 초기화합니다.

   ```bash
   npm install
   gulp init
   ```
   > [!NOTE]
   > 단원 1에서 **gulp install-tools**를 실행하지 않았다면 이 명령도 실행합니다.

2. 다음 명령을 실행하여 Visual Studio Code에서 장치 구성 파일 `config-edison.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

   ![config.json](media/iot-hub-intel-edison-lessons/lesson3/config.png)

3. `config-edison.json` 파일에서 다음 내용을 바꿉니다.

   * **[device hostname or IP address]**를 장치를 구성할 때 표시한 장치 IP 주소로 바꿉니다.
   * **[IoT device connection string]**을 가져온 `device connection string`으로 바꿉니다.
   * **[IoT hub connection string]**을 가져온 `iot hub connection string`으로 바꿉니다.

   > [!NOTE]
   > 이 문서에서는 `azure_storage_connection_string`이 필요하지 않습니다. 그대로 유지합니다.

## <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Edison에서 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>샘플 응용 프로그램 작동 확인
Edison에 연결된 LED가&2;초마다 깜빡이는 것을 볼 수 있습니다. LED가 깜빡일 때마다 샘플 응용 프로그램은 IoT Hub에 메시지를 전송하고 해당 메시지가 IoT Hub에 성공적으로 전송되었는지 확인합니다. 또한 IoT Hub가 수신한 각 메시지가 콘솔 창에 출력됩니다. 샘플 응용 프로그램은 메시지를 20개 보낸 후에 자동으로 종료됩니다.

![보낸 메시지와 수신한 메시지가 있는 샘플 응용 프로그램][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>요약
Edison에서 깜빡이는 샘플 응용 프로그램을 새로 배포하고 실행하여 IoT Hub에 장치-클라우드 메시지를 보냈습니다. 이제 저장소 계정에 메시지가 작성될 때 해당 메시지를 모니터링합니다.

## <a name="next-steps"></a>다음 단계
[Azure Storage에 유지되는 메시지 읽기][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-intel-edison-lessons/lesson3/gulp_run_c.png
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md


<!--HONumber=Jan17_HO4-->


