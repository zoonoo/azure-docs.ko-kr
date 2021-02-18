---
title: Azure IoT 장치 개발자를 위한 연결 옵션에 대 한 자세한 정보
description: Azure IoT 장치 개발자를 위한 일반적으로 사용 되는 장치 연결 옵션 및 도구에 대해 알아봅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654367"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>개요: Azure IoT 장치 개발자를 위한 연결 옵션
장치를 사용 하는 개발자는 Azure IoT 장치를 연결 하 고 관리 하기 위한 몇 가지 옵션을 사용할 수 있습니다. 이 문서에서는 장치를 연결 하 고 관리 하는 데 가장 일반적으로 사용 되는 옵션 및 도구에 대해 간략하게 설명 합니다.

장치에 대 한 Azure IoT 연결 옵션을 평가할 때 다음 항목을 비교 하는 것이 좋습니다.
- Azure IoT 장치 응용 프로그램 플랫폼
- 장치 연결 및 관리를 위한 도구

## <a name="application-platforms-iot-central-and-iot-hub"></a>응용 프로그램 플랫폼: IoT Central 및 IoT Hub
Azure IoT에는 장치 사용 클라우드 응용 프로그램에 대 한 플랫폼인 Azure IoT Central 및 Azure IoT Hub의 두 서비스가 포함 되어 있습니다. 하나를 사용 하 여 IoT 응용 프로그램을 호스트 하 고 장치를 연결할 수 있습니다.
- [IoT Central](../iot-central/core/overview-iot-central.md) 은 IoT 솔루션 작업의 복잡성과 비용을 줄이도록 설계 되었습니다. IoT 응용 프로그램을 호스팅하기 위한 전체 플랫폼을 제공 하는 SaaS (software as a service) 응용 프로그램입니다. IoT Central 웹 UI를 사용 하 여 IoT 응용 프로그램을 만들고 관리 하는 전체 수명 주기를 간소화할 수 있습니다. 웹 UI는 응용 프로그램을 만드는 작업을 간소화 하 고 몇 개의 장치에서 연결 하 고 관리 하는 작업을 간소화 합니다. IoT Central는 IoT Hub를 사용 하 여 응용 프로그램을 만들고 관리 하지만 세부 정보를 사용자에 게 투명 하 게 유지 합니다. 일반적으로 IoT Central는 웹 UI를 통해 복잡성 및 개발 노력을 줄이고 간소화 된 장치 관리를 제공 합니다.
- [IoT Hub](../iot-hub/about-iot-hub.md) 은 IoT 응용 프로그램 및 연결 된 장치 간의 양방향 통신을 위해 중앙 메시지 허브 역할을 합니다. IoT 응용 프로그램을 호스트 하는 플랫폼을 제공 하는 PaaS (platform as a service) 응용 프로그램입니다. IoT Central 처럼 수백만 개의 장치를 지원 하도록 확장할 수 있습니다. 일반적으로 IoT Hub는 응용 프로그램 디자인에 대 한 더 많은 제어 및 사용자 지정을 제공 하 고 서비스를 사용 하기 위한 개발자 도구 옵션을 제공 합니다 .이는 개발 및 관리 복잡성이 증가 하는 비용입니다.

## <a name="tools-to-connect-and-manage-devices"></a>장치를 연결 하 고 관리 하는 도구
IoT Hub 또는 IoT Central를 선택 하 여 IoT 응용 프로그램을 호스트 한 후에는 몇 가지 개발자 도구 옵션을 사용할 수 있습니다. 이러한 도구를 사용 하 여 선택한 IoT 응용 프로그램 플랫폼에 연결 하 고 응용 프로그램 및 장치를 만들고 관리할 수 있습니다. 다음 표에서는 일반적인 도구 옵션을 요약 합니다. 

> [!NOTE]
> 다음 도구 외에도 REST API의, Azure Sdk 또는 Azure Resource Manager 템플릿을 사용 하 여 프로그래밍 방식으로 IoT 응용 프로그램을 만들고 관리할 수 있습니다. [IoT Hub](../iot-hub/about-iot-hub.md) 및 [IoT Central](../iot-central/core/overview-iot-central.md) 서비스 설명서에서 자세히 알아볼 수 있습니다.

|도구  |IoT 플랫폼 &nbsp; &nbsp; &nbsp; 지원 &nbsp; |문서화  |설명  |
|---------|---------|---------|---------|
|중앙 웹 UI     | Central | [중부 빠른 시작](../iot-central/core/quick-deploy-iot-central.md) | IoT Central에 대 한 브라우저 기반 포털입니다. |
|Azure portal     | 허브, 중앙      | [Azure Portal를 사용 하 여 IoT Hub 만들기](../iot-hub/iot-hub-create-through-portal.md), [Azure Portal에서 IoT Central 관리](../iot-central/core/howto-manage-iot-central-from-portal.md)| IoT Hub 및 장치용 브라우저 기반 포털입니다. 는 IoT Central를 포함 하 여 다른 Azure 리소스에도 작동 합니다. |
|Azure CLI     | 허브, 중앙          | [CLI를 사용 하 여 IoT Hub 만들기](../iot-hub/iot-hub-create-using-cli.md), [IoT Central 관리 Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | IoT 응용 프로그램을 만들고 관리 하기 위한 명령줄 인터페이스입니다. |
|Azure PowerShell     | 허브, 중앙   | [PowerShell을 사용 하 여 IoT Hub 만들기](../iot-hub/iot-hub-create-using-powershell.md), [Azure PowerShell에서 IoT Central 관리](../iot-central/core/howto-manage-iot-central-from-powershell.md) | IoT 응용 프로그램을 만들고 관리 하기 위한 PowerShell 인터페이스 |
|VS Code용 Azure IoT Tools  | 허브 | [VS Code 도구를 사용 하 여 IoT hub 만들기](../iot-hub/iot-hub-create-use-iot-toolkit.md) | IoT Hub 응용 프로그램에 대 한 VS Code 확장입니다. |
|Azure IoT 탐색기     | 허브 | [Azure IoT 탐색기](https://github.com/Azure/azure-iot-explorer) | IoT hub를 만들 수 없습니다. 기존 IoT hub에 연결 하 여 장치를 관리 합니다. CLI 또는 포털에서 자주 사용 됩니다.|

## <a name="next-steps"></a>다음 단계
장치를 Azure IoT에 연결 하는 옵션에 대 한 자세한 내용을 보려면 다음 빠른 시작을 탐색 하세요.
- IoT Central: [Azure IoT Central 응용 프로그램 만들기](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [장치에서 IoT 허브로 원격 분석을 보내고 Azure CLI를 사용](../iot-hub/quickstart-send-telemetry-cli.md) 하 여 모니터링