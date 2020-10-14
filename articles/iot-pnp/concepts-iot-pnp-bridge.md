---
title: IoT 플러그 앤 플레이 브리지 | Microsoft Docs
description: IoT 플러그 앤 플레이 브리지 및이 브리지를 사용 하 여 Windows 또는 Linux 게이트웨이에 연결 된 기존 장치를 IoT 플러그 앤 플레이 장치로 연결 하는 방법에 대해 알아봅니다.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0435fe3946118d59d786dd3e6cec350a5ab4eee4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046454"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지

IoT 플러그 앤 플레이 브리지는 Windows 또는 Linux 게이트웨이에 연결 된 기존 장치를 IoT 플러그 앤 플레이 장치로 연결 하는 오픈 소스 응용 프로그램입니다. Windows 또는 Linux 컴퓨터에서 응용 프로그램을 설치 하 고 구성한 후에는이를 사용 하 여 연결 된 장치를 IoT hub에 연결할 수 있습니다. 브리지를 사용 하 여 연결 된 장치에서 전송 하는 원격 분석에 IoT 플러그 앤 플레이 인터페이스를 매핑하고, 장치 속성을 작업 하 고, 명령을 호출할 수 있습니다.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="왼쪽에는 IoT 플러그 앤 플레이 브리지가 포함 된 Windows 또는 Linux PC에 몇 가지 기존 센서 (유선 및 무선 모두)가 연결 되어 있습니다. IoT 플러그 앤 플레이 브리지는 오른쪽에 있는 IoT hub에 연결 합니다.":::

IoT 플러그 앤 플레이 브리지는 Windows 10 또는 Linux를 실행 하는 모든 IoT 장치, 산업용 PC, 서버 또는 게이트웨이에서 독립 실행형 실행 파일로 배포할 수 있습니다. 응용 프로그램 코드로 컴파일될 수도 있습니다. 간단한 구성 JSON 파일은 Azure에 노출 해야 하는 연결 된 장치/주변 장치를 IoT 플러그 앤 플레이 브리지에 알려 줍니다.

## <a name="supported-protocols-and-sensors"></a>지원 되는 프로토콜 및 센서

IoT 플러그 앤 플레이 브리지는 어댑터 설명서에 대 한 링크를 사용 하 여 기본적으로 다음과 같은 유형의 주변 장치를 지원 합니다.

|파악|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |예|아니요|
|[카메라](https://aka.ms/iot-pnp-bridge-camera)               |예|아니요|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |예|예|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |예|예|
|[연속적인](https://aka.ms/iot-pnp-bridge-serial)                |예|예|
|[Windows USB 주변 장치](https://aka.ms/iot-pnp-bridge-usb)  |예|해당 없음|

>[!Important]
>개발자는 iot 플러그 앤 플레이 브리지를 확장 하 여 **[iot 플러그 앤 플레이 bridge 개발자 설명서](https://aka.ms/iot-pnp-bridge-dev-doc)** 의 지침을 통해 추가 장치 프로토콜을 지원할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

### <a name="os-platform"></a>OS 플랫폼

지원 되는 OS 플랫폼 및 버전은 다음과 같습니다.

|플랫폼  |지원되는 버전  |
|---------|---------|
|Windows 10 |     모든 Windows Sku가 지원 됩니다. 예: IoT Enterprise, Server, Desktop, IoT Core. *카메라 상태 모니터링 기능을 위해 20H1 이상 빌드를 권장 합니다. 모든 다른 기능은 모든 Windows 10 빌드에서 사용할 수 있습니다.*  |
|Linux     |Ubuntu 18.04에서 테스트 되 고 지원 되는 다른 배포판의 기능은 테스트 되지 않았습니다.         |
||

### <a name="hardware"></a>하드웨어

- 위의 OS Sku 및 버전을 지원할 수 있는 모든 하드웨어 플랫폼입니다.
- 직렬, USB, Bluetooth 및 카메라 주변 기기와 센서는 기본적으로 지원 됩니다. IoT 플러그 앤 플레이 브리지를 확장 하 여 사용자 지정 주변 장치 또는 센서를 지원할 수 있습니다 ([위의 주변 장치 섹션 참조](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>개발 환경.

IoT 플러그 앤 플레이 브리지를 빌드하고 확장 하 고 개발 하려면 다음이 필요 합니다.  

- [Visual studio (Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/)와 같은 c + + 컴파일을 지 원하는 개발 환경-visual studio를 설치할 때 c + +를 사용한 데스크톱 개발 워크 로드를 포함 하는지 확인 합니다.
- [Cmake](https://cmake.org/download/) -csa를 설치할 때 옵션을 선택 `Add CMake to the system PATH` 합니다.
- Windows에서 빌드하는 경우에도 Windows 17763 SDK를 다운로드 해야 합니다. [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [장치 C SDK를 Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-c)합니다. 이 리포지토리의 포함 된 빌드 스크립트는 필요한 Azure IoT C SDK를 자동으로 복제 합니다.

### <a name="azure-iot-products-and-tools"></a>Azure IoT 제품 및 도구

- **Azure IoT Hub** -장치를에 연결 하려면 azure 구독에서 [azure IoT Hub](../iot-hub/index.yml) 가 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. IoT hub가 없는 경우 [다음 지침에 따라 새로 만듭니다](../iot-hub/iot-hub-create-using-cli.md).

> [!Note]
> IoT 플러그 앤 플레이는 현재 미국 중부, 북유럽 및 일본 동부에서 생성된 IoT 허브에서 사용할 수 있습니다. IoT 플러그 앤 플레이 지원은 기본 계층 IoT 허브에 포함되지 않습니다. IoT 플러그 앤 플레이 장치와 상호 작용 하려면 Azure IoT 탐색기 도구를 사용할 수 있습니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 플러그 앤 플레이 브리지 아키텍처

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="왼쪽에는 IoT 플러그 앤 플레이 브리지가 포함 된 Windows 또는 Linux PC에 몇 가지 기존 센서 (유선 및 무선 모두)가 연결 되어 있습니다. IoT 플러그 앤 플레이 브리지는 오른쪽에 있는 IoT hub에 연결 합니다.":::

## <a name="download-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 다운로드

[IoT 플러그 앤 플레이 브리지 릴리스](https://aka.ms/iot-pnp-bridge-releases) 에서 지원 되는 어댑터를 사용 하 여 브리지의 미리 작성 된 버전을 다운로드 하 고 최신 릴리스에 대 한 자산 목록을 확장할 수 있습니다. 운영 체제용 응용 프로그램의 최신 버전을 다운로드 합니다.

또한 [GitHub에서 IoT 플러그 앤 플레이 bridge](https://aka.ms/bridge)의 소스 코드를 다운로드 하 고 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 브리지의 아키텍처에 대 한 개요를 만들었으므로 다음 단계는에 대 한 자세한 내용입니다.

- [IoT 플러그 앤 플레이 브리지를 사용 하는 방법](./howto-use-iot-pnp-bridge.md)
- [IoT 플러그 앤 플레이 브리지에 대 한 GitHub 개발자 참조를 참조 하세요.](https://aka.ms/iot-pnp-bridge-dev-doc)
- [GitHub의 IoT 플러그 앤 플레이 브리지](https://aka.ms/iotplugandplaybridge)