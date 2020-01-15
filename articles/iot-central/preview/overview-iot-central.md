---
title: Azure IoT Central이란? | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하고 IoT 관리 작업 및 개발에 대한 부담과 및 비용을 줄여주는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 Azure IoT Central의 기능에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 52504fb8333b286407b3f2df8f962da59b80ac53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434842"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central(미리 보기 기능)이란?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central의 [IoT 플러그 앤 플레이](../../iot-pnp/overview-iot-plug-and-play.md) 기능은 현재 공개 미리 보기로 제공됩니다. IoT 플러그 앤 플레이 지원 IoT Central [애플리케이션 템플릿](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)을 프로덕션 워크로드에 사용하지 마세요. 프로덕션 환경에서는 일반적으로 사용 가능한 최신 [애플리케이션 템플릿](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)에서 만든 IoT Central 애플리케이션을 사용합니다.

IoT Central은 엔터프라이즈급 IoT 솔루션의 개발, 관리 및 유지 관리 부담과 비용을 줄이는 IoT 애플리케이션 플랫폼입니다. IoT Central을 사용하여 빌드하기로 선택하면 복잡하고 지속적으로 진화하는 IoT 인프라를 단순히 유지 관리하는 데서 그치지 않고 IoT 데이터를 활용하여 비즈니스를 전환하는 데 시간, 비용 및 에너지를 집중할 수 있습니다.

웹 UI를 통해 디바이스 상태를 모니터링하고, 규칙을 만들고, 전체 수명 주기 동안 수백만 대의 디바이스와 해당 데이터를 관리할 수 있습니다. 뿐만 아니라 IoT 인텔리전스를 LOB(기간 업무) 애플리케이션으로 확장하여 디바이스 인사이트를 기반으로 조치를 취할 수 있습니다.

이 문서에서는 IoT Central에 대해 개략적으로 설명합니다.

- 프로젝트와 관련된 일반적인 가상 사용자.
- 애플리케이션을 만드는 방법.
- 디바이스를 애플리케이션에 연결하는 방법
- 애플리케이션을 관리하는 방법.
- IoT Central의 Azure IoT Edge 기능.
- Azure IoT Edge 런타임 기반 디바이스를 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="known-issues"></a>알려진 문제

> [!Note]
> 이러한 알려진 이슈는 IoT Central 미리 보기 애플리케이션에만 적용됩니다.

- 연속 데이터 내보내기는 Avro 형식을 지원하지 않습니다(비호환).
- GeoJSON은 현재 지원되지 않습니다.
- 지도 타일은 현재 지원되지 않습니다.
- 작업은 복합 형식을 지원하지 않습니다.
- 배열 스키마 형식은 지원되지 않습니다.
- C 디바이스 SDK와 Node.js 디바이스 및 서비스 SDK만 지원됩니다.
- 미국 및 유럽 지역에서만 사용할 수 있습니다.
- 디바이스 기능 모델의 모든 인터페이스가 동일한 파일에 인라인으로 정의되어 있어야 합니다.

## <a name="personas"></a>가상 사용자

IoT Central 설명서는 IoT Central 애플리케이션과 상호 작용하는 다음 4명의 일반적인 가상 사용자와 관련이 있습니다.

- _솔루션 개발자_는 애플리케이션에 연결하는 디바이스 유형을 정의하고 운영자에 맞게 애플리케이션을 사용자 지정하는 역할을 담당합니다.
- _운영자_는 애플리케이션에 연결된 디바이스를 관리합니다.
- _관리자_는 애플리케이션 내 [사용자 및 권한](howto-administer.md) 관리와 같은 관리 작업을 담당합니다.
- _디바이스 개발자_는 애플리케이션에 연결된 디바이스 또는 IoT Edge 모듈에서 실행되는 코드를 만듭니다.

## <a name="create-your-iot-central-application"></a>IoT Central 애플리케이션 만들기

솔루션 개발자는 IoT Central을 사용하여 조직을 위한 사용자 지정 클라우드 호스팅 IoT 솔루션을 만들 수 있습니다. 사용자 지정 IoT 솔루션은 일반적으로 다음과 같이 구성됩니다.

- 디바이스에서 원격 분석을 수신하고 해당 디바이스를 관리할 수 있게 해주는 클라우드 기반 애플리케이션.
- 클라우드 기반 애플리케이션에 연결된 사용자 지정 코드를 실행하는 여러 디바이스.

새로운 IoT Central 애플리케이션을 빠르게 배포한 다음, 브라우저에서 특정 요구 사항에 맞게 사용자 지정할 수 있습니다. 솔루션 개발자는 웹 기반 도구를 사용하여 애플리케이션에 연결하는 디바이스를 위한 _디바이스 템플릿_을 만들 수 있습니다. 디바이스 템플릿은 다음과 같은 디바이스 유형의 특성과 동작을 정의하는 청사진입니다.

- 전송하는 원격 분석.
- 운영자가 수정할 수 있는 비즈니스 속성.
- 디바이스에서 설정하고 애플리케이션에서 읽기 전용인 디바이스 속성.
- 운영자가 설정하는 속성으로, 디바이스의 동작을 결정합니다.

이 디바이스 템플릿에는 다음 항목이 포함되어 있습니다.

- 디바이스에서 보내는 원격 분석 데이터 및 디바이스에서 보고하는 속성처럼 디바이스에서 구현해야 하는 기능을 설명하는 _디바이스 기능 모델_
- 디바이스에 저장되지 않는 클라우드 속성
- IoT Central 애플리케이션의 일부인 사용자 지정, 대시보드 및 양식

### <a name="create-device-templates"></a>디바이스 템플릿 만들기

[IoT 플러그 앤 플레이](../../iot-pnp/overview-iot-plug-and-play.md)를 사용하면 포함된 디바이스 코드를 작성하지 않고도 IoT Central에서 디바이스를 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심은 디바이스 기능을 설명하는 디바이스 기능 모델 스키마입니다. IoT Central 미리 보기 애플리케이션에서 디바이스 템플릿은 이러한 IoT 플러그 앤 플레이 디바이스 기능 모델을 사용합니다.

솔루션 개발자는 다음과 같은 여러 가지 옵션을 통해 디바이스 템플릿을 만들 수 있습니다.

- IoT Central에서 디바이스 템플릿을 디자인한 다음, 디바이스 코드에서 디바이스 기능 모델을 구현합니다.
- [IoT용 Azure Certified 디바이스 카탈로그](https://aka.ms/iotdevcat)에서 디바이스 기능 모델을 가져온 다음, IoT Central 애플리케이션에 필요한 클라우드 속성, 사용자 지정 및 대시보드를 추가합니다.
- Visual Studio Code를 사용하여 디바이스 기능 모델을 만듭니다. 모델에서 디바이스 코드를 구현하고, 디바이스를 IoT Central 애플리케이션에 연결합니다. IoT Central은 자동으로 리포지토리에서 디바이스 기능 모델을 찾아서 간단한 디바이스 템플릿을 만듭니다.
- Visual Studio Code를 사용하여 디바이스 기능 모델을 만듭니다. 모델에서 디바이스 코드를 구현합니다. 수동으로 디바이스 기능 모델을 IoT Central 애플리케이션으로 가져온 다음, IoT Central 애플리케이션에 필요한 클라우드 속성, 사용자 지정 및 대시보드를 추가합니다.

솔루션 개발자는 IoT Central을 사용하여 디바이스 템플릿의 유효성을 검사하는 테스트 디바이스용 코드를 생성할 수 있습니다.

### <a name="customize-the-ui"></a>UI 사용자 지정

또한 솔루션 개발자는 애플리케이션의 일상적인 사용을 담당하는 운영자를 위해 IoT Central 애플리케이션 UI를 사용자 지정할 수 있습니다. 솔루션 개발자가 수행할 수 있는 사용자 지정은 다음과 같습니다.

- 디바이스 템플릿의 속성 및 설정 레이아웃 정의.
- 운영자가 인사이트를 발견하고 문제를 더 빨리 해결할 수 있도록 사용자 지정 대시보드 구성.
- 연결된 디바이스에서 시계열 데이터를 탐색하는 사용자 지정 분석 구성.

## <a name="connect-your-devices"></a>사용자 디바이스 연결

개발자가 애플리케이션에 연결할 수 있는 디바이스 유형을 정의한 후 디바이스 개발자는 디바이스에서 실행할 코드를 만듭니다. 디바이스 개발자는 Microsoft의 오픈 소스를 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)를 사용하여 디바이스 코드를 만듭니다. 이 SDK는 광범위한 언어, 플랫폼 및 프로토콜 지원을 통해 IoT Central 애플리케이션에 디바이스를 연결하는 데 필요한 사항을 충족합니다. SDK를 사용하면 다음과 같은 디바이스 기능을 구현할 수 있습니다.

- 보안 연결 생성.
- 원격 분석 전송.
- 상태 보고.
- 구성 업데이트 수신.

자세한 내용은 [Azure IoT SDK 사용 시 이점과 그렇지 않은 경우 방지해야 하는 문제](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)라는 블로그 게시물을 참조하세요.

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge 디바이스

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)를 사용하여 만든 디바이스 뿐만 아니라 [Azure IoT Edge 디바이스](../../iot-edge/about-iot-edge.md)도 IoT Central 애플리케이션에 연결할 수 있습니다. Azure IoT Edge에서는 IoT Central에서 관리하는 IoT 디바이스에서 직접 클라우드 인텔리전스 및 사용자 지정 논리를 실행할 수 있습니다. IoT Edge 런타임을 사용하면 다음을 수행할 수 있습니다.

- 디바이스에 워크로드를 설치하고 업데이트합니다.
- 디바이스에서 Azure IoT Edge 보안 표준을 유지합니다.
- IoT Edge 모듈이 항상 실행되도록 합니다.
- 원격 모니터링을 위해 모듈 상태를 클라우드에 보고합니다.
- 다운스트림 리프 디바이스와 IoT Edge 디바이스 간, IoT Edge 디바이스의 모듈 간, IoT Edge 디바이스와 클라우드 간의 통신을 관리합니다.

자세한 내용은 [Azure IoT Edge 디바이스 및 IoT Central](./concepts-architecture.md#azure-iot-edge-devices)을 참조하세요.

## <a name="manage-your-application"></a>애플리케이션 관리

IoT Central 애플리케이션은 Microsoft에서 완벽하게 호스트하므로 애플리케이션 관리 부담이 줄어듭니다.

운영자는 IoT Central 애플리케이션을 사용하여 IoT Central 솔루션의 디바이스를 관리합니다. 운영자는 다음과 같은 작업을 수행합니다.

- 애플리케이션에 연결된 디바이스 모니터링.
- 디바이스 관련 문제 해결 및 수정.
- 새 디바이스 프로비저닝.

솔루션 개발자는 연결된 디바이스의 스트리밍 데이터에 적용되는 사용자 지정 규칙 및 작업을 정의할 수 있습니다. 운영자는 디바이스 수준에서 이러한 규칙을 사용하거나 사용하지 않도록 설정하여 애플리케이션 내에서 작업을 제어하고 자동화할 수 있습니다.

관리자는 [사용자 역할 및 권한](howto-administer.md)을 사용하여 애플리케이션에 대한 액세스를 관리할 수 있습니다.

## <a name="quotas"></a>할당량

각 Azure 구독에는 IoT 솔루션의 범위에 영향을 줄 수 있는 기본 할당량이 있습니다. 현재 IoT Central은 구독 시 배포할 수 있는 애플리케이션 수를 10개로 제한합니다. 이 제한을 늘려야 하는 경우 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="next-steps"></a>다음 단계

이제 IoT Central에 대한 개요를 살펴보았으니, 권장되는 다음 단계는 아래와 같습니다.

- 사용 가능한 [IoT 솔루션을 만들기 위한 Azure 기술 및 서비스](../../iot-fundamentals/iot-services-and-technologies.md)를 이해합니다.
- [Azure IoT Central UI](overview-iot-central-tour.md)를 숙지합니다.
- [Azure IoT Central 애플리케이션을 생성](quick-deploy-iot-central.md)하여 시작합니다.
- [IoT 플러그 앤 플레이](../../iot-pnp/overview-iot-plug-and-play.md)에 대한 자세한 정보
- [Azure IoT Edge 디바이스 템플릿을 만드는 방법](./tutorial-define-edge-device-type.md)에 대한 자세한 정보
