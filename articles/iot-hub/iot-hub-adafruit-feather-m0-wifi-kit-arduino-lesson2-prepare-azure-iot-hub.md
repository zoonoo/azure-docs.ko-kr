---
title: "Azure IoT에 Raspberry Pi(C) 연결 - 단원 2: 장치 등록 | Microsoft Docs"
description: "리소스 그룹과 Azure IoT Hub를 만들고, Azure CLI를 사용하여 Azure IoT Hub에 Adafruit Feather M0 WiFi를 등록합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "클라우드에 arduino 연결, azure iot hub, 사물 인터넷 클라우드, azure iot hub 장치 만들기, arduino 클라우드"
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e81ff09443b8d4930f20d0bbeb100c4a90f92a7f


---
# <a name="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board"></a>IoT Hub 만들기 및 Adafruit Feather M0 WiFi Arduino 보드 등록

## <a name="what-you-will-do"></a>수행할 사항
* 리소스 그룹을 만듭니다.
* 리소스 그룹에 Azure IoT hub를 만듭니다.
* Azure CLI(Azure 명령줄 인터페이스)를 사용하여 Arduino 보드를 Azure IoT Hub에 추가합니다.

Azure CLI를 사용하여 Arduino 보드를 IoT Hub에 추가하는 경우 서비스는 Arduino 보드에 대한 키를 생성하여 서비스로 인증합니다. 문제가 있으면 [문제 해결 페이지][troubleshoot]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.
* Azure CLI를 사용하여 IoT Hub를 만드는 방법
* IoT Hub에서 Arduino 보드에 대한 장치 ID를 만드는 방법

## <a name="what-you-need"></a>필요한 항목
* Azure 계정.
* Azure CLI가 설치된 컴퓨터

## <a name="create-your-iot-hub"></a>IoT Hub 만들기
Azure IoT Hub를 사용하면 수백만 개의 IoT 자산을 연결, 모니터링 및 관리할 수 있습니다. IoT Hub를 만들려면 다음 단계를 따르세요.

1. 다음 명령을 실행하여 Azure 계정에 로그인합니다.

   ```bash
   az login
   ```

   로그인이 완료되면 사용 가능한 구독이 모두 나열됩니다.

2. 다음 명령을 실행하여 사용할 기본 구독을 설정합니다.

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name`은 `az login` 또는 `az account list` 명령의 출력에서 찾을 수 있습니다.

3. 다음 명령을 실행하여 공급자를 등록합니다. 리소스 공급자는 응용 프로그램에 대한 리소스를 제공하는 서비스입니다. 공급자를 등록해야만 공급자가 제공하는 Azure 리소스를 배포할 수 있습니다.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. 다음 명령을 실행하여 미국 서부 하위 지역에 이름이 iot-sample인 리소스 그룹을 만듭니다.

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus`는 리소스 그룹을 만드는 위치입니다. 다른 위치를 사용하려는 경우 `az account list-locations -o table`을 실행하면 Azure가 지원하는 모든 위치를 볼 수 있습니다.

5. 다음 명령을 실행하여 iot-sample 리소스 그룹에서 IoT hub를 만듭니다.

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

기본적으로 도구는 IoT Hub를 무료 가격 책정 계층에 만듭니다. 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

> [!NOTE]
> IoT hub의 이름은 전역적으로 고유해야 합니다.
> Azure IoT Hub의 F1 버전은 사용자의 Azure 구독에서 하나만 만들 수 있습니다.

## <a name="register-your-arduino-board-in-your-iot-hub"></a>IoT Hub에 Arduino 보드 등록
IoT Hub와 메시지를 주고 받는 각 장치는 고유한 ID로 등록되어야 합니다.

다음 명령을 실행하여 IoT Hub에 Arduino 보드를 등록합니다.

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

## <a name="summary"></a>요약
IoT Hub를 만들고 IoT Hub에 장치 ID로 Arduino 보드를 등록했습니다. Arduino 보드에서 IoT Hub로 메시지를 보내는 방법을 알아볼 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub 메시지를 처리하고 저장하기 위해 Azure 함수 앱 및 Azure Storage 계정을 만듭니다][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md


<!--HONumber=Jan17_HO4-->


