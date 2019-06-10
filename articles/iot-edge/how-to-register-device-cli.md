---
title: 명령줄에서 새 디바이스 등록 - Azure IoT Edge | Microsoft Docs
description: Azure CLI용 IoT 확장을 사용하여 새 IoT Edge 디바이스를 등록하고 연결 문자열 검색
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495386"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Azure CLI를 사용하여 새 Azure IoT Edge 디바이스 등록

Azure IoT Edge에서 IoT 디바이스를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 장치를 등록 하면 IoT Edge 워크 로드에 대 한 장치를 설정 하는 연결 문자열을 수신 합니다.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)는 IoT Edge 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. 이 기능을 사용하면 Azure IoT Hub 리소스, 디바이스 프로비전 서비스 인스턴스 및 연결된 허브를 즉시 관리할 수 있습니다. 새로운 IoT 확장은 디바이스 관리 및 전체 IoT Edge 같은 기능으로 Azure CLI를 강화합니다.

이 문서에서는 Azure CLI를 사용하여 새 IoT Edge 디바이스를 등록하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md)
* 사용자 환경의 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.24 이상이어야 합니다. `az –-version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.
* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>디바이스 만들기

사용 된 [az iot hub 장치 id 만들기](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) IoT hub에서 새 장치 id를 만드는 명령입니다. 예를 들면 다음과 같습니다.

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

이 명령에는 세 개의 매개 변수가 포함됩니다.

* **device-id**: IoT Hub에 고유한 설명 이름을 제공합니다.

* **hub-name**: IoT Hub의 이름을 제공합니다.

* **edge-enabled**: 이 매개 변수는 디바이스를 IoT Edge에서 사용한다고 선언합니다.

   ![az iot hub device-identity create output](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>모든 디바이스 보기

사용 된 [az iot hub 장치 id 목록](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) IoT hub의 모든 장치를 보려면 명령입니다. 예를 들면 다음과 같습니다.

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true**로 설정됩니다.

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다. 사용 된 [az iot hub 장치 id 연결 문자열 표시](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) 단일 장치에 대 한 연결 문자열을 반환 하는 명령:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 매개 변수의 값은 대소문자를 구분합니다. 연결 문자열 앞뒤의 따옴표를 복사하지 마세요.

## <a name="next-steps"></a>다음 단계

설명 하는 방법 [Azure CLI를 사용 하 여 장치에 모듈을 배포](how-to-deploy-modules-cli.md)합니다.
