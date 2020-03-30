---
title: 용어집 - IoT 플러그 앤 플레이 미리보기 | 마이크로 소프트 문서
description: 개념 - IoT 플러그 앤 플레이 미리 보기와 관련된 일반적인 용어의 용어집입니다.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025642"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이 미리 보기 용어집

IoT 플러그 앤 플레이 문서에서 사용되는 일반적인 용어의 정의입니다.

## <a name="azure-certified-for-iot-portal"></a>IoT 포털용 Azure 인증

[IoT 포털](https://aka.ms/ACFI) 웹 사이트에 대한 Azure 인증 웹 사이트를 사용하여 다음을 수행할 수 있습니다.

- [IoT 플러그 앤 플레이 장치에](#iot-plug-and-play-device)대한 인증 [프로세스를](#device-certification) 완료합니다.
- [장치 기능 모델](#device-capability-model)찾기 .
- [공용 모델 리포지토리에](#public-model-repository)장치 기능 모델을 게시합니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 Azure 리소스를 관리하기 위한 크로스 플랫폼 명령줄 도구입니다. Azure CLI에 대한 Azure IoT 확장은 [IoT 플러그 앤 플레이 장치와](#iot-plug-and-play-device)상호 작용하고 테스트하기 위한 명령줄 도구입니다. 확장을 사용하여 다음을 수행할 수 있습니다.

- IoT 플러그 앤 플레이 장치에 연결합니다.
- 장치가 보내는 [원격 분석을](#telemetry) 봅니다.
- 장치 [속성으로](#properties)작업합니다.
- 장치 [명령을 호출합니다.](#commands)
- [모델 리포지토리,](#model-repository)인터페이스 및 [장치 기능 모델을](#device-capability-model) [관리합니다.](#interface)

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central은 [IoT 플러그 앤 플레이 장치를](#iot-plug-and-play-device)쉽게 연결, 모니터링 및 관리할 수 있는 서비스형 소프트웨어 솔루션입니다. [장치 기능 모델을](#device-capability-model) 사용하여 IoT Central 응용 프로그램을 자동으로 구성하여 장치를 모니터링하고 관리할 수 있습니다.

## <a name="azure-iot-certification-service"></a>Azure IoT 인증 서비스

Azure IoT 인증 서비스는 IoT 포털에 대한 Azure 인증 포털을 통해 인증을 위한 [IoT 플러그 앤 플레이 장치를](#iot-plug-and-play-device) 제출할 때 [인증](#azure-certified-for-iot-portal)테스트 집합을 실행합니다. [IoT 에 대한 인증 된 장치 카탈로그에](#certified-for-iot-device-catalog)장치를 추가하려면 먼저 장치가 인증되어야합니다.

## <a name="azure-iot-tools-extension"></a>Azure IoT 도구 확장

Azure IoT Tools는 IoT Hub와 상호 작용하고 IoT 장치를 개발하는 데 도움이 되는 [Visual Studio 코드의](#visual-studio-code) 확장 모음입니다. IoT 플러그 앤 플레이 장치 개발의 경우 다음을 수행할 수 있습니다.

- [장치 기능 모델](#device-capability-model) 및 [인터페이스를 작성합니다.](#interface)
- 모델 [리포지토리에](#model-repository)게시합니다.
- 스켈레톤 코드를 생성하여 장치 응용 프로그램을 구현합니다.

## <a name="azure-iot-explorer-tool"></a>Azure IoT 탐색기 도구

Azure IoT 탐색기는 [IoT 플러그 앤 플레이 장치와](#iot-plug-and-play-device)상호 작용하고 테스트하는 데 사용할 수 있는 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후 다음을 수행할 수 있습니다.

- [IoT 허브에](#azure-iot-hub)연결된 장치를 봅니다.
- IoT 플러그 앤 플레이 장치에 연결합니다.
- 장치가 보내는 [원격 분석을](#telemetry) 봅니다.
- 장치 [속성으로](#properties)작업합니다.
- 장치 [명령을 호출합니다.](#commands)

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub는 클라우드에서 호스팅되는 관리 서비스이며, IoT 애플리케이션과 이를 통해 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. [IoT 플러그 앤 플레이 장치는 IoT](#iot-plug-and-play-device) 허브에 연결할 수 있습니다. IoT 솔루션은 IoT 허브를 사용하여 다음을 지원합니다.

- 원격 분석을 클라우드 기반 솔루션으로 보내는 장치입니다.
- 연결된 장치를 관리하는 클라우드 기반 솔루션입니다.

## <a name="azure-iot-device-sdk"></a>Azure IoT 디바이스 SDK

IoT 플러그 앤 플레이 장치 클라이언트 응용 프로그램을 빌드하는 데 사용할 수 있는 여러 언어용 장치 SDK가 있습니다. [장치 인증에](#device-certification) 대한 요구 사항 중 하나는 장치 클라이언트 코드가 Azure IoT 장치 SDK 중 하나를 사용한다는 것입니다.

## <a name="certified-for-iot-device-catalog"></a>IoT 장치 카탈로그 인증

[IoT 장치 인증 카탈로그에는](https://catalog.azureiotsolutions.com/) [장치 인증](#device-certification) 테스트를 통과한 [IoT 플러그 앤 플레이 장치가](#iot-plug-and-play-device) 나열되어 있습니다. 카탈로그의 IoT 플러그 앤 플레이 디바이스에 대한 [장치 기능 모델이며](#device-capability-model) 공용 모델 리포지토리에 게시됩니다.

## <a name="commands"></a>명령

[인터페이스에](#interface) 정의된 명령은 [디지털 트윈에서](#digital-twin)실행할 수 있는 메서드를 나타냅니다. 예를 들어 장치를 재부팅하는 명령입니다.

## <a name="common-interface"></a>공통 인터페이스

모든 [IoT 플러그 앤 플레이 장치는](#iot-plug-and-play-device) 몇 가지 일반적인 인터페이스를 구현해야 [합니다.](#interface) 예를 들어 장치 정보 인터페이스는 장치에 대한 하드웨어 및 운영 체제 정보를 정의합니다. [장치 인증을](#device-certification) 요구하므로 장치에서 몇 가지 공통 인터페이스를 구현해야 합니다. 공용 모델 리포지토리에서 공통 인터페이스 정의를 검색할 수 있습니다.

## <a name="company-model-repository"></a>회사 모델 리포지토리

조직은 회사 모델 [리포지토리를](#model-repository) 장치 기능 [모델](#device-capability-model) 및 [인터페이스에](#interface)대한 개인 저장소로 사용할 수 있습니다.

## <a name="connection-string"></a>연결 문자열

연결 문자열은 끝점에 연결하는 데 필요한 정보를 캡슐화합니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.

- 장치 연결 문자열을 사용하면 [IoT 플러그 앤 플레이 디바이스가 IoT](#iot-plug-and-play-device) 허브의 장치 연결 끝점에 연결할 수 있습니다. 장치의 클라이언트 코드는 연결 문자열을 사용하여 IoT 허브와 보안 연결을 설정합니다.
- IoT Hub 연결 문자열을 사용하면 백 엔드 솔루션 및 도구를 IoT 허브의 서비스 연결 엔드포인트에 안전하게 연결할 수 있습니다. 이러한 솔루션 및 도구는 IoT 허브와 연결된 장치를 관리합니다.
- 회사 모델 리포지토리 연결 문자열을 사용하면 백 엔드 솔루션 및 도구를 [사용하여 회사 모델 리포지토리에](#company-model-repository)안전하게 연결할 수 있습니다. 이러한 솔루션 및 도구는 리포지토리에서 [장치 기능 모델](#device-capability-model) 및 인터페이스를 사용하거나 [관리합니다.](#interface)

## <a name="device-capability-model"></a>디바이스 기능 모델

장치 기능 모델은 [IoT 플러그 앤 플레이 장치를](#iot-plug-and-play-device) 설명하고 장치에서 구현한 인터페이스 [집합을 정의합니다.](#interface) 장치 기능 모델은 일반적으로 물리적 장치, 제품 또는 SKU에 해당합니다. [디지털 트윈 정의 언어를](#digital-twin-definition-language) 사용하여 장치 기능 모델을 정의합니다.

## <a name="device-certification"></a>장치 인증

장치 인증은 [IoT 플러그 앤 플레이 장치를](#iot-plug-and-play-device) 인증하여 [IoT 장치 카탈로그](#certified-for-iot-device-catalog) 및 해당 장치 기능 모델 및 공용 모델 [리포지토리에](#public-model-repository)추가된 [인터페이스에](#device-capability-model) 추가할 수 있도록 인증하는 [프로세스입니다.](#interface)

## <a name="device-developer"></a>디바이스 개발자

장치 개발자는 [장치 기능 모델,](#device-capability-model) [인터페이스](#interface)및 [Azure IoT 장치 SDK를](#azure-iot-device-sdk) 사용하여 [IoT 플러그 앤 플레이 장치에서](#iot-plug-and-play-device)실행되는 코드를 구현합니다.

## <a name="device-modeling"></a>장치 모델링

[장치 개발자는](#device-developer) [디지털 트윈 정의 언어를](#digital-twin-definition-language) 사용하여 [IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)기능을 모델링합니다. 모델 리포지토리를 사용하여 모델을 공유할 수 있습니다. 장치 개발자는 모델에서 스켈레톤 장치 코드를 생성할 수 있습니다. [솔루션 개발자는](#solution-developer) 모델에서 IoT 솔루션을 구성할 수 있습니다.

## <a name="device-provisioning-service"></a>Device Provisioning Service

[Azure IoT Central은](#azure-iot-central) 장치 프로비저닝 서비스를 사용하여 모든 장치 등록 및 연결을 관리합니다. 자세한 내용은 [Azure IoT Central의 장치 연결을](../iot-central/core/concepts-get-connected.md)참조하십시오. 장치 프로비저닝 서비스를 사용하여 IoT Hub 기반 IoT 솔루션에 대한 장치 등록 및 연결을 관리할 수도 있습니다. 자세한 내용은 [Azure IoT Hub 장치 프로비전 서비스를 사용 하 여 프로비저닝 장치를](../iot-dps/about-iot-dps.md)참조 하십시오.

## <a name="device-registration"></a>디바이스 등록

[IoT 플러그 앤 플레이 장치가 IoT](#iot-plug-and-play-device) 솔루션에 연결하려면 먼저 솔루션에 등록해야 합니다. [Azure IoT Central은](#azure-iot-central) [장치 프로비저닝 서비스를](#device-provisioning-service) 사용하여 장치 등록을 관리합니다. 사용자 지정 IoT 솔루션에서 Azure 포털또는 프로그래밍 방식으로 IoT 허브에 장치를 등록할 수 있습니다.

## <a name="device-first"></a>장치 우선

[Azure IoT Central은](#azure-iot-central) 장치 우선 등록 및 연결 시나리오를 지원합니다. 이 시나리오에서는 [IoT 플러그 앤 플레이 장치를](#iot-plug-and-play-device) 미리 등록 하지 않고 IoT 중앙 응용 프로그램에 연결할 수 있습니다. 장치가 응용 프로그램에 처음 연결될 때 등록이 자동으로 수행됩니다.

## <a name="digital-twin"></a>디지털 트윈

디지털 트윈은 [IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)모델입니다. 디지털 트윈은 [디지털 트윈 정의 언어를](#digital-twin-definition-language)사용하여 모델링됩니다. [Azure IoT 장치 SDK를](#azure-iot-device-sdk) 사용하여 런타임에 디지털 트윈과 상호 작용할 수 있습니다. 예를 들어 장치에서 디지털 트윈에 속성 값을 설정할 수 있으며 SDK는 이러한 변경 사항을 클라우드의 IoT 솔루션에 전달합니다.

## <a name="digital-twin-change-events"></a>디지털 트윈 체인지 이벤트

[IoT 플러그 앤 플레이 장치가 IoT](#iot-plug-and-play-device) [허브에](#azure-iot-hub)연결되어 있으면 허브에서 라우팅 기능을 사용하여 디지털 트윈 변경 알림을 보낼 수 있습니다. 예를 들어 장치에서 [속성](#properties) 값이 변경될 때마다 IoT Hub는 서비스 버스 큐와 같은 끝점으로 알림을 보낼 수 있습니다.

## <a name="digital-twin-definition-language"></a>디지털 트윈 정의 언어

[IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)모델 및 인터페이스를 설명하기 위한 언어입니다. 디지털 [트윈 정의 언어를](https://aka.ms/DTDL) 사용하여 [디지털 트윈의](#digital-twin) 기능을 설명하고 IoT 플랫폼 및 IoT 솔루션이 엔터티의 의미체계를 활용할 수 있도록 합니다.

## <a name="digital-twin-route"></a>디지털 트윈 루트

서비스 버스 큐와 같은 [끝점에 디지털 트윈 변경 이벤트를](#digital-twin-change-events) 전달하기 위해 [IoT 허브에](#azure-iot-hub) 설정된 경로입니다.

## <a name="interface"></a>인터페이스

인터페이스는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 또는 디지털 [트윈에](#digital-twin)의해 구현되는 관련 기능을 설명합니다. 다양한 [장치 기능 모델에서](#device-capability-model)인터페이스를 재사용할 수 있습니다.

## <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

IoT Hub 쿼리 언어는 여러 용도로 사용됩니다. 예를 들어 이 언어를 사용하여 IoT 허브에 [등록된 장치를](#device-registration) 검색하거나 [디지털 트윈 라우팅](#digital-twin-route) 동작을 구체화할 수 있습니다.

## <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 장치

IoT 플러그 앤 플레이 장치는 일반적으로 데이터를 수집하거나 다른 장치를 제어하는 소규모 독립 실행형 컴퓨팅 장치이며 [장치 기능 모델을](#device-capability-model)구현하는 소프트웨어 또는 펌웨어를 실행합니다.  예를 들어 IoT 플러그 앤 플레이 장치는 환경 모니터링 장치이거나 스마트 농업 관개 시스템을 위한 컨트롤러일 수 있습니다. 클라우드 호스팅 IoT 솔루션을 작성하여 IoT 플러그 앤 플레이 장치에서 데이터를 명령, 제어 및 수신할 수 있습니다. [IoT 장치 카탈로그에 대한 Azure 인증](#certified-for-iot-device-catalog) 에는 사용 가능한 IoT 플러그 앤 플레이 장치가 나열되어 있습니다. 카탈로그의 각 IoT 플러그 앤 플레이 장치는 유효성을 검사받았으며 [장치 기능 모델이 있습니다.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Microsoft 파트너 센터

[Microsoft 파트너 센터는](https://docs.microsoft.com/partner-center/) 조직에서 Microsoft와의 종단 간 관계를 관리하는 곳입니다. IoT 포털에 [대한 Azure 인증](#azure-certified-for-iot-portal)장치에서 [IoT 플러그 앤 플레이 장치를](#iot-plug-and-play-device) 인증하려면 Microsoft 파트너 센터 계정이 필요합니다.

## <a name="model-discovery"></a>모델 검색

[IoT 플러그 앤 플레이 장치가 IoT](#iot-plug-and-play-device) 솔루션에 연결되면 솔루션은 [장치 기능 모델을](#device-capability-model)찾아 장치의 기능을 검색할 수 있습니다. 장치는 해당 기능 모델을 솔루션으로 보내거나 솔루션에서 [모델 리포지토리에서](#model-repository)장치 기능 모델을 찾을 수 있습니다.

## <a name="model-repository"></a>모델 리포지토리

모델 리포지토리는 [장치 기능 모델](#device-capability-model) 및 [인터페이스를 저장합니다.](#interface) 단일 [공용 모델 리포지토리가](#public-model-repository)있습니다. 조직은 자체 조직 모델 리포지토리를 만들 수 있습니다.

## <a name="model-repository-rest-api"></a>리포지토리 REST API 모델

모델 리포지토리를 관리하고 상호 작용하기 위한 API입니다. 예를 들어 API를 사용하여 [장치 기능 모델을](#device-capability-model) 추가하고 기능 모델을 검색할 수 있습니다.

## <a name="properties"></a>속성

속성은 디지털 트윈의 일부 상태를 나타내는 [인터페이스에](#interface) 정의된 데이터 필드입니다. 속성을 읽기 전용 또는 쓰기 용으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 자체에서 실행되는 코드에 의해 설정됩니다.  경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT 솔루션에서 설정됩니다.

## <a name="public-model-repository"></a>공용 모델 리포지토리

[인증된](#device-certification)장치에 대한 [장치 기능 모델](#device-capability-model) 및 인터페이스를 저장하는 단일 공용 모델 리포지토리가 [있습니다.](#interface) 공용 모델 리포지토리는 [공통 인터페이스](#common-interface) 정의도 저장합니다.

## <a name="registration-id"></a>등록 ID

등록 ID는 [장치 프로비저닝 서비스에서](#device-provisioning-service)장치를 고유하게 식별합니다. 이 ID는 [IoT 허브의](#azure-iot-hub)장치에 대한 고유 식별자인 장치 ID와 동일하지 않습니다.

## <a name="scope-id"></a>범위 ID

Scope ID 범위는 [장치 프로비저닝 서비스](#device-provisioning-service) 인스턴스를 고유하게 식별합니다.

## <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. 공유 액세스 서명 인증에는 공유 액세스 정책과 공유 액세스 서명(토큰이라고도 함)의 두 가지 구성 요소가 있습니다. [IoT 플러그 앤 플레이 장치는](#iot-plug-and-play-device) 공유 액세스 서명을 사용하여 [IoT 허브로](#azure-iot-hub)인증합니다.

## <a name="solution-developer"></a>솔루션 개발자

솔루션 개발자는 솔루션 백 엔드를 만듭니다. 솔루션 개발자는 일반적으로 [IoT Hub](#azure-iot-hub) 및 [모델 리포지토리와](#model-repository)같은 Azure 리소스와 함께 작동하거나 [IoT Central.](#azure-iot-central)

## <a name="telemetry"></a>원격 분석

[인터페이스에](#interface) 정의된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정값은 일반적으로 [IoT 플러그 앤 플레이 장치에서](#iot-plug-and-play-device) 데이터 스트림으로 전송되는 센서 판독값과 같은 값입니다.

## <a name="visual-studio-code"></a>비주얼 스튜디오 코드

Visual Studio 코드는 여러 플랫폼에서 사용할 수 있는 최신 코드 편집기입니다. [Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 팩의 확장과 같은 확장을 사용하면 편집기를 사용자 지정하여 광범위한 개발 시나리오를 지원할 수 있습니다.
