---
title: "Azure 데이터 저장소에 IoT Hub 메시지 저장 | Microsoft Docs"
description: "Azure 함수 앱을 사용하여 Azure 테이블 저장소에 IoT Hub 메시지를 저장합니다. IoT Hub 메시지에는 IoT 장치에서 보낸 센서 데이터와 같은 정보가 있습니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 데이터 저장소, IoT 센서 데이터 저장소"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: de47a15dd4009b9a33a53fd981bc178eaa50c035
ms.lasthandoff: 03/30/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>Azure 테이블 저장소에 센서 데이터와 같은 정보를 포함한 IoT Hub 메시지 저장

> [!Note]
> 이 자습서를 시작하기 전에 [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md)을 완료했는지 확인합니다. [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md)에서 IoT 장치와 IoT Hub를 설정하고 장치에서 실행할 샘플 응용 프로그램을 배포합니다. 응용 프로그램에서 수집된 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-will-learn"></a>알아볼 내용

Azure 저장소 계정 및 Azure 함수 앱을 만들어 Azure 테이블 저장소에 IoT Hub 메시지를 저장하는 방법에 대해 알아봅니다.

## <a name="what-you-will-do"></a>수행할 사항

- Azure 저장소 계정 만들기
- 메시지를 읽도록 IoT Hub 연결 준비
- Azure 함수 앱 만들기 및 배포

## <a name="what-you-will-need"></a>필요한 사항

- 다음 요구 사항을 다루는 [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md) 자습서
  - 활성 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 실행 중인 응용 프로그램

## <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

1. Azure Portal에서 **새로 만들기** > **저장소** > **저장소 계정**을 차례로 클릭합니다.
1. 저장소 계정에 필요한 정보를 입력합니다.

   ![Azure Portal에서 저장소 계정 만들기](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **이름** - 저장소 계정의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **대시보드에 고정**: 대시보드에서 IoT Hub에 쉽게 액세스하려면 이 옵션을 선택합니다.
1. **만들기**를 클릭합니다.

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>메시지를 읽도록 IoT Hub 연결 준비

IoT Hub에서 기본 제공 Event Hub 호환 끝점을 노출하여 응용 프로그램이 IoT Hub 메시지를 읽을 수 있습니다. 한편 응용 프로그램은 소비자 그룹을 사용하여 IoT Hub에서 데이터를 읽습니다. IoT Hub에서 데이터를 읽는 Azure 함수 앱을 만들기 전에 다음을 수행해야 합니다.

- IoT Hub 끝점의 연결 문자열 가져오기
- IoT Hub에 대한 소비자 그룹 만들기

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>IoT Hub 끝점의 연결 문자열 가져오기

1. IoT Hub를 엽니다.
1. **IoT Hub** 창에서 **메시지** 아래에 있는 **끝점**을 클릭합니다.
1. 오른쪽 창에서 **기본 제공 끝점** 아래에 있는 **이벤트**를 클릭합니다.
1. **속성** 창에서 다음 값을 기록해 둡니다.
   - Event Hub 호환 끝점
   - Event Hub 호환 이름

   ![Azure Portal에서 IoT Hub 끝점의 연결 문자열 가져오기](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. **IoT Hub** 창에서 **설정** 아래에 있는 **공유 액세스 정책**을 클릭합니다.
1. **iothubowner**를 클릭합니다.
1. **기본 키** 값을 기록해 둡니다.
1. IoT Hub 끝점의 연결 문자열을 다음과 같이 구성합니다.

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > `<Event Hub-compatible endpoint>` 및 `<Primary key>`를 기록한 값으로 바꿉니다.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>IoT Hub에 대한 소비자 그룹 만들기

1. IoT Hub를 엽니다.
1. **IoT Hub** 창에서 **메시지** 아래에 있는 **끝점**을 클릭합니다.
1. 오른쪽 창에서 **기본 제공 끝점** 아래에 있는 **이벤트**를 클릭합니다.
1. **속성** 창의 **소비자 그룹** 아래에서 이름을 입력하고 기록해 둡니다.
1. **Save**를 클릭합니다.

## <a name="create-and-deploy-an-azure-function-app"></a>Azure 함수 앱 만들기 및 배포

1. [Azure Portal](https://portal.azure.com/)에서 **새로 만들기** > **계산** > **함수 앱**을 차례로 클릭합니다.
1. 함수 앱에 필요한 정보를 입력합니다.

   ![Azure Portal에서 함수 앱 만들기](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **앱 이름**: 함수 앱의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **저장소 계정**: 만든 저장소 계정입니다.

   **대시보드에 고정**: 대시보드에서 함수 앱에 쉽게 액세스하려면 이 옵션을 선택합니다.
1. **만들기**를 클릭합니다.
1. 만든 함수 앱을 엽니다.
1. 함수 앱에서 새 함수를 만듭니다.
   1. **새 함수**를 클릭합니다.
   1. **언어**에서 **JavaScript**를 선택하고, **시나리오**에서 **데이터 처리**를 선택합니다.
   1. **EventHubTrigger-JavaScript** 템플릿을 클릭합니다.
   1. 템플릿에 필요한 정보를 입력합니다.

      **함수 이름 지정**: 함수 이름입니다.

      **Event Hub 이름**: 기록한 Event Hub 호환 이름입니다.

      **Event Hub 연결**: [새로 만들기]를 클릭하여 구성한 IoT Hub 끝점의 연결 문자열을 추가합니다.
   1. **만들기**를 클릭합니다.
1. 함수의 출력을 구성합니다.
   1. **통합** > **새 출력** > **Azure Table Storage** > **선택**을 차례로 클릭합니다.

      ![Azure Portal에서 함수 앱에 테이블 저장소 추가](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. 필요한 정보를 입력합니다.

      **테이블 이름**: 이름으로 `deviceData`를 사용합니다.

      **저장소 계정 연결**: **새로 만들기**를 클릭하고 저장소 계정을 선택합니다.
   1. **Save**를 클릭합니다.
1. **트리거** 아래에서 **Azure Event Hub(myEventHubTrigger)**를 클릭합니다.
1. **Event Hub 소비자 그룹** 아래에서 만든 소비자 그룹의 이름을 입력한 다음 **저장**을 클릭합니다.
1. **개발**을 클릭한 다음 **파일 보기**를 클릭합니다.
1. **추가**를 클릭하여 `package.json`이라는 새 파일을 추가하고, 다음 정보를 붙여넣고, **저장**을 클릭합니다.

   ```json
   {
      "name": "iothub_save_message_to_table",
      "version": "0.0.1",
      "private": true,
      "main": "index.js",
      "author": "Microsoft Corp.",
      "dependencies": {
         "azure-iothub": "1.0.9",
         "azure-iot-common": "1.0.7",
         "moment": "2.14.1"
      }
   }
   ```
1. `index.js`의 코드를 다음으로 바꾼 다음 **저장**을 클릭합니다.

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
   var moment = require('moment');

   module.exports = function (context, iotHubMessage) {
      context.log('Message received: ' + JSON.stringify(iotHubMessage));
      context.bindings.outputTable = {
      "partitionKey": moment.utc().format('YYYYMMDD'),
         "rowKey": moment.utc().format('hhmmss') + process.hrtime()[1] + '',
         "message": JSON.stringify(iotHubMessage)
      };
      context.done();
   };
   ```
1. **함수 앱 설정** > **개발자 콘솔 열기**를 차례로 클릭합니다.

   함수 앱의 `wwwroot` 폴더에 있어야 합니다.
1. 다음 명령을 실행하여 함수 폴더로 이동합니다.

   ```bash
   cd <your function name>
   ```
1. 다음 명령을 실행하여 npm 패키지를 설치합니다.

   ```bash
   npm install
   ```

   > [!Note]
   > 설치를 완료하는 데 약간의 시간이 걸릴 수 있습니다.

지금까지 함수 앱을 만들었습니다. IoT Hub에서 받는 메시지를 Azure 테이블 저장소에 저장합니다.

> [!Note]
> **실행** 단추를 사용하여 함수 앱을 테스트할 수 있습니다. **실행**을 클릭하면 테스트 메시지를 IoT Hub로 보냅니다. 메시지가 도착하면 함수 앱을 트리거하여 시작하고 Azure 테이블 저장소에 메시지를 저장해야 합니다. **로그** 창에서 프로세스의 세부 정보를 기록합니다.

## <a name="verify-your-message-in-your-table-storage"></a>테이블 저장소에 있는 메시지 확인

1. 장치에서 샘플 응용 프로그램을 실행하여 IoT Hub로 메시지를 보냅니다.
1. [Microsoft Azure Storage 탐색기를 다운로드하고 설치합니다](http://storageexplorer.com/).
1. Microsoft Azure Storage 탐색기를 열고 **Azure 계정 추가** > **로그인**을 차례로 클릭한 다음 Azure 계정에 로그인합니다.
1. Azure 구독 > **저장소 계정** > 저장소 계정 > **테이블** > **deviceData**를 차례로 클릭합니다.

   장치에서 IoT Hub로 보낸 메시지가 `deviceData` 테이블에 기록되어 있어야 합니다.

## <a name="next-steps"></a>다음 단계

Azure 저장소 계정과 Azure 함수 앱을 만들어 IoT Hub에서 받는 메시지를 Azure 테이블 저장소에 저장했습니다.

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](iot-hub-explorer-cloud-device-messaging.md)
