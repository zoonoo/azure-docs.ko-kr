---
title: 용어 설명 - IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: 개념 - IoT 플러그 앤 플레이 미리 보기와 관련된 일반적인 용어 설명입니다.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767075"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이 미리 보기용 용어 설명

IoT 플러그 앤 플레이 문서에서 사용되는 일반적인 용어의 정의입니다.

## <a name="azure-certified-for-iot-portal"></a>IoT용 Azure Certified 포털

[IoT용 Azure Certified 포털](https://aka.ms/ACFI) 웹 사이트를 사용하여 다음 작업을 수행할 수 있습니다.

- [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에 대한 [인증 프로세스](#device-certification)를 완료합니다.
- [디바이스 기능 모델](#device-capability-model)을 찾습니다.
- [퍼블릭 모델 리포지토리](#public-model-repository)에 디바이스 기능 모델을 게시합니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 Azure 리소스를 관리하기 위한 플랫폼 간 명령줄 도구입니다. Azure CLI용 Azure IoT 확장은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)와 상호 작용하고 테스트하기 위한 명령줄 도구입니다. 이 확장을 사용하여 다음을 수행할 수 있습니다.

- IoT 플러그 앤 플레이 디바이스에 연결합니다.
- 디바이스가 전송하는 [원격 분석](#telemetry)을 봅니다.
- 디바이스 [속성](#properties)을 사용합니다.
- 디바이스 [명령](#commands)을 호출합니다.
- [모델 리포지토리](#model-repository), [인터페이스](#interface) 및 [디바이스 기능 모델](#device-capability-model)을 관리합니다.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 쉽게 연결, 모니터링 및 관리할 수 있는 완전 관리형 Software-as-a-Service 솔루션입니다. [디바이스 기능 모델](#device-capability-model)을 사용하여 IoT Central 애플리케이션에서 디바이스를 모니터링하고 관리할 수 있도록 자동으로 구성할 수 있습니다.

## <a name="azure-iot-certification-service"></a>Azure IoT 인증 서비스

Azure IoT 인증 서비스는 [IoT용 Azure Certified 포털](#azure-certified-for-iot-portal)을 통해 인증을 위해 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 제출할 때 일련의 인증 테스트를 실행합니다. 디바이스를 [IoT용 Certified 디바이스 카탈로그](#certified-for-iot-device-catalog)에 추가하려면 먼저 디바이스를 인증해야 합니다.

## <a name="azure-iot-tools-extension"></a>Azure IoT Tools 확장

Azure IoT Tools는 IoT Hub와 상호 작용하고 IoT 디바이스를 개발하는 데 유용한 [Visual Studio Code](#visual-studio-code) 확장 모음입니다. IoT 플러그 앤 플레이 디바이스 개발의 경우 다음 작업을 수행하는 데 도움이 됩니다.

- [디바이스 기능 모델](#device-capability-model) 및 [인터페이스](#interface)를 작성합니다.
- [모델 리포지토리](#model-repository)에 게시합니다.
- 디바이스 애플리케이션을 구현하는 기본 코드를 생성합니다.

## <a name="azure-iot-explorer-tool"></a>Azure IoT Explorer 도구

Azure IoT Explorer는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)와 상호 작용하고 이러한 디바이스를 테스트하는 데 사용할 수 있는 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후에 이 도구를 사용하여 다음 작업을 수행할 수 있습니다.

- [IoT Hub](#azure-iot-hub)에 연결된 디바이스를 확인합니다.
- IoT 플러그 앤 플레이 디바이스에 연결합니다.
- 디바이스가 전송하는 [원격 분석](#telemetry)을 봅니다.
- 디바이스 [속성](#properties)을 사용합니다.
- 디바이스 [명령](#commands)을 호출합니다.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub는 클라우드에서 호스팅되는 관리 서비스이며, IoT 애플리케이션과 이를 통해 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. [Iot 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 IoT Hub에 연결할 수 있습니다. IoT 솔루션은 IoT Hub를 사용하여 다음을 지원합니다.

- 디바이스에서 클라우드 기반 솔루션에 원격 분석을 전송할 수 있도록 합니다.
- 클라우드 기반 솔루션에서 연결된 디바이스를 관리할 수 있도록 합니다.

## <a name="azure-iot-device-sdk"></a>Azure IoT 디바이스 SDK

IoT 플러그 앤 플레이 디바이스 클라이언트 애플리케이션을 빌드하는 데 사용할 수 있는 여러 언어에 대한 디바이스 SDK가 있습니다. [디바이스 인증](#device-certification)에 대한 요구 사항 중 하나는 디바이스 클라이언트 코드에서 Azure IoT 디바이스 SDK 중 하나를 사용하는 것입니다.

## <a name="certified-for-iot-device-catalog"></a>IoT용 Certified 디바이스 카탈로그

[IoT용 Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)는 [디바이스 인증](#device-certification) 테스트를 통과한 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 나열합니다. 카탈로그의 IoT 플러그 앤 플레이 디바이스에 대한 [디바이스 기능 모델](#device-capability-model)이며 퍼블릭 모델 리포지토리에 게시됩니다.

## <a name="commands"></a>명령

[인터페이스](#interface)에 정의된 명령은 [디지털 쌍](#digital-twin)에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어, 디바이스를 재부팅하는 명령이 여기에 해당합니다.

## <a name="common-interface"></a>일반 인터페이스

모든 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)는 일반적인 일부 [인터페이스](#interface)를 구현해야 합니다. 예를 들어, 디바이스 정보 인터페이스는 디바이스에 대한 하드웨어 및 운영 체제 정보를 정의합니다. [디바이스 인증](#device-certification)을 사용하려면 디바이스에서 몇 가지 공통 인터페이스를 구현해야 합니다. 퍼블릭 모델 리포지토리에서 일반적인 인터페이스 정의를 검색할 수 있습니다.

## <a name="company-model-repository"></a>회사 모델 리포지토리

조직에서는 [디바이스 기능 모델](#device-capability-model) 및 [인터페이스](#interface)에 대한 프라이빗 저장소로 회사 [모델 리포지토리](#model-repository)를 사용할 수 있습니다.

## <a name="connection-string"></a>연결 문자열

연결 문자열은 엔드포인트에 연결하는 데 필요한 정보를 캡슐화합니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.

- 디바이스 연결 문자열은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 IoT hub의 디바이스 연결 엔드포인트에 연결할 수 있도록 합니다. 디바이스의 클라이언트 코드는 연결 문자열을 사용하여 IoT Hub와의 보안 연결을 설정합니다.
- IoT Hub 연결 문자열은 백 엔드 솔루션 및 도구에서 IoT Hub의 서비스 연결 엔드포인트에 안전하게 연결할 수 있도록 합니다. 이러한 솔루션 및 도구는 IoT Hub 및 연결된 디바이스를 관리합니다.
- 회사 모델 리포지토리 연결 문자열은 백 엔드 솔루션 및 도구에서 [회사 모델 리포지토리](#company-model-repository)에 안전하게 연결할 수 있도록 합니다. 이러한 솔루션 및 도구는 리포지토리의[디바이스 기능 모델](#device-capability-model) 및 [인터페이스](#interface)를 사용하거나 관리합니다.

## <a name="device-capability-model"></a>디바이스 기능 모델

디바이스 기능 모델은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 설명하고 디바이스에서 구현된 [인터페이스](#interface) 세트를 정의합니다. 디바이스 기능 모델은 일반적으로 물리적 디바이스, 제품 또는 SKU에 해당합니다. [디지털 쌍 정의 언어](#digital-twin-definition-language)를 사용하여 디바이스 기능 모델을 정의합니다.

## <a name="device-certification"></a>디바이스 인증

디바이스 인증은 [IoT용 Certified 디바이스 카탈로그](#certified-for-iot-device-catalog)에 추가될 수 있고 [디바이스 기능 모델](#device-capability-model) 및 [인터페이스](#interface)를 [퍼블릭 모델 리포지토리](#public-model-repository)에 추가될 수 있도록 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 인증하는 프로세스입니다.

## <a name="device-developer"></a>디바이스 개발자

디바이스 개발자는 [디바이스 기능 모델](#device-capability-model), [인터페이스](#interface) 및 [Azure IoT 디바이스 SDK](#azure-iot-device-sdk)를 사용하여 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 실행되는 코드를 구현합니다.

## <a name="device-modeling"></a>디바이스 모델링

[디바이스 개발자](#device-developer)는 [디지털 쌍 정의 언어](#digital-twin-definition-language)를 사용하여 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)의 기능을 모델링합니다. 모델 리포지토리를 사용하여 모델을 공유할 수 있습니다. 디바이스 개발자는 모델에서 기본 디바이스 코드를 생성할 수 있습니다. [솔루션 개발자](#solution-developer)는 모델에서 IoT 솔루션을 구성할 수 있습니다.

## <a name="device-provisioning-service"></a>Device Provisioning Service

[Azure IoT Central](#azure-iot-central)은 Device Provisioning Service를 사용하여 모든 디바이스 등록 및 연결을 관리합니다. 자세한 내용은 [Azure IoT Central의 디바이스 연결](../iot-central/core/concepts-get-connected.md)을 참조하세요. Device Provisioning Service를 사용하여 IoT Hub 기반 IoT 솔루션에 대한 디바이스 등록 및 연결을 관리할 수도 있습니다. 자세한 내용은 [Azure IoT Hub Device Provisioning Service로 디바이스 프로비전](../iot-dps/about-iot-dps.md)을 참조하세요.

## <a name="device-registration"></a>디바이스 등록

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 IoT 솔루션에 연결할 수 있으려면 먼저 솔루션에 등록해야 합니다. [Azure IoT Central](#azure-iot-central)은 [Device Provisioning Service](#device-provisioning-service)를 사용하여 디바이스 등록을 관리합니다. 사용자 지정 IoT 솔루션에서는 Azure Portal 또는 프로그래밍 방식으로 IoT Hub에 디바이스를 등록할 수 있습니다.

## <a name="device-first"></a>디바이스-우선

[Azure IoT Central](#azure-iot-central)은 디바이스 우선 등록 및 연결 시나리오를 지원합니다. 이 시나리오에서는 미리 등록하지 않고 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 IoT Central 애플리케이션에 연결할 수 있습니다. 디바이스를 애플리케이션에 처음 연결할 때 등록이 자동으로 수행됩니다.

## <a name="digital-twin"></a>디지털 쌍

디지털 쌍은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)의 모델입니다. 디지털 쌍은 [디지털 쌍 정의 언어](#digital-twin-definition-language)를 사용하여 모델링됩니다. [Azure IoT 디바이스 SDK](#azure-iot-device-sdk)를 사용하여 런타임에 디지털 쌍과 상호 작용할 수 있습니다. 예를 들어, 디바이스의 디지털 쌍에서 속성 값을 설정할 수 있으며 SDK는 클라우드의 IoT 솔루션에 이 변경 내용을 전달합니다.

## <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)가 [IoT Hub](#azure-iot-hub)에 연결된 경우 허브는 라우팅 기능을 사용하여 디지털 쌍 변경 내용에 대한 알림을 보낼 수 있습니다. 예를 들어 디바이스에서 [속성](#properties) 값이 변경될 때마다 IoT Hub는 Service Bus 큐와 같은 엔드포인트로 알림을 보낼 수 있습니다.

## <a name="digital-twin-definition-language"></a>디지털 쌍 정의 언어

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에 대한 모델 및 인터페이스를 설명하는 언어입니다. [디지털 쌍 정의 언어](https://aka.ms/DTDL)를 사용하여 [디지털 쌍](#digital-twin)의 기능을 설명하고 IoT 플랫폼과 IoT 솔루션에서 엔터티의 의미 체계를 활용할 수 있도록 합니다.

## <a name="digital-twin-route"></a>디지털 쌍 경로

[디지털 쌍 변경 이벤트](#digital-twin-change-events)를 엔드포인트(예: Service Bus 큐)에 전달하기 위해 [IoT Hub](#azure-iot-hub)에 설정된 경로입니다.

## <a name="interface"></a>인터페이스

인터페이스는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 또는 [디지털 쌍](#digital-twin)에서 구현하는 관련 기능을 설명합니다. 여러 [디바이스 기능 모델](#device-capability-model)에서 인터페이스를 다시 사용할 수 있습니다.

## <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

IoT Hub 쿼리 언어는 여러 용도로 사용됩니다. 예를 들어, 언어를 사용하여 IoT Hub에 [등록된 디바이스](#device-registration)를 검색하거나 [디지털 쌍 라우팅](#digital-twin-route) 동작을 구체화할 수 있습니다.

## <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 디바이스

IoT 플러그 앤 플레이 디바이스는 일반적으로 데이터를 수집하거나 다른 디바이스를 제어하고 [디바이스 기능 모델](#device-capability-model)을 구현하는 소프트웨어 또는 펌웨어를 실행하는 소규모의 독립 실행형 컴퓨팅 디바이스입니다.  예를 들어, IoT 플러그 앤 플레이 디바이스는 환경 모니터링 디바이스이거나 스마트 농업 관개 시스템용 컨트롤러일 수 있습니다. IoT 플러그 앤 플레이 디바이스에서 명령을 하고 제어하고 데이터를 수신하기 위한 클라우드 호스티트 IoT 솔루션을 작성할 수 있습니다. [IoT용 Azure Certified 디바이스 카탈로그](#certified-for-iot-device-catalog)는 사용 가능한 IoT 플러그 앤 플레이 디바이스를 나열합니다. 카탈로그의 각 IoT 플러그 앤 플레이 디바이스는 유효성 검사를 마쳤으며, [디바이스 기능 모델](#device-capability-model)을 포함하고 있습니다.

## <a name="microsoft-partner-center"></a>Microsoft 파트너 센터

[Microsoft 파트너 센터](https://docs.microsoft.com/partner-center/)는 조직이 Microsoft와 종단 간 관계를 관리하는 곳입니다. [IoT용 Azure Certified 포털](#azure-certified-for-iot-portal)에서 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)를 인증하려면 먼저 Microsoft 파트너 센터 계정이 필요합니다.

## <a name="model-discovery"></a>모델 검색

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)가 IoT 솔루션에 연결되면 솔루션은 [디바이스 기능 모델](#device-capability-model)을 찾아 디바이스의 기능을 검색할 수 있습니다. 디바이스는 해당 기능 모델을 솔루션으로 보낼 수 있으며, 솔루션에서 [모델 리포지토리](#model-repository)에서 디바이스 기능 모델을 찾을 수 있습니다.

## <a name="model-repository"></a>모델 리포지토리

모델 리포지토리는 [디바이스 기능 모델](#device-capability-model) 및 [인터페이스](#interface)를 저장합니다. 단일 [퍼블릭 모델 리포지토리](#public-model-repository)가 있습니다. 조직에서는 고유한 조직 모델 리포지토리를 만들 수 있습니다.

## <a name="model-repository-rest-api"></a>Model repository REST API

모델 리포지토리를 관리하고 상호 작용하기 위한 API입니다. 예를 들어 API를 사용하여 [디바이스 기능 모델](#device-capability-model)을 추가하고 기능 모델을 검색할 수 있습니다.

## <a name="properties"></a>속성

속성은 디지털 쌍의 일부 상태를 나타내는 [인터페이스](#interface)에 정의된 데이터 필드입니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 자체에서 실행되는 코드에 의해 설정됩니다.  경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT 솔루션에서 설정됩니다.

## <a name="public-model-repository"></a>퍼블릭 모델 리포지토리

[인증 디바이스](#device-certification)에 대한 [디바이스 기능 모델](#device-capability-model) 및 [인터페이스](#interface)를 저장하는 단일 퍼블릭 모델 리포지토리가 있습니다. 퍼블릭 모델 리포지토리는 [공통 인터페이스](#common-interface) 정의도 저장합니다.

## <a name="registration-id"></a>등록 ID

등록 ID는 [Device Provisioning Service](#device-provisioning-service)에서 디바이스를 고유하게 식별합니다. 이 ID는 [IoT Hub](#azure-iot-hub)의 디바이스에 대한 고유 식별자인 디바이스 ID와는 다릅니다.

## <a name="scope-id"></a>범위 ID

범위 ID 범위는 [Device Provisioning Service](#device-provisioning-service) 인스턴스를 고유하게 식별합니다.

## <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. 공유 액세스 서명 인증에는 공유 액세스 정책과 공유 액세스 서명(토큰이라고 부름)의 두 구성 요소가 있습니다. [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)는 공유 액세스 서명을 사용하여 [IoT Hub](#azure-iot-hub)에서 인증을 받습니다.

## <a name="solution-developer"></a>솔루션 개발자

솔루션 개발자는 솔루션 백 엔드를 만듭니다. 솔루션 개발자는 일반적으로 [IoT Hub](#azure-iot-hub) 및 [모델 리포지토리](#model-repository)와 같은 Azure 리소스를 사용하거나 [IoT Central](#azure-iot-central)을 사용합니다.

## <a name="telemetry"></a>원격 분석

[인터페이스](#interface)에 정의된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정값은 일반적으로 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 데이터 스트림으로 보내는 센서 판독값과 같은 값입니다.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code는 여러 플랫폼에서 사용할 수 있는 최신 코드 편집기입니다. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 팩에 포함된 것과 같은 확장을 사용하면 다양한 개발 시나리오를 지원하도록 편집기를 사용자 지정할 수 있습니다.
