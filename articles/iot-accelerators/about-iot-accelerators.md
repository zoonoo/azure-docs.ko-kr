---
title: IoT 솔루션 가속기 소개 - Azure | Microsoft Docs
description: Azure IoT 솔루션 가속기에 대해 알아봅니다. IoT 솔루션 가속기는 완전한 엔드투엔드이며 IoT 솔루션을 배포할 준비가 되었습니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629071"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT 솔루션 가속기란?

일반적으로 클라우드 기반 IoT 솔루션은 사용자 지정 코드 및 클라우드 서비스를 사용하여 디바이스 연결, 데이터 처리와 분석 및 표시를 관리합니다.

IoT 솔루션 가속기는 일반적인 IoT 시나리오를 구현하는 완전하고 배포 준비가 완료된 IoT 솔루션입니다. 시나리오에는 연결된 팩터리 및 디바이스 시뮬레이션이 포함되어 있습니다. 솔루션 가속기를 배포할 때 배포에는 필수 애플리케이션 코드와 함께 모든 필수 클라우드 기반 서비스가 포함됩니다.

솔루션 가속기는 고유한 IoT 솔루션에 대한 시작점입니다. 모든 솔루션 가속기의 소스 코드는 오픈 소스이며 GitHub에서 사용할 수 있습니다. 요구 사항에 맞게 솔루션 가속기를 다운로드하고 사용자 지정하는 것이 좋습니다.

또한 처음부터 사용자 지정 IoT 솔루션을 빌드하기 전에 솔루션 가속기를 학습 도구로 사용할 수 있습니다. 솔루션 가속기는 수행할 클라우드 기반 IoT 솔루션에 대해 검증된 사례를 구현합니다.

각 솔루션 가속기의 애플리케이션 코드에는 솔루션 가속기를 관리할 수 있는 웹앱이 포함되어 있습니다.

> [!NOTE]
> 원격 모니터링 및 예측 유지 관리 솔루션이 [Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators) 사이트에서 제거되었습니다. 자세한 내용은 [Azure IoT 솔루션 가속기란(이전 버전)?](/previous-versions/azure/iot-accelerators/about-iot-accelerators)을 참조하세요.

## <a name="supported-iot-scenarios"></a>지원되는 IoT 시나리오

현재 두 가지 솔루션 가속기를 배포에 사용할 수 있습니다.

### <a name="connected-factory"></a>연결된 팩터리

[연결된 팩터리 솔루션 가속기](iot-accelerators-connected-factory-features.md)를 사용하여 [OPC 통합 아키텍처](https://opcfoundation.org/about/opc-technologies/opc-ua/) 인터페이스를 사용하여 산업 자산의 원격 분석을 수집하고 제어합니다. 산업 자산에는 공장 생산 라인에서 어셈블리 및 테스트 스테이션이 포함될 수 있습니다.

연결된 팩터리 대시보드를 사용하여 산업용 디바이스를 모니터링하고 관리할 수 있습니다.

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="연결된 팩터리 솔루션 대시보드를 보여주는 스크린샷." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>디바이스 시뮬레이션

[디바이스 시뮬레이션 솔루션 가속기](iot-accelerators-device-simulation-overview.md)를 사용하여 사실적 원격 분석을 생성하는 시뮬레이션된 디바이스를 실행합니다. 이 솔루션 가속기를 사용하여 다른 솔루션 가속기의 동작을 테스트하거나 고유한 사용자 지정 IoT 솔루션을 테스트할 수 있습니다.

디바이스 시뮬레이션 웹앱을 사용하여 시뮬레이션을 구성하고 실행할 수 있습니다.

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="디바이스 시뮬레이션 솔루션 대시보드를 보여주는 스크린샷." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>디자인 원칙

모든 솔루션 가속기는 동일한 디자인 원칙 및 목표를 수행합니다. 다음과 같이 설계되었습니다.

* **확장 가능** - 수백만 개의 연결된 디바이스를 연결하고 관리할 수 있습니다.
* **확장 가능**, 요구 사항에 맞게 사용자 지정할 수 있습니다.
* **포괄 가능**, 작동 방식 및 구현되는 방법을 이해할 수 있습니다.
* **모듈식**, 대안에 대한 서비스를 교환할 수 있습니다.
* **보안** - 기본 제공 연결 및 디바이스 보안 기능을 사용하여 Azure 보안을 결합합니다.

## <a name="architectures-and-languages"></a>아키텍처 및 언어

원래 솔루션 가속기는 MVC(모델-뷰-컨트롤러) 아키텍처를 사용하는 .NET을 사용하여 작성되었습니다. Microsoft는 솔루션 가속기를 새로운 마이크로 서비스 아키텍처로 업데이트하고 있습니다. 다음 표에서는 GitHub 리포지토리에 대한 링크를 사용하여 솔루션 가속기의 현재 상태를 보여줍니다.

| 솔루션 가속기   | Architecture  | 언어     |
| ---------------------- | ------------- | ------------- |
| 연결된 팩터리      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| 디바이스 시뮬레이션      | 마이크로 서비스 | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

마이크로서비스 아키텍처에 대해 자세히 알아보려면 [Azure IoT 참조 아키텍처 소개](/azure/architecture/reference-architectures/iot/)를 참조하세요.

## <a name="deployment-options"></a>배포 옵션

[Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#) 사이트에서 또는 명령줄을 사용하여 솔루션 가속기를 배포할 수 있습니다.

솔루션 가속기를 실행하는 비용은 조합된 [기본 Azure 서비스 실행 비용](https://azure.microsoft.com/pricing)입니다. 배포 옵션을 선택할 때 사용되는 Azure 서비스의 세부 정보가 표시됩니다.

## <a name="next-steps"></a>다음 단계

IoT 솔루션 가속기 중 하나를 사용해 보려면 빠른 시작 [연결된 팩터리 솔루션 사용해보기](quickstart-connected-factory-deploy.md)를 확인하세요.
