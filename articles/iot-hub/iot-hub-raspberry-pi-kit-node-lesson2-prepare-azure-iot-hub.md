---
featureFlags:
- usabilla
title: "Azure IoT에 Raspberry Pi(노드) 연결 - 단원 2: 장치 등록 | Microsoft Docs"
description: "리소스 그룹을 만들고, Azure IoT Hub를 만들고, Azure CLI를 사용하여 IoT Hub ID 레지스트리에 Pi를 등록합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Raspberry Pi 클라우드, Pi 클라우드 연결"
ms.assetid: 736215b6-e7e4-46f9-af30-0ded9ffa5204
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: add684549056a824e5534d071a5d0215e27f7a0e
ms.lasthandoff: 01/24/2017


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>IoT Hub 만들기 및 Raspberry Pi 3 등록
## <a name="what-you-will-do"></a>수행할 사항
* 리소스 그룹을 만듭니다.
* 리소스 그룹에 Azure IoT hub를 만듭니다.
* Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Raspberry Pi 3을 the Azure IoT Hub에 추가합니다.

Azure CLI를 사용하여 Pi를 IoT Hub에 추가하는 경우 서비스는 Pi에 대한 키를 생성하여 서비스로 인증합니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
이 문서에서는 다음에 대해 알아봅니다.
* Azure CLI를 사용하여 IoT Hub를 만드는 방법
* IoT Hub에서 Pi에 대한 장치 ID를 만드는 방법

## <a name="what-you-need"></a>필요한 항목
* Azure 계정.
* Azure CLI가 설치된 Mac 또는 Windows 컴퓨터

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
> IoT hub의 이름은 전역적으로 고유해야 합니다. Azure IoT Hub의 F1 버전은 사용자의 Azure 구독에서 하나만 만들 수 있습니다.

## <a name="register-pi-in-your-iot-hub"></a>IoT Hub에 Pi 등록
IoT Hub와 메시지를 주고 받는 각 장치는 고유한 ID로 등록되어야 합니다. Azure CLI를 사용하여 Pi를 등록하고 장치 인증을 위해 자체 서명된 X.509 인증서를 만듭니다.

다음 명령을 실행합니다.

```bash
# For Windows command prompt
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir %USERPROFILE%\.iot-hub-getting-started
 
# For macOS or Ubuntu
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir ~/.iot-hub-getting-started
```

## <a name="summary"></a>요약
IoT Hub를 만들고 IoT Hub에 장치 ID로 Pi를 등록했습니다. Pi에서 IoT Hub로 메시지를 보내는 방법을 알아볼 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[IoT Hub 메시지를 처리하고 저장하기 위해 Azure 함수 앱 및 Azure Storage 계정 만들기](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)


