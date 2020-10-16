---
title: Azure IoT Central에서 Azure Sphere 디바이스 연결 | Microsoft Docs
description: Azure Sphere(DevKit) 디바이스를 Azure IoT Central 애플리케이션에 연결하는 방법을 알아봅니다.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123426"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Azure Sphere 디바이스 추가

*이 문서는 디바이스 개발자에게 적용됩니다.*

이 문서에서는 Azure Sphere(DevKit) 디바이스를 Azure IoT Central 애플리케이션에 연결하는 방법을 보여 줍니다.

Azure Sphere는 인터넷에 연결된 디바이스를 위한 기본 제공 통신 및 보안 기능을 갖춘 안전한 고급 애플리케이션 플랫폼입니다. 안전하게 연결된 교차형 MCU(마이크로 컨트롤러 단위), 사용자 지정 상위 수준 Linux 기반 OS(운영 체제) 및 지속적이고 갱신 가능한 보안을 제공하는 클라우드 기반 보안 서비스로 구성됩니다. 자세한 내용은 [Azure Sphere란?](/azure-sphere/product-overview/what-is-azure-sphere)을 참조하세요.

[Azure Sphere 개발 키트](https://azure.microsoft.com/services/azure-sphere/get-started/)는 Azure Sphere 애플리케이션의 프로토타입 생성 및 개발을 시작하는 데 필요한 모든 것을 제공합니다. Azure Sphere를 사용한 Azure IoT Central을 통해 IoT 솔루션에 대한 엔드투엔드 스택을 사용할 수 있습니다. Azure Sphere는 디바이스 지원과 함께 IoT Central을 코드 없이 관리형 IoT 애플리케이션 플랫폼으로 제공합니다.

이 방법 문서에서는 다음을 수행합니다.

- 라이브러리의 Azure Sphere DevKit 디바이스 템플릿을 사용하여 IoT Central에서 Azure Sphere 디바이스를 만듭니다.
- Azure IoT에 대한 Azure Sphere DevKit 디바이스를 준비합니다.
- Azure Sphere DevKit를 Azure IoT Central에 연결합니다.
- IoT Central에서 디바이스에 대한 원격 분석을 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

- Azure IoT Central 애플리케이션.
- Visual Studio 2019, 버전 16.4 이상.
- [Seeed Studios의 Azure Sphere MT3620 개발 키트](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> 물리적 디바이스가 없는 경우에는 첫 번째 단계 후 마지막 섹션으로 건너뛰어 시뮬레이션된 디바이스를 시도합니다.

## <a name="create-the-device-in-iot-central"></a>IoT Central에서 디바이스 만들기

IoT Central에서 Azure Sphere 디바이스를 만들려면 다음을 수행합니다.

1. Azure IoT Central 애플리케이션에서 **디바이스 템플릿** 탭을 선택하고 **+ 새로 만들기**를 선택합니다. **추천 디바이스 템플릿 사용** 섹션에서 **Azure Sphere 샘플 디바이스**를 선택합니다.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Azure Sphere DevKit에 대한 디바이스 템플릿":::

1. 디바이스 템플릿에서 **개요**라고 하는 보기를 편집하여 **온도** 및 **단추 누르기**가 표시되도록 합니다.

1. **디바이스 및 클라우드 데이터 편집** 보기 유형을 선택하여 읽기/쓰기 속성 **상태 LED**를 표시하는 다른 보기를 추가합니다. **상태 LED** 속성을 양식의 오른쪽에 있는 빈 점선 사각형으로 끕니다. **저장**을 선택합니다.

## <a name="prepare-the-device"></a>디바이스 준비

Azure Sphere DevKit 디바이스를 IoT Central에 연결하려면 [디바이스 및 개발 환경을 설정](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT)해야 합니다.

## <a name="connect-the-device"></a>디바이스 연결

샘플을 IoT Central에 연결되도록 설정하려면 [Azure IoT Central 애플리케이션을 구성한 다음, 샘플의 애플리케이션 매니페스트를 수정](https://aka.ms/iotcentral-sphere-git-readme)해야 합니다.

## <a name="view-the-telemetry-from-the-device"></a>디바이스에 대한 원격 분석 확인

디바이스가 IoT Central에 연결되면 대시보드에서 원격 분석을 볼 수 있습니다.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Azure Sphere DevKit에 대한 디바이스 템플릿":::

## <a name="create-a-simulated-device"></a>시뮬레이션된 디바이스 만들기

물리적 Azure Sphere DevKit 디바이스가 없으면 시뮬레이션된 디바이스를 만들어 Azure IoT Central 애플리케이션을 사용할 수 있습니다.

시뮬레이션된 디바이스를 만들려면 다음을 수행합니다.

- **디바이스 > Azure IoT Sphere**를 선택합니다.
- **+새로 만들기**를 선택합니다.
- 고유한 **디바이스 ID** 및 친숙한 **디바이스 이름**을 입력합니다.
- **시뮬레이션된** 설정을 사용하도록 설정합니다.
- **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

디바이스 개발자라면 다음과 같은 몇 가지 단계를 살펴보세요.

- [Azure IoT Central에서 디바이스 연결](./concepts-get-connected.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.