---
title: "SensorTag 장치 및 Azure IoT 게이트웨이 - 단원 2: 장치 등록 | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, 사물 인터넷 클라우드, azure iot hub 장치 만들기, ti sensortag, ti ble"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 2c18f5ae-e39a-48ae-a9fe-04bb595740a0
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a895f18b4cefc01f12613e701c71bd471bb369c3
ms.lasthandoff: 01/25/2017


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>Azure IoT Hub 만들기 및 장치 등록

## <a name="what-you-will-do"></a>수행할 사항

- 리소스 그룹 만들기
- 첫 번째 IoT Hub 만들기
- Azure CLI를 사용하여 IoT Hub에 장치를 등록합니다. 

IoT Hub에 장치를 등록하면 Azure IoT Hub 서비스가 서비스 인증에 사용할 장치에 대한 키를 생성합니다. 

문제가 있으면 [문제 해결 페이지](iot-hub-gateway-kit-c-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용

이 단원에서는 다음 내용을 배웁니다.

- Azure CLI를 사용하여 IoT Hub를 만드는 방법
- IoT Hub에 장치를 등록하는 방법

## <a name="what-you-need"></a>필요한 항목

- 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [무료 Azure 평가판 계정](http://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.
- Azure CLI이 설치되어 있어야 합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

IoT Hub를 만들려면 다음 단계를 따르세요.

1. 다음 명령을 실행하여 Azure 계정에 로그인합니다.

   ```bash
   az login
   ```

   로그인하면 사용 가능한 구독이 모두 나열됩니다.

2. 사용할 기본 Azure 구독을 다음 명령을 실행하여 설정합니다.

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name`은 `az login` 또는 `az account list` 명령의 출력에서 찾을 수 있습니다.

3. 다음 명령을 실행하여 공급자를 등록합니다. 리소스 공급자는 응용 프로그램에 대한 리소스를 제공하는 서비스입니다. 공급자를 등록해야만 공급자가 제공하는 Azure 리소스를 배포할 수 있습니다.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. 다음 명령을 실행하여 미국 서부 하위 지역에 이름이 `iot-gateway`인 리소스 그룹을 만듭니다.

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus`는 리소스 그룹을 만드는 위치입니다. 다른 위치를 사용하려는 경우 `az account list-locations -o table`을 실행하면 Azure가 지원하는 모든 위치를 볼 수 있습니다.

5. 다음 명령을 실행하여 `iot-gateway` 리소스 그룹에서 IoT Hub를 만듭니다.

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

기본적으로 도구는 IoT Hub를 무료 가격 책정 계층에 만듭니다. 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

> [!NOTE]
> IoT hub의 이름은 전역적으로 고유해야 합니다. Azure IoT Hub의 F1 버전은 사용자의 Azure 구독에서 하나만 만들 수 있습니다.

## <a name="register-your-device-in-your-iot-hub"></a>IoT Hub에 장치 등록

IoT Hub와 메시지를 주고 받는 각 장치는 고유한 ID로 등록되어야 합니다.
다음 명령을 실행하여 IoT Hub에 장치를 등록합니다.

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>요약

IoT Hub를 만들고 IoT Hub에 장치 ID로 논리적 장치를 등록했습니다. 이제 물리적 장치에서 클라우드의 IoT Hub로 데이터를 보내도록 게이트웨이 샘플 응용 프로그램을 구성하고 실행하는 방법을 배울 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[BLE 샘플 앱 구성 및 실행](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)
