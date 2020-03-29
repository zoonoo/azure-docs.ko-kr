---
title: 게이트웨이를 사용하여 modbus 프로토콜 변환 - Azure IoT Edge | Microsoft Docs
description: Modbus TCP를 사용하는 디바이스에서 IoT Edge 게이트웨이 디바이스를 만들어 Azure IoT Hub와 통신할 수 있도록 합니다.
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511147"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>IoT Edge 디바이스 게이트웨이를 통해 Modbus TCP 디바이스 연결

Modbus TCP 또는 RTU 프로토콜을 사용하는 IoT 장치를 Azure IoT 허브에 연결하려는 경우 IoT Edge 장치를 게이트웨이로 사용할 수 있습니다. 게이트웨이 디바이스는 Modbus 디바이스에서 데이터를 읽은 다음 지원되는 프로토콜을 사용하여 해당 데이터를 클라우드에 전달합니다.

![Modbus 장치는 IoT Edge 게이트웨이를 통해 IoT 허브에 연결됩니다.](./media/deploy-modbus-gateway/diagram.png)

이 문서에서는 Modbus 모듈에 대한 사용자 고유의 컨테이너 이미지를 만드는 방법(또는 미리 빌드된 샘플을 사용할 수 있음) 및 게이트웨이로 작동할 IoT Edge 디바이스에 이 이미지를 배포하는 방법에 대해 설명합니다.

이 문서에서는 Modbus TCP 프로토콜을 사용한다고 가정합니다. Modbus RTU를 지원 하도록 모듈을 구성 하는 방법에 대 한 자세한 내용은 GitHub에 [Azure IoT 가장자리 모드 버스 모듈](https://github.com/Azure/iot-edge-modbus) 프로젝트를 참조 하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure IoT Edge 디바이스 하나를 설정하는 방법에 대한 연습은 Windows 또는 [Linux에서](quickstart-linux.md) [Azure IoT 에지 배포를](quickstart.md) 참조하십시오.
* IoT Edge 디바이스에 대한 기본 키 연결 문자열입니다.
* Modbus TCP를 지원하는 물리적 또는 시뮬레이션된 Modbus 디바이스 IPv4 주소를 알아야 합니다.

## <a name="prepare-a-modbus-container"></a>Modbus 컨테이너 준비

Modbus 게이트웨이 기능을 테스트하려는 경우 사용할 수 있는 샘플 모듈은 Microsoft에 있습니다. Azure 마켓플레이스, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)또는 이미지 URI를 통해 모듈에 액세스할 수 `mcr.microsoft.com/azureiotedge/modbus:1.0`있습니다.

사용자 고유의 모듈을 만들고 사용자 환경에 맞게 사용자 지정하려는 경우 GitHub에 오픈 소스 [Azure IoT Edge Modbus 모듈](https://github.com/Azure/iot-edge-modbus) 프로젝트가 있습니다. 해당 프로젝트의 지침에 따라 사용자 고유의 컨테이너 이미지를 만듭니다. 컨테이너 이미지를 만들려면 [Visual Studio에서 C# 모듈 개발을](how-to-visual-studio-develop-csharp-module.md) 참조하거나 Visual Studio [코드에서 모듈 을 개발합니다.](how-to-vs-code-develop-module.md) 이러한 문서에서는 새 모듈을 만들고 컨테이너 이미지를 레지스트리에 게시하는 방법에 대한 지침을 제공합니다.

## <a name="try-the-solution"></a>솔루션 사용

이 섹션에서는 IoT Edge 장치에 Microsoft의 샘플 Modbus 모듈을 배포하는 방법을 안내합니다.

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하여 IoT Edge 디바이스를 클릭합니다.

3. **모듈 설정**을 선택합니다.

4. IoT 에지 모듈 섹션에서 Modbus **모듈을 추가합니다.**

   1. 드롭다운 **추가를** 클릭하고 **마켓플레이스 모듈을**선택합니다.
   2. 검색 `Modbus` 및 마이크로 소프트에 의해 **모드 버스 TCP 모듈을** 선택합니다.
   3. 이 모듈은 IoT Hub에 맞게 자동으로 구성되며 IoT Edge 모듈 목록에 표시됩니다. 경로도 자동으로 구성됩니다. **검토 + 만들기를**선택합니다.
   4. 배포 매니페스트를 검토하고 **만들기를**선택합니다.

5. Modbus 모듈을 `ModbusTCPModule`선택합니다. **Module Twin Settings** 모듈 트윈 원하는 속성에 필요한 JSON은 자동 채워집니다.

6. JSON에서 **SlaveConnection** 속성을 찾아 Modbus 장치의 IPv4 주소로 값을 설정합니다.

7. **업데이트를**선택합니다.

8. **검토 + 만들기,** 배포를 검토한 다음 **을**선택합니다.

9. 디바이스 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다. IoT Edge `ModbusTCPModule` 런타임과 함께 새 모듈이 실행되는 것을 볼 수 있습니다.

## <a name="view-data"></a>데이터 보기

Modbus 모듈을 통해 들어오는 데이터를 봅니다.

```cmd/sh
iotedge logs modbus
```

또한 Visual Studio 코드(이전의 Azure IoT 도구 키트 [확장)에 대한 Azure IoT Hub 확장을](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 사용하여 장치가 보내는 원격 분석을 볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* IoT Edge 장치가 게이트웨이 역할을 하는 방법에 대해 자세히 알아보려면 [투명 게이트웨이 역할을 하는 IoT Edge 장치 만들기를](./how-to-create-transparent-gateway.md)참조하십시오.
* IoT Edge 모듈의 작동 방식에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해를](iot-edge-modules.md)참조하십시오.
