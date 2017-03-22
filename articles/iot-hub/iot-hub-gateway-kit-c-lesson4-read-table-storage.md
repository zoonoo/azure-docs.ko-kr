---
title: "SensorTag 장치 및 Azure IoT 게이트웨이 - 단원 4: 테이블 저장소 | Microsoft Docs"
description: "Intel NUC의 메시지를 IoT Hub에 저장하고 Azure Table Storage에 기록한 다음 클라우드에서 읽습니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "클라우드에서 데이터 검색, IoT 클라우드 서비스"
ms.assetid: 8ca78045-ad92-4a6a-90f1-05f9668e6f0e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 293343e5db58bebc8c2e27d54d6396212fe6d3a1
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>Azure Table Storage에 유지되는 메시지 읽기

## <a name="what-you-will-do"></a>수행할 사항

- IoT Hub에 메시지를 보내는 게이트웨이에서 게이트웨이 샘플 응용 프로그램을 실행합니다.
- 그런 다음 호스트 컴퓨터에서 샘플 코드를 실행하여 Azure Table Storage의 메시지를 읽습니다. 

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

Gulp 도구로 샘플 코드를 실행하여 Azure Table Storage의 메시지를 읽는 방법

## <a name="what-you-need"></a>필요한 항목

다음 작업을 성공적으로 완료했습니다.

- [Azure 함수 앱 및 Azure Storage 계정 만들기](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)
- [게이트웨이 샘플 응용 프로그램 실행](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)
- [IoT Hub에서 메시지 읽기](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)

## <a name="get-your-azure-storage-connection-strings"></a>Azure Storage 연결 문자열 가져오기

이 단원의 초반부에 Azure Storage 계정을 성공적으로 만들었습니다. Azure Storage 계정의 연결 문자열을 가져오려면 다음 명령을 실행합니다.

* 모든 저장소 계정을 나열합니다.

```bash
az storage account list -g iot-gateway --query [].name
```

* Azure Storage 연결 문자열을 가져옵니다.

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

단원 2에서 값을 변경하지 않았다면 `{resource group name}` 값으로 iot-gateway를 사용합니다.

## <a name="configure-the-device-connection"></a>장치 연결 구성

호스트 컴퓨터에서 실행되는 샘플 코드가 Azure Table Storage의 메시지를 읽을 수 있도록 `config-azure.json` 파일을 업데이트합니다. 장치 연결을 구성하려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 장치 구성 파일 `config-azure.json`을 엽니다.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![구성](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. `[Azure storage connection string]`을 가져온 Azure Storage 연결 문자열로 바꿉니다.

   `[IoT hub connection string]`은&3;단원의 [Azure IoT Hub에서 메시기 읽기](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md) 섹션에서 이미 바꿨어야 합니다.

## <a name="read-messages-in-your-azure-table-storage"></a>Azure Table Storage에서 메시지 읽기

게이트웨이 샘플 응용 프로그램을 실행하고 다음 명령으로 Azure Table Storage 메시지를 읽습니다.

```bash
gulp run --table-storage
```

새 메시지가 도착하면 IoT Hub가 Azure 함수 응용 프로그램을 트리거하여 Azure Table Storage에 메시지를 저장합니다.
`gulp run` 명령은 IoT Hub에 메시지를 보내는 게이트웨이 샘플 응용 프로그램을 실행합니다. `table-storage` 매개 변수를 사용하면 Azure Table Storage에 저장된 메시지를 수신할 하위 프로세스도 생성됩니다.

보내고 받는 메시지는 모두 호스트 시스템의 동일한 콘솔 창에 즉시 표시됩니다. 샘플 응용 프로그램 인스턴스는 40초 후 자동으로 종료됩니다.

   ![gulp 읽기](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table.png)


## <a name="summary"></a>요약

샘플 코드를 실행하여 Azure 함수 응용 프로그램에서 저장한 Azure Table Storage의 메시지를 읽었습니다.
