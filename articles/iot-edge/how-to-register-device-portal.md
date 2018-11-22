---
title: 새 Azure IoT Edge 장치 등록(포털) | Microsoft Docs
description: Azure Portal을 사용하여 새 IoT Edge 장치를 등록
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6657203c76bc03a262fbcbd30b5bf74b5be140eb
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577501"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Azure Portal에서 새 Azure IoT Edge 장치 등록

Azure IoT Edge에서 IoT 장치를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 장치를 등록하면 Edge 워크로드에 대해 장치를 설정하는 데 사용할 수 있는 연결 문자열을 받게 됩니다. 

이 문서에서는 Azure Portal을 사용하여 새 IoT Edge 장치를 등록하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) 

## <a name="create-a-device"></a>장치 만들기

Azure Portal에서 IoT Edge 장치는 IoT Hub에 연결되지만 에지를 사용할 수 없는 장치와 별도로 만들어지고 관리됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
2. 메뉴에서 **IoT Edge**를 선택합니다.
3. **IoT Edge 장치 추가**를 선택합니다. 
4. 설명이 포함된 장치 ID를 제공합니다. 
5. **저장**을 선택합니다. 

## <a name="view-all-devices"></a>모든 장치 보기

IoT Hub에 연결된 에지를 사용할 수 있는 모든 장치는 **IoT Edge** 페이지에 나열되어 있습니다. 

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

장치를 설정할 준비가 되면, 물리적 장치를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. 포털에서 **IoT Edge** 페이지의 Edge 장치 목록에서 장치 ID를 클릭합니다. 
2. **연결 문자열(기본 키)** 또는 **연결 문자열(보조 키)** 중 하나의 값을 복사합니다. 

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 장치에 모듈을 배포](how-to-deploy-modules-portal.md)하는 방법을 알아봅니다.
