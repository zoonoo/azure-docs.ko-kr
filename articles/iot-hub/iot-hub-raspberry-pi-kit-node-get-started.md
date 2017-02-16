---
title: "Raspberry Pi 3을 Azure IoT Hub에 연결 | Microsoft Docs"
description: "Raspberry Pi 3 장치로 시작하고, Azure IoT hub를 만들고 Pi를 IoT hub에 연결합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Azure IoT Hub, 사물 인터넷 시작, IoT 도구 키트"
experimental: true
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d04f9d219451d683b572df43f73944011d686797


---
# <a name="get-started-with-raspberry-pi-3-nodejs"></a>Raspberry Pi 3 시작(Node.js)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

이 자습서에서는 Raspbian을 실행하는 Raspberry Pi 3 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](iot-hub-what-is-iot-hub.md)를 사용하여 장치를 클라우드에 원활하게 연결하는 방법을 알아봅니다. Windows 10 IoT Core 샘플이 필요하면 [Windows 개발자 센터](http://www.windowsondevices.com/)로 이동하세요.

아직 키트가 없으세요? [여기](https://azure.microsoft.com/develop/iot/starter-kits)에서 시작합니다.

## <a name="lesson-1-configure-your-device"></a>단원 1: 장치 구성
![단원 1 종단간 다이어그램](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

이 단원에서는 운영 체제를 통해 Raspberry Pi 3 장치를 구성하고, 개발 환경을 설정하며, 응용 프로그램을 Pi에 배포합니다.

### <a name="configure-your-device"></a>장치 구성
최초 사용을 위해 Raspberry Pi 3을 구성하고 Raspbian을 설치합니다. Raspbian은 Raspberry Pi 하드웨어에 최적화된 무료 운영 체제입니다.

*예상 완료 시간: 30분*

[장치 구성](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)으로 이동.

### <a name="get-the-tools"></a>도구 얻기
Raspberry Pi 3의 첫 번째 응용 프로그램을 빌드 및 배포하기 위한 도구 및 소프트웨어를 다운로드합니다.

*예상 완료 시간: 20분* 

[도구 얻기](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)로 이동.

### <a name="create-and-deploy-the-blink-application"></a>깜박임 응용 프로그램 만들기 및 배포
Github에서 샘플 Node.js 깜박임 응용 프로그램을 복제하고, gulp를 사용하여 이 응용 프로그램을 Raspberry Pi 3 보드에 배포합니다. 이 샘플 응용 프로그램은 보드에 연결된 LED를 2초마다 깜박이게 합니다.

*예상 완료 시간: 5분*  

[깜박임 응용 프로그램 만들기 및 배포](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)로 이동.

## <a name="lesson-2-create-your-iot-hub"></a>단원 2: IoT hub 만들기
![단원 2 종단간 다이어그램](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

이 단원에서는 무료 Azure 계정을 만들고, Azure IoT Hub를 프로비전하며, IoT Hub에서 첫 번째 장치를 만듭니다.

이 단원을 시작하기 전에 1단원을 완료합니다.

### <a name="get-the-azure-tools"></a>Azure 도구 얻기
Azure 명령줄 인터페이스(Azure CLI) 설치.

*예상 완료 시간: 10분*  

[Azure 도구 얻기](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)로 이동.

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>IoT Hub 만들기 및 Raspberry Pi 3 등록
리소스 그룹을 만들고, 첫 번째 Azure IoT Hub를 프로비전하며, Azure CLI를 사용하여 첫 번째 장치를 IoT Hub에 추가합니다.

*예상 완료 시간: 10분*  

[IoT Hub 만들기 및 Raspberry Pi 3 등록](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)으로 이동.

## <a name="lesson-3-send-device-to-cloud-messages"></a>단원 3: 장치-클라우드 메시지 보내기
![단원 3 종단간 다이어그램](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

이 단원에서는 Pi에서 IoT Hub로 메시지를 보냅니다. 또한 IoT Hub에서 들어오는 메시지를 가져와서 Azure Table Storage에 쓰는 Azure 함수 앱을 만듭니다.

이 단원을 시작하기 전에 단원 1과 단원 2를 완료합니다.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure 함수 앱 및 Azure Storage 계정 만들기
Azure Resource Manager 템플릿을 사용하여 Azure 함수 앱 및 Azure Storage 계정을 만듭니다.

*예상 완료 시간: 10분*  

[Azure 함수 앱 및 Azure Storage 계정 만들기](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)로 이동.

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>샘플 응용 프로그램을 실행하여 장치-클라우드 메시지 보내기
IoT Hub에 메시지를 보내는 샘플 응용 프로그램을 Raspberry Pi 3에 배포 및 실행합니다.

*예상 완료 시간: 10분*  

[샘플 응용 프로그램을 실행하여 장치-클라우드 메시지 보내기](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)로 이동.

### <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage에 유지되는 메시지 읽기
장치-클라우드 메시지가 Azure Storage에 기록될 때 해당 메시지를 모니터링합니다.

*예상 완료 시간: 5분*  

[Azure Storage에 유지되는 메시지 읽기](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)로 이동.

## <a name="lesson-4-send-cloud-to-device-messages"></a>단원 4: 클라우드-장치 메시지 보내기
![단원 4 종단간 다이어그램](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

이 단원은 Azure IoT Hub에서 Raspberry Pi 3으로 메시지를 보내는 방법을 보여줍니다. 이 메시지는 Pi에 연결된 LED의 켜기 및 끄기 동작을 제어합니다. 이 작업을 수행하기 위한 샘플 응용 프로그램이 준비되었습니다.

이 단원을 시작하기 전에 단원 1, 단원 2, 단원 3을 완료합니다.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기
단원 4의 샘플 응용 프로그램은 Pi에서 실행되며 IoT Hub에서 들어오는 메시지를 모니터링합니다. 새로운 gulp 작업은 IoT Hub에서 Pi로 메시지를 보내고 LED를 깜빡입니다.

*예상 완료 시간: 10분*  

[샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)로 이동.

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>선택적인 섹션: LED 켜기 및 끄기 동작 변경
LED의 켜기 및 끄기 동작을 변경하도록 메시지를 사용자 지정합니다.

*예상 완료 시간: 10분*  

[선택적인 섹션: LED 켜기 및 끄기 동작 변경](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)으로 이동.

## <a name="troubleshooting"></a>문제 해결
단원을 진행하는 동안 문제가 있으면 [문제 해결](iot-hub-raspberry-pi-kit-node-troubleshooting.md) 문서를 참조하세요.




<!--HONumber=Dec16_HO2-->


