---
title: "Azure IoT에 Raspberry Pi(C) 연결 - 단원 3: 템플릿 배포 | Microsoft Docs"
description: "Azure 함수 앱은 Azure IoT Hub 이벤트를 수신 대기하고, 들어오는 메시지를 처리하고, 이를 Azure Table Storage에 씁니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "클라우드에 데이터 저장, 클라우드에 저장된 데이터, IoT 클라우드 서비스"
ms.assetid: 4bcfb071-b3ae-48cc-8ea5-7e7434732287
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 402a5dac4e77f64576acb968b67df92167f54f27
ms.lasthandoff: 01/24/2017


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure 함수 앱 및 Azure Storage 계정 만들기
[Azure Functions](../../articles/azure-functions/functions-overview.md)는 클라우드에서 *함수*(작은 코드)를 손쉽게 실행하기 위한 솔루션입니다. Azure 함수 앱은 Azure에서 함수 실행을 호스트합니다.

## <a name="what-will-you-do"></a>수행할 내용
Azure Resource Manager 템플릿을 사용하여 Azure 함수 앱 및 Azure Storage 계정을 만듭니다. Azure 함수 앱은 Azure IoT Hub 이벤트를 수신 대기하고, 들어오는 메시지를 처리하고, 이를 Azure Table Storage에 씁니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-will-you-learn"></a>학습할 내용
이 문서에서는 다음에 대해 알아봅니다.
* [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md)를 사용하여 Azure 리소스를 배포하는 방법.
* Azure 함수 앱을 사용하여 IoT hub 메시지를 처리하고 이를 Azure Table Storage의 테이블에 쓰는 방법.

## <a name="what-do-you-need"></a>필요한 내용
* 다음을 완료해야 합니다.
- [Raspberry Pi 3 시작](iot-hub-raspberry-pi-kit-c-get-started.md)
- [Azure IoT Hub 만들기](iot-hub-raspberry-pi-kit-c-get-started.md)

## <a name="open-the-sample-app"></a>샘플 앱 열기
다음 명령을 실행하여 Visual Studio Code에서 샘플 프로젝트를 엽니다.

```bash
cd Lesson3
code .
```

![Repo 구조](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure_c.png)

* `app` 하위 폴더에서 `main.c` 파일은 키 소스 파일입니다. 이 소스 파일에는 메시지를 IoT hub로 20번 보내서 각 메시지에 대한 LED를 깜빡이는 코드가 포함되어 있습니다.
* `arm-template.json` 파일은 Azure 함수 앱 및 Azure Storage 계정을 포함하는 Azure Resource Manager 템플릿입니다.
* `arm-template-param.json` 파일은 Azure Resource Manager 템플릿에서 사용되는 구성 파일입니다.
* `ReceiveDeviceMessages` 하위 폴더에는 Azure 함수에 대한 Node.js 코드가 포함되어 있습니다.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Azure Resource Manager 템플릿 구성 및 Azure에서 리소스 만들기
Visual Studio Code에서 `arm-template-param.json` 파일을 업데이트합니다.

![Azure Resource Manager 템플릿 매개 변수](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para_c.png)

* **[your IoT Hub name]**을 [IoT Hub를 만들고 Raspberry Pi 3을 등록](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)할 때 지정한 **{my hub name}**으로 바꿉니다.
* **[새 리소스에 대한 문자열 접두사]**를 원하는 접두사로 대체합니다. 접두사는 리소스 이름이 전역적으로 고유하여 충돌을 피할 수 있게 합니다. 접두사에 대시 또는 숫자 이니셜을 사용하지 마십시오.

`arm-template-param.json` 파일을 업데이트한 후 다음 명령을 실행하여 Azure에 리소스를 배포합니다.

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

이러한 리소스를 만드는 데 약&5;분이 걸립니다. 리소스 만들기가 진행되는 동안, 다음 문서로 이동할 수 있습니다.

## <a name="summary"></a>요약
IoT Hub 메시지를 처리하는 Azure 함수 앱과 이러한 메시지를 저장하는 Azure Storage 계정을 만들었습니다. 이제 샘플을 배포 및 실행하고 장치-클라우드 메시지를 Pi에 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계
[샘플 응용 프로그램을 실행하여 장치-클라우드 메시지 보내기](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md)


