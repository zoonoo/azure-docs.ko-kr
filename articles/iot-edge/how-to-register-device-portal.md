---
title: Azure Portal에서 새 디바이스 등록 - Azure IoT Edge | Microsoft Docs
description: Azure Portal을 사용하여 새 IoT Edge 디바이스를 등록하고 연결 문자열 검색
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 16660fbed465cc70f16cde430024f33b8aa4350e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495349"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Azure Portal에서 새 Azure IoT Edge 디바이스 등록

Azure IoT Edge에서 IoT 디바이스를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 장치를 등록 하면 IoT Edge 워크 로드에 대 한 장치를 설정 하는 연결 문자열을 수신 합니다.

이 문서에서는 Azure Portal을 사용하여 새 IoT Edge 디바이스를 등록하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

표준에 대 한 무료 [IoT hub](../iot-hub/iot-hub-create-through-portal.md) Azure 구독에 있습니다.

## <a name="create-a-device"></a>디바이스 만들기

Azure Portal에서 IoT Edge 디바이스는 IoT Hub에 연결되지만 에지를 사용할 수 없는 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
2. 메뉴에서 **IoT Edge**를 선택합니다.
3. **IoT Edge 디바이스 추가**를 선택합니다.
4. 설명이 포함된 디바이스 ID를 제공합니다. 인증 키를 자동으로 생성 하 고 허브에 새 장치를 연결 하려면 기본 설정을 사용 합니다.
5. **저장**을 선택합니다.

## <a name="view-all-devices"></a>모든 디바이스 보기

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. **IoT Edge** 포털에서 페이지, IoT Edge 장치 목록에서 장치 ID를 클릭 합니다.
2. **연결 문자열(기본 키)** 또는 **연결 문자열(보조 키)** 중 하나의 값을 복사합니다.

## <a name="next-steps"></a>다음 단계

에 대해 알아봅니다 하는 방법 [Azure portal 사용 하 여 장치에 모듈 배포](how-to-deploy-modules-portal.md)합니다.
