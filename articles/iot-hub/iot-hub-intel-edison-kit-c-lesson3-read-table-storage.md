---
title: "Azure IoT에 Intel Edison(C) 연결 - 단원 3: 메시지 모니터링 | Microsoft Docs"
description: "장치-클라우드 메시지가 Azure Table Storage에 기록될 때 해당 메시지를 모니터링합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "클라우드의 데이터, 클라우드 데이터 컬렉션, IoT 클라우드 서비스, IoT 데이터"
ms.assetid: cad545c3-dd88-486c-a663-d587a924ccd4
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 1be4de1fef11da43c2940fdd890027d0e9728515


---
# <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage에 유지되는 메시지 읽기
## <a name="what-you-will-do"></a>수행할 사항
Azure Table Storage에 메시지가 기록될 때 Intel Edison에서 IoT Hub로 전송되는 장치-클라우드 메시지를 모니터링합니다. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 Gulp 메시지 읽기 작업을 사용하여 Azure Table Storage에 유지되는 메시지를 읽는 방법을 알아봅니다.

## <a name="what-you-need"></a>필요한 항목
이 과정을 시작하기 전에 [Intel Edison에서 Azure 깜빡이는 샘플 응용 프로그램 실행][run-the-azure-blink-sample-application-on-intel-edison]을 완료해야 합니다.

## <a name="read-new-messages-from-your-storage-account"></a>저장소 계정에서 새 메시지 읽어오기
이전 문서에서는 Edison에서 샘플 응용 프로그램을 실행했습니다. 샘플 응용 프로그램은 Azure IoT Hub에 메시지를 보냈습니다. IoT Hub에 보낸 메시지는 Azure 함수 앱을 경유하여 Azure Table Storage에 저장됩니다. Azure Table Storage에서 메시지를 읽어오려면 Azure Storage 연결 문자열이 필요합니다.

Azure Table Storage에 저장된 메시지를 읽으려면 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 연결 문자열을 가져옵니다.

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   첫 번째 명령은 두 번째 명령에 사용되어 연결 문자열을 가져오는 `storage name`을 가져옵니다. 값을 변경하지 않았다면 `iot-sample`을 `{resource group name}` 값으로 사용합니다.
2. 다음 명령을 실행하여 Visual Studio Code에서 구성 파일인 `config-edison.json` 파일을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. `[Azure storage connection string]`을 1단계에서 가져온 연결 문자열로 바꿉니다.
4. `config-edison.json` 파일을 저장합니다.
5. 다음 명령을 실행하여 메시지를 다시 보내고 Azure Table Storage에서 해당 메시지를 읽어옵니다.

   ```bash
   gulp run --read-storage
   ```

   Azure Table Storage에서 읽어오는 논리는 `azure-table.js` 파일에 있습니다.

   ![gulp run --read-storage][gulp run]

## <a name="summary"></a>요약
Edison을 클라우드의 IoT Hub에 성공적으로 연결했고 깜빡이는 샘플 응용 프로그램을 사용하여 장치-클라우드 메시지를 보냈습니다. 또한, Azure 함수 앱을 사용하여 들어오는 IoT Hub 메시지를 Azure Table Storage에 저장했습니다. 이제 IoT Hub에서 Edison으로 클라우드-장치 메시지를 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계
[샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message_c.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md


<!--HONumber=Jan17_HO4-->


