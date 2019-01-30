---
title: 원격 모니터링 솔루션의 Edge 스키마 추가 - Azure | Microsoft Docs
description: 이 문서에서는 IoT Edge 디바이스를 원격 모니터링 솔루션 가속기에 추가하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 5172ba9881f80e202bdc69689e67befb79101dc0
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450663"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기에 IoT Edge 디바이스 추가

솔루션 가속기에 [IoT Edge](../iot-edge/about-iot-edge.md) 디바이스를 추가하려면 다음 두 단계를 완료합니다.

1. 원격 모니터링 솔루션 가속기 웹 UI의 **디바이스** 페이지에서 Edge 디바이스를 추가합니다.
1. Edge 디바이스에 IoT Edge 런타임을 설치합니다.

## <a name="add-the-iot-edge-device"></a>IoT Edge 디바이스 추가

IoT Edge 디바이스를 원격 모니터링 솔루션 가속기에 추가하려면 웹 UI의 **디바이스** 페이지로 이동한 후 **+새 디바이스**를 클릭합니다.

**새 디바이스** 패널에서 **IoT Edge 디바이스**를 선택합니다. 다른 설정의 경우 기본값을 그대로 둡니다. 그런 다음 **적용**을 클릭합니다.

![IoT Edge 디바이스 추가](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>IoT Edge 디바이스를 추가하는 다른 방법

솔루션 가속기에서 IoT Hub 인스턴스에 직접 IoT Edge 디바이스를 등록할 수도 있습니다. 이러한 방법 가이드 중 하나를 수행하기 전에 솔루션 가속기에서 IoT Hub의 이름을 알고 있어야 합니다.

- [Azure Portal에서 새 Azure IoT Edge 디바이스 등록](../iot-edge/how-to-register-device-portal.md)
- [Azure CLI를 사용하여 새 Azure IoT Edge 디바이스 등록](../iot-edge/how-to-register-device-cli.md)
- [Visual Studio Code에서 새 Azure IoT Edge 디바이스 등록](../iot-edge/how-to-register-device-vscode.md)

원격 모니터링 솔루션 가속기에서 IoT Hub에 디바이스를 직접 등록하면 웹 UI의 **디바이스**에 표시됩니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

Edge 디바이스에 모듈을 배포하려면 먼저 IoT Edge 런타임을 실제 디바이스에 설치해야 합니다. 다음 방법 가이드는 일반적인 디바이스 플랫폼에 런타임을 설치하는 방법을 보여 줍니다.

- [Linux(x64)에서 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge-linux.md)
- [Linux(ARM32v7/armhf)에 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Windows 컨테이너에서 사용하기 위해 Windows에 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Linux 컨테이너에서 사용하기 위해 Windows에 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Windows IoT Core에 IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>다음 단계

IoT Edge 디바이스를 준비했으므로 다음 단계는 모듈을 배포하는 것입니다. [원격 모니터링 솔루션 가속기로 IoT Edge 패키지 가져오기](iot-accelerators-remote-monitoring-import-edge-package.md)를 참조하세요.
