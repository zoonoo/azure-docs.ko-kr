---
title: Azure IoT Central이란? | Microsoft Docs
description: Azure IoT Central은 사용자 지정 IoT 솔루션을 구축하고 관리하는 데 사용할 수 있는 엔드투엔드 SaaS 솔루션입니다. 이 문서에서는 Azure IoT Central의 기능에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0675be988e7e9560560dd07338563c9fa22fcb4c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881652"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central(미리 보기 기능)이란?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central의 [IoT 플러그 앤 플레이](https://aka.ms/iot-pnp-docs) 기능은 현재 공개 미리 보기로 제공됩니다. IoT 플러그 앤 플레이 지원 IoT Central 애플리케이션을 프로덕션 워크로드에 사용하지 마세요. 프로덕션 환경에서는 일반적으로 사용 가능한 최신 애플리케이션 템플릿에서 만든 IoT Central 애플리케이션을 사용합니다.

Azure IoT Central은 실제 세계와 디지털 세계를 연결하는 제품을 쉽게 만들 수 있게 해주는 완전 관리형 IoT SaaS(Software-as-a-Service) 솔루션입니다. 다음을 통해 연결된 제품 비전을 실현할 수 있습니다.

- 고객에게 더 나은 제품과 경험을 제공할 수 있도록 연결된 디바이스에서 새로운 인사이트 유도.
- 조직을 위한 새로운 비즈니스 기회 창출.

Azure IoT Central이 일반 IoT 프로젝트와 다른 점:

- 관리 부담 경감
- 운영 비용 및 오버헤드 경감
- 애플리케이션을 쉽게 사용자 지정하는 동시에 다음과 같은 장점 활용
  - [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 및 [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)와 같은 업계 최고의 기술.
  - 엔드투엔드 암호화와 같은 엔터프라이즈급 보안 기능.

다음 비디오는 Azure IoT Central의 개요를 보여 줍니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

이 문서에서는 Azure IoT Central에 대해 개략적으로 설명합니다.

- 프로젝트와 관련된 일반적인 가상 사용자.
- 애플리케이션을 만드는 방법.
- 디바이스를 애플리케이션에 연결하는 방법
- 애플리케이션을 관리하는 방법.

## <a name="known-issues"></a>알려진 문제

> [!Note]
> 이러한 알려진 이슈는 IoT Central 미리 보기 애플리케이션에만 적용됩니다.

- 규칙이 모든 작업을 지원하지는 않습니다(이메일만 지원).
- 복합 형식의 경우 규칙, 분석 및 디바이스 그룹이 지원되지 않습니다.
- 연속 데이터 내보내기는 Avro 형식을 지원하지 않습니다(비호환).
- 시뮬레이션된 디바이스는 일부 복합 형식을 지원하지 않습니다.
- GeoJSON은 현재 지원되지 않습니다.
- 지도 타일은 현재 지원되지 않습니다.
- 작업은 복합 형식을 지원하지 않습니다.
- 배열 스키마 형식은 지원되지 않습니다.
- 애플리케이션 템플릿 내보내기 및 애플리케이션 복사는 지원되지 않습니다.
- C 디바이스 SDK와 Node.js 디바이스 및 서비스 SDK만 지원됩니다.
- 일부 지역에서만 사용할 수 있습니다.

## <a name="personas"></a>가상 사용자

Azure IoT Central 설명서는 Azure IoT Central 애플리케이션과 상호 작용하는 다음 4명의 일반적인 가상 사용자와 관련이 있습니다.

- _개발자_는 애플리케이션에 연결하는 디바이스 유형을 정의하고 운영자에 맞게 애플리케이션을 사용자 지정하는 역할을 담당합니다.
- _운영자_는 애플리케이션에 연결된 디바이스를 관리합니다.
- _관리자_는 애플리케이션 내 사용자 및 역할 관리와 같은 관리 작업을 담당합니다.
- _디바이스 개발자_는 애플리케이션에 연결된 디바이스에서 실행되는 코드를 만듭니다.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션 만들기

개발자는 Azure IoT Central을 사용하여 조직을 위한 사용자 지정 클라우드 호스팅 IoT 솔루션을 만들 수 있습니다. 사용자 지정 IoT 솔루션은 일반적으로 다음과 같이 구성됩니다.

- 디바이스에서 원격 분석을 수신하고 해당 디바이스를 관리할 수 있게 해주는 클라우드 기반 애플리케이션.
- 클라우드 기반 애플리케이션에 연결된 사용자 지정 코드를 실행하는 여러 디바이스.

새로운 Azure IoT Central 애플리케이션을 빠르게 배포한 다음, 브라우저에서 특정 요구 사항에 맞게 사용자 지정할 수 있습니다. 개발자는 웹 기반 도구를 사용하여 애플리케이션에 연결하는 디바이스를 위한 _디바이스 템플릿_을 만들 수 있습니다. 디바이스 템플릿은 다음과 같은 디바이스 유형의 특성과 동작을 정의하는 청사진입니다.

- 전송하는 원격 분석.
- 운영자가 수정할 수 있는 비즈니스 속성.
- 디바이스에서 설정하고 애플리케이션에서 읽기 전용인 디바이스 속성.
- 운영자가 설정하며 디바이스의 동작을 결정하는 속성입니다.

이 디바이스 템플릿에는 다음 항목이 포함되어 있습니다.

- 디바이스에서 보내는 원격 분석 데이터 및 디바이스에서 보고하는 속성처럼 디바이스에서 구현해야 하는 기능을 설명하는 _디바이스 기능 모델_
- 디바이스에 저장되지 않는 클라우드 속성
- IoT Central 애플리케이션의 일부인 사용자 지정, 대시보드 및 양식

### <a name="create-device-templates"></a>디바이스 템플릿 만들기

[IoT 플러그 앤 플레이](https://aka.ms/iot-pnp-docs)를 사용하면 포함된 디바이스 코드를 작성하지 않고도 IoT Central에서 디바이스를 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심은 디바이스 기능을 설명하는 디바이스 기능 모델 스키마입니다. IoT Central 미리 보기 애플리케이션에서 디바이스 템플릿은 이러한 IoT 플러그 앤 플레이 디바이스 기능 모델을 사용합니다.

개발자는 다음과 같은 여러 가지 옵션을 통해 디바이스 템플릿을 만들 수 있습니다.

- IoT Central에서 디바이스 템플릿을 디자인한 다음, 디바이스 코드에서 디바이스 기능 모델을 구현합니다.
- [IoT용 Azure Certified 디바이스 카탈로그](https://aka.ms/iotdevcat)에서 디바이스 기능 모델을 가져온 다음, IoT Central 애플리케이션에 필요한 클라우드 속성, 사용자 지정 및 대시보드를 추가합니다.
- Visual Studio Code를 사용하여 디바이스 기능 모델을 만듭니다. 모델에서 디바이스 코드를 구현하고, 디바이스를 IoT Central 애플리케이션에 연결합니다. IoT Central은 자동으로 리포지토리에서 디바이스 기능 모델을 찾아서 간단한 디바이스 템플릿을 만듭니다.
- Visual Studio Code를 사용하여 디바이스 기능 모델을 만듭니다. 모델에서 디바이스 코드를 구현합니다. 수동으로 디바이스 기능 모델을 IoT Central 애플리케이션으로 가져온 다음, IoT Central 애플리케이션에 필요한 클라우드 속성, 사용자 지정 및 대시보드를 추가합니다.

개발자는 IoT Central을 사용하여 디바이스 템플릿의 유효성을 검사하는 테스트 디바이스용 코드를 생성할 수 있습니다.

### <a name="customize-the-ui"></a>UI 사용자 지정

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

관리자는 [사용자 역할 및 권한](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)을 사용하여 애플리케이션에 대한 액세스를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central에 대한 개요를 살펴보았으니, 권장되는 다음 단계는 아래와 같습니다.

- [Azure IoT Central과 Azure IoT 솔루션 가속기](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)의 차이점을 이해합니다.
- [Azure IoT Central UI](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)를 숙지합니다.
- [Azure IoT Central 애플리케이션을 생성](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)하여 시작합니다.
- 다음과 같은 방법을 보여주는 일련의 자습서를 따릅니다.
  - [개발자가 디바이스 템플릿 생성](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [개발자가 솔루션 자동화를 위한 규칙 추가](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [운영자가 디바이스 모니터링](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [운영자가 솔루션에 디바이스 추가](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [IoT 플러그 앤 플레이](https://aka.ms/iot-pnp-docs)에 대한 자세한 정보
