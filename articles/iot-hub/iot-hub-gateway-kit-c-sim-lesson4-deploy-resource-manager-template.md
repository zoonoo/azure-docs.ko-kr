---
title: "Azure 클라우드에 메시지 저장 및 클라우드에서 메시지 읽기 | Microsoft Docs"
description: "Intel NUC의 메시지를 IoT Hub에 저장하고 Azure Table Storage에 기록한 다음 클라우드에서 읽습니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "클라우드에 데이터 저장, 클라우드에 저장된 데이터, IoT 클라우드 서비스"
ms.assetid: ffed0c2e-b092-40e1-9113-8196ec057d67
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: ac4204d6d253e59a367f110aaa3bd28fd94792ea


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Azure 함수 앱 및 저장소 계정 만들기

Azure Functions는 클라우드에서 _함수_(작은 코드)를 손쉽게 실행하기 위한 솔루션입니다. Azure 함수 앱은 Azure에서 함수 실행을 호스트합니다. 

## <a name="what-you-will-do"></a>수행할 사항

- Azure Resource Manager 템플릿을 사용하여 Azure 함수 앱 및 Azure Storage 계정을 만듭니다. Azure 함수 앱은 Azure IoT Hub 이벤트를 수신 대기하고, 들어오는 메시지를 처리하고, 이를 Azure Table Storage에 씁니다.

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-sim-troubleshooting.md)에서 솔루션을 검색하세요.


## <a name="what-you-will-learn"></a>알아볼 내용

이 단원에서는 다음 내용을 배웁니다.

- Azure Resource Manager를 사용하여 Azure 리소스를 배포하는 방법
- Azure 함수 앱을 사용하여 IoT Hub 메시지를 처리하고 이를 Azure Table Storage의 테이블에 쓰는 방법

## <a name="what-you-need"></a>필요한 항목

다음과 같은 이전 단원을 완료했어야 합니다.

- [1단원: Intel NUC를 IoT Gateway로 설정](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)
- [2단원: 호스트 컴퓨터 및 Azure IoT Hub 준비](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
- [3단원: 시뮬레이트된 장치에서 메시지 수신 및 IoT Hub에서 메시지 읽기](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

## <a name="open-a-sample-app"></a>샘플 앱 열기

`iot-hub-c-intel-nuc-gateway-getting-started` repo 폴더로 이동하여 구성 파일을 초기화한 후 다음 명령을 실행하여 Visual Studio Code에서 샘플 프로젝트를 엽니다.

```bash
cd Lesson4
npm install
gulp init
code .
```

![Repo 구조](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- `arm-template.json` 파일은 Azure 함수 앱 및 Azure Storage 계정을 포함하는 Azure Resource Manager 템플릿입니다.
- `arm-template-param.json` 파일은 Azure Resource Manager 템플릿에서 사용되는 구성 파일입니다.
- `ReceiveDeviceMessages` 하위 폴더에는 Azure 함수에 대한 Node.js 코드가 포함되어 있습니다.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Azure Resource Manager 템플릿 구성 및 Azure에서 리소스 만들기

Visual Studio Code에서 `arm-template-param.json` 파일을 업데이트합니다.

![ARM 템플릿 JSON](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- `[your IoT Hub name]`을 단원 2에서 지정했던 `{my hub name}`으로 바꿉니다.

`arm-template-param.json` 파일을 업데이트한 후 다음 명령을 실행하여 Azure에 리소스를 배포합니다.

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

단원 2에서 값을 변경하지 않았다면 `iot-gateway`을 `{resource group name}` 값으로 사용합니다.

## <a name="summary"></a>요약

IoT Hub 메시지를 처리하는 Azure 함수 앱과 이러한 메시지를 저장하는 Azure Storage 계정을 만들었습니다. 이제 게이트웨이에서 IoT Hub로 보낸 메시지를 읽을 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Storage에 유지되는 메시지 읽기](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md)



<!--HONumber=Dec16_HO3-->


