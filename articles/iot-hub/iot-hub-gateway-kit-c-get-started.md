---
title: "SensorTag 장치 및 Azure IoT 게이트웨이 - 시작 | Microsoft Docs"
description: "IoT Gateway 시작 키트를 시작하여 Azure IoT Hub를 만들고 SensorTag 및 게이트웨이를 IoT Hub에 연결합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, iot 게이트웨이, 사물 인터넷 시작, iot 도구 키트"
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: dd14d137c23fbccb3616b60db8a63e668ae7ea80
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-sensortag"></a>SensorTag를 사용하여 IoT Gateway 시작 키트 시작

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [시뮬레이션된 장치](iot-hub-gateway-kit-c-sim-get-started.md)

이 자습서에서는 [IoT Gateway 시작 키트](https://aka.ms/gateway-kit)를 실행하는 작업의 기초부터 학습합니다. Wind River Linux 및 [TI SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main)를 실행 중인 Intel NUC로 작업합니다. Azure IoT Hub를 사용하여 장치를 클라우드에 원활하게 연결하는 방법을 알아봅니다.

***
**아직 키트가 없나요?:** [여기](https://aka.ms/gateway-kit)를 클릭합니다. **SensorTag가 없나요?:** [시뮬레이트된 장치로 시작하거나](iot-hub-gateway-kit-c-sim-get-started.md) [SensorTag를 구입합니다.](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag)
***

## <a name="lesson-1-configure-your-nuc"></a>단원 1: NUC 구성
![단원&1; 종단간 다이어그램](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

이 단원에서는 키트에 Intel NUC(Next Unit of Computing)를 Azure IoT Gateway로 설정하고 NUC에 Azure IoT Gateway SDK 패키지를 설치한 다음 샘플 앱을 실행하여 게이트웨이 기능을 확인합니다.

*예상 완료 시간: 15분*

[Intel NUC를 IoT Gateway로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)으로 이동

## <a name="lesson-2-create-your-iot-hub"></a>단원 2: IoT Hub 만들기
![단원&2; 종단간 다이어그램](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

이 단원에서는 호스트 컴퓨터에 도구 및 소프트웨어를 설치합니다. 그런 다음 무료 Azure 계정을 만들고, Azure IoT Hub를 프로비전하며, IoT Hub에서 첫 번째 장치를 만듭니다.

이 단원을 시작하기 전에 1단원을 완료합니다.

### <a name="get-the-tools"></a>도구 얻기
호스트 컴퓨터에 도구 및 소프트웨어를 설치합니다.

*예상 완료 시간: 20분* 

[도구 얻기](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)로 이동

### <a name="create-an-iot-hub-and-register-your-device"></a>IoT Hub 만들기 및 장치 등록
리소스 그룹을 만들고, 첫 번째 Azure IoT Hub를 프로비전하며, Azure CLI를 사용하여 첫 번째 장치를 IoT Hub에 추가합니다.

*예상 완료 시간: 10분*  

[IoT Hub 만들기 및 장치 등록](iot-hub-gateway-kit-c-lesson2-register-device.md)으로 이동

## <a name="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub"></a>3단원: SensorTag에서 메시지 수신 및 IoT Hub에서 메시지 읽기
이 단원에서는 스크립트를 사용하여 게이트웨이에서 BLE 샘플 응용 프로그램의 구성 및 실행을 자동화합니다. 이러한 응용 프로그램은 모듈 컬렉션을 사용하여 데이터 집계 및 변환, 명령 처리 또는 여러 가지 관련 작업을 수행합니다. 모듈은 메시지 브로커를 통해 서로 통신합니다. 샘플 응용 프로그램에는 BLE 모듈과 IoT Hub 모듈이 있습니다. BLE 모듈은 BLE SensorTag에서 데이터를 수신합니다. IoT Hub 모듈은 받은 데이터를 패키지하고 Azure IoT Gateway SDK에서 제공된 게이트웨이 프레임워크를 통해 IoT Hub로 보냅니다.

![단원 3 종단간 다이어그램](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

### <a name="configure-and-run-the-ble-sample-app"></a>BLE 샘플 앱 구성 및 실행
SensorTag와 게이트웨이 간의 연결을 설정합니다. 그런 다음 구성을 완료하고 BLE 샘플 응용 프로그램을 실행합니다.

*예상 완료 시간: 15분*

[BLE 샘플 앱 구성 및 실행](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)으로 이동

### <a name="read-messages-from-your-iot-hub"></a>IoT Hub에서 메시지 읽기
IoT Hub에서 메시지를 읽으려면 호스트 컴퓨터에서 샘플 코드를 실행합니다.

*예상 완료 시간: 15분*

[IoT Hub에서 메시지 읽기](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)로 이동

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>4단원: Azure Table Storage로 메시지 저장
IoT Hub에서 들어오는 메시지를 가져와서 Azure Table Storage에 쓰는 Azure 함수 앱을 만듭니다.

![단원 4 종단간 다이어그램](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure 함수 앱 및 Azure Storage 계정 만들기
Azure Resource Manager 템플릿을 사용하여 Azure 함수 앱 및 Azure Storage 계정을 만듭니다.

*예상 완료 시간: 10분*  

[Azure 함수 앱 및 Azure Storage 계정 만들기](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)로 이동

### <a name="read-messages-persisted-in-azure-table-storage"></a>Azure Table Storage에 유지되는 메시지 읽기
게이트웨이-클라우드 메시지가 Azure Table Storage에 기록될 때 해당 메시지를 모니터링합니다.

*예상 완료 시간: 5분*  

[Azure Table Storage에 유지되는 메시지 읽기](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)로 이동

## <a name="troubleshooting"></a>문제 해결
단원을 진행하는 동안 문제가 발생하면 [문제 해결](iot-hub-gateway-kit-c-troubleshooting.md) 문서에서 솔루션을 찾습니다.

## <a name="explore-more"></a>자세히 살펴보기
자세한 내용은 [Intel IoT Gateway Kit 개발자 영역](http://software.intel.com/iot/microsoft-azure)을 참조하세요.
