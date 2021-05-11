---
title: Azure IoT 디바이스 개발자를 위한 연결 옵션에 대한 정보
description: Azure IoT 디바이스 개발자를 위한 일반적으로 사용되는 디바이스 연결 옵션 및 도구에 대해 알아봅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654367"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>개요: Azure IoT 디바이스 개발자를 위한 연결 옵션
디바이스로 작업하는 개발자로서 Azure IoT 디바이스를 연결하고 관리하는 몇 가지 옵션을 사용할 수 있습니다. 이 문서에서는 디바이스를 연결하고 관리하는 데 가장 일반적으로 사용되는 옵션 및 도구에 대해 간략하게 설명합니다.

디바이스에 대한 Azure IoT 연결 옵션을 평가할 때 다음 항목을 비교하면 유용합니다.
- Azure IoT 디바이스 애플리케이션 플랫폼
- 디바이스 연결 및 관리 도구

## <a name="application-platforms-iot-central-and-iot-hub"></a>애플리케이션 플랫폼: IoT Central 및 IoT Hub
Azure IoT에는 디바이스 사용 클라우드 애플리케이션에 대한 플랫폼인 Azure IoT Central과 Azure IoT Hub의 두 서비스가 포함되어 있습니다. 이 중 하나를 사용하여 IoT 애플리케이션을 호스트하고 디바이스를 연결할 수 있습니다.
- [IoT Central](../iot-central/core/overview-iot-central.md)은 IoT 솔루션 작업의 복잡성과 비용을 줄이기 위해 설계되었습니다. 이는 IoT 애플리케이션을 호스트하기 위한 전체 플랫폼을 제공하는 SaaS(software-as-a-service) 애플리케이션입니다. IoT Central 웹 UI를 사용하여 IoT 애플리케이션을 만들고 관리하는 전체 수명 주기를 간소화할 수 있습니다. 웹 UI는 애플리케이션을 만드는 작업과, 몇 개에서 수백만 개에 이르는 디바이스를 연결하고 관리하는 작업을 간소화합니다. IoT Central는 IoT Hub를 사용해 애플리케이션을 만들고 관리하지만 세부 정보를 사용자에게 투명하게 공개합니다. 일반적으로 IoT Central은 웹 UI를 통해 복잡성 및 개발에 드는 노력을 줄이고 간소화된 디바이스 관리를 제공합니다.
- [IoT 허브](../iot-hub/about-iot-hub.md)는 IoT 애플리케이션과 연결된 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. 이는 IoT 애플리케이션을 호스트하는 플랫폼을 제공하는 PaaS(platform-as-a-service) 애플리케이션입니다. IoT Central처럼 수백만 개의 디바이스를 지원하도록 스케일링할 수 있습니다. 일반적으로 IoT Hub는 애플리케이션 설계에 대한 더 강한 제어 및 사용자 지정을 제공하고 서비스를 사용하기 위한 개발자 도구 옵션을 제공하지만, 이 때문에 개발 및 관리에 있어서의 복잡성이 어느 정도 증가합니다.

## <a name="tools-to-connect-and-manage-devices"></a>디바이스를 연결하고 관리하는 도구
IoT Hub 또는 IoT Central을 선택하여 IoT 애플리케이션을 호스트한 후에는 몇 가지 개발자 도구 옵션을 사용할 수 있습니다. 이러한 도구를 사용하여 선택한 IoT 애플리케이션 플랫폼에 연결하고, 애플리케이션 및 디바이스를 만들고 관리할 수 있습니다. 다음 표에는 이러한 옵션이 요약되어 있습니다. 

> [!NOTE]
> 다음 도구 외에도 REST API, Azure SDK 또는 Azure Resource Manager 템플릿을 사용하여 프로그래밍 방식으로 IoT 애플리케이션을 만들고 관리할 수 있습니다. [IoT Hub](../iot-hub/about-iot-hub.md) 및 [IoT Central](../iot-central/core/overview-iot-central.md) 서비스 설명서에서 자세히 알아볼 수 있습니다.

|도구  |IoT 플랫폼 &nbsp; &nbsp; &nbsp; &nbsp; 지원 |문서화  |설명  |
|---------|---------|---------|---------|
|중앙 웹 UI     | Central | [Central 빠른 시작](../iot-central/core/quick-deploy-iot-central.md) | IoT Central용 브라우저 기반 포털입니다. |
|Azure portal     | Hub, Central      | [Azure Portal을 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-through-portal.md), [Azure Portal에서 IoT Central 관리](../iot-central/core/howto-manage-iot-central-from-portal.md)| IoT Hub 및 디바이스용 브라우저 기반 포털입니다. IoT Central을 포함하여 다른 Azure 리소스에도 작동합니다. |
|Azure CLI     | Hub, Central          | [CLI를 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-using-cli.md), [Azure CLI에서 IoT Central 관리](../iot-central/core/howto-manage-iot-central-from-cli.md) | IoT 애플리케이션을 만들고 관리하기 위한 명령줄 인터페이스입니다. |
|Azure PowerShell     | Hub, Central   | [PowerShell을 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-using-powershell.md), [Azure PowerShell에서 IoT Central 관리](../iot-central/core/howto-manage-iot-central-from-powershell.md) | IoT 애플리케이션을 만들고 관리하기 위한 PowerShell 인터페이스 |
|VS Code용 Azure IoT Tools  | 허브 | [VS Code용 도구를 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-use-iot-toolkit.md) | IoT Hub 애플리케이션에 대한 VS Code 확장입니다. |
|Azure IoT Explorer     | 허브 | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) | IoT Hub를 만들 수 없습니다. 기존 IoT Hub에 연결해 디바이스를 관리합니다. CLI 또는 포털에서 자주 사용됩니다.|

## <a name="next-steps"></a>다음 단계
디바이스를 Azure IoT에 연결하는 옵션에 대한 자세한 내용을 보려면 다음 빠른 시작을 살펴보세요.
- IoT Central: [Azure IoT Central 애플리케이션 만들기](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [디바이스에서 IoT 허브로 원격 분석을 전송하여 Azure CLI로 모니터링](../iot-hub/quickstart-send-telemetry-cli.md)