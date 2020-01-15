---
title: Azure IoT Central이란? | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 Azure IoT Central의 기능에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: a1aa2f12f62a95ac14750c821079df2bac46e8ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434962"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Azure IoT Central이란?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central은 엔터프라이즈급 IoT 솔루션의 개발, 관리 및 유지 관리와 관련된 부담 및 비용을 줄이는 IoT 앱 플랫폼입니다. Azure IoT Central을 사용하여 빌드하기로 선택하면 복잡하고 지속적으로 진화하는 IoT 인프라를 단순히 유지 관리하는 데서 그치지 않고 IoT 데이터를 활용하여 비즈니스를 전환하는 데 시간, 비용 및 에너지를 집중할 수 있습니다.

사용하기 쉬운 이 인터페이스를 통해 간편하게 디바이스 상태를 모니터링하고, 규칙을 만들고, 전체 수명 주기 동안 수백만 대의 디바이스와 해당 데이터를 관리할 수 있습니다. 뿐만 아니라 IoT 인텔리전스를 LOB(기간 업무) 애플리케이션으로 확장하여 디바이스 인사이트를 기반으로 조치를 취할 수 있습니다.

이 문서에서는 Azure IoT Central에 대해 개략적으로 설명합니다.

- 프로젝트와 관련된 일반적인 가상 사용자.
- 애플리케이션을 만드는 방법.
- 디바이스를 애플리케이션에 연결하는 방법
- 애플리케이션을 관리하는 방법.

## <a name="personas"></a>가상 사용자

Azure IoT Central 설명서는 Azure IoT Central 애플리케이션과 상호 작용하는 다음 4명의 일반적인 가상 사용자와 관련이 있습니다.

- _개발자_는 애플리케이션에 연결하는 디바이스 유형을 정의하고 운영자에 맞게 애플리케이션을 사용자 지정하는 역할을 담당합니다.
- _운영자_는 애플리케이션에 연결된 디바이스를 관리합니다.
- _관리자_는 애플리케이션 내 [사용자 역할 및 권한](howto-administer.md) 관리와 같은 작업을 담당합니다.
- _디바이스 개발자_는 애플리케이션에 연결된 디바이스에서 실행되는 코드를 만듭니다.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션 만들기

개발자는 Azure IoT Central을 사용하여 조직을 위한 사용자 지정 클라우드 호스팅 IoT 솔루션을 만들 수 있습니다. 사용자 지정 IoT 솔루션은 일반적으로 다음과 같이 구성됩니다.

- 디바이스에서 원격 분석을 수신하고 해당 디바이스를 관리할 수 있게 해주는 클라우드 기반 애플리케이션.
- 클라우드 기반 애플리케이션에 연결된 사용자 지정 코드를 실행하는 여러 디바이스.

새로운 Azure IoT Central 애플리케이션을 빠르게 배포한 다음, 브라우저에서 특정 요구 사항에 맞게 사용자 지정할 수 있습니다. 개발자는 웹 기반 도구를 사용하여 애플리케이션에 연결하는 디바이스를 위한 _디바이스 템플릿_을 만들 수 있습니다. 디바이스 템플릿은 다음과 같은 디바이스 유형의 특성과 동작을 정의하는 청사진입니다.

- 전송하는 원격 분석.
- 운영자가 수정할 수 있는 비즈니스 속성.
- 디바이스에서 설정하고 애플리케이션에서 읽기 전용인 디바이스 속성.
- 애플리케이션이 응답하는 임계값.
- 디바이스의 동작을 결정하는 설정.

Azure IoT Central에서 생성하는 시뮬레이션 데이터를 사용하여 디바이스 템플릿과 애플리케이션을 즉시 테스트할 수 있습니다.

또한 개발자는 애플리케이션의 일상적인 사용을 담당하는 운영자를 위해 Azure IoT Central 애플리케이션 UI를 사용자 지정할 수 있습니다. 개발자가 수행할 수 있는 사용자 지정은 다음과 같습니다.

- 디바이스 템플릿의 속성 및 설정 레이아웃 정의.
- 운영자가 인사이트를 발견하고 문제를 더 빨리 해결할 수 있도록 사용자 지정 대시보드 구성.
- 연결된 디바이스에서 시계열 데이터를 탐색하는 사용자 지정 분석 구성.

## <a name="connect-your-devices"></a>사용자 디바이스 연결

개발자가 애플리케이션에 연결할 수 있는 디바이스 유형을 정의한 후 디바이스 개발자는 디바이스에서 실행할 코드를 만듭니다. 디바이스 개발자는 Microsoft의 오픈 소스를 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)를 사용하여 디바이스 코드를 만듭니다. 이 SDK는 광범위한 언어, 플랫폼 및 프로토콜 지원을 통해 Azure IoT Central 애플리케이션에 디바이스를 연결하는 데 필요한 사항을 충족합니다. SDK를 사용하면 다음과 같은 디바이스 기능을 구현할 수 있습니다.

- 보안 연결 생성.
- 원격 분석 전송.
- 상태 보고.
- 구성 업데이트 수신.

자세한 내용은 [Azure IoT SDK 사용 시 이점과 그렇지 않은 경우 방지해야 하는 문제](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)라는 블로그 게시물을 참조하세요.

## <a name="manage-your-application"></a>애플리케이션 관리

Azure IoT Central 애플리케이션은 Microsoft에서 완벽하게 호스트하므로 애플리케이션 관리 부담이 줄어듭니다.

운영자는 Azure IoT Central 애플리케이션을 사용하여 Azure IoT Central 솔루션의 디바이스를 관리합니다. 운영자는 다음과 같은 작업을 수행합니다.

- 애플리케이션에 연결된 디바이스 모니터링.
- 디바이스 관련 문제 해결 및 수정.
- 새 디바이스 프로비저닝.

개발자는 연결된 디바이스의 스트리밍 데이터에 적용되는 사용자 지정 규칙 및 작업을 정의할 수 있습니다. 운영자는 디바이스 수준에서 이러한 규칙을 사용하거나 사용하지 않도록 설정하여 애플리케이션 내에서 작업을 제어하고 자동화할 수 있습니다.

관리자는 [사용자 역할 및 권한](howto-administer.md)을 사용하여 애플리케이션에 대한 액세스를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central에 대한 개요를 살펴보았으니, 권장되는 다음 단계는 아래와 같습니다.

- 사용 가능한 [IoT 솔루션을 만들기 위한 Azure 기술 및 서비스](../../iot-fundamentals/iot-services-and-technologies.md)를 이해합니다.
- [Azure IoT Central UI](overview-iot-central-tour.md)를 숙지합니다.
- [Azure IoT Central 애플리케이션을 생성](quick-deploy-iot-central.md)하여 시작합니다.
- 다음과 같은 방법을 보여주는 일련의 자습서를 따릅니다.
  - [개발자가 디바이스 템플릿 생성](tutorial-define-device-type.md)
  - [개발자가 솔루션 자동화를 위한 규칙 추가](tutorial-configure-rules.md)
  - [개발자가 운영자에 맞게 애플리케이션 사용자 지정](tutorial-customize-operator.md)
  - [운영자가 디바이스 모니터링](tutorial-monitor-devices.md)
  - [운영자가 솔루션에 실제 디바이스 추가](tutorial-add-device.md)
  - [디바이스 개발자가 디바이스용 코드 생성](tutorial-add-device.md#prepare-the-client-code)
