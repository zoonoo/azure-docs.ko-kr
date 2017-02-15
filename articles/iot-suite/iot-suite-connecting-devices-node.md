---
title: "Node.js를 사용하여 장치 연결 | Microsoft Docs"
description: "Node.js에 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 02247d679c9bffa01b8d2827ea57eba38894107e
ms.openlocfilehash: 698384bb5079feb937f423f9b6365dff20cfa3be


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>미리 구성된 원격 모니터링 솔루션에 장치 연결(Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>node.js 샘플 솔루션 빌드 및 실행
1. Node.js용 *Microsoft Azure IoT SDK* GitHub 리포지토리를 복제하고 데스크톱 환경에 설치하려면 [개발 환경 준비][lnk-github-prepare] 지침을 따르세요.
2. [azure-iot-sdk-node][lnk-github-repo] 리포지토리의 로컬 복사본에서device/samples 폴더에 있는 다음 두 파일을 장치의 폴더로 복사합니다.
   
   * package.json
   * remote_monitoring.js
3. remote_monitoring.js 파일을 열고 다음 변수를 찾습니다.
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. **[IoT Hub device connection string]** 을 장치 연결 문자열로 바꿉니다. 원격 모니터링 솔루션 대시보드에서 IoT Hub 호스트 이름, 장치 ID 및 장치 키에 대한 값을 확인할 수 있습니다. 장치 연결 문자열은 다음과 같은 형식입니다.
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    IoT Hub 호스트 이름이 **contoso**이고 장치 ID가 **mydevice**이면 연결 문자열은 다음과 같습니다.
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. 파일을 저장합니다. 이러한 파일이 포함된 폴더에서 명령 프롬프트에 다음 명령을 입력하여 필요한 패키지를 설치하고 샘플 응용 프로그램을 실행합니다.
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md



<!--HONumber=Dec16_HO3-->


