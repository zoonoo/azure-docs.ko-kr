---
title: IoT 플러그 앤 플레이 미리 보기 소개 | Microsoft Docs
description: IoT 플러그 앤 플레이 미리 보기에 대해 알아봅니다. IoT 플러그 앤 플레이는 IoT 디바이스가 기능을 선언할 수 있는 개방형 모델링 언어를 기반으로 합니다. IoT 디바이스는 Azure IoT Central 또는 파트너 애플리케이션 같은 클라우드 솔루션에 연결할 때 디바이스 기능 모델이라고 하는 선언을 제공합니다. 그러면 클라우드 솔루션이 코드를 작성하지 않고도 자동으로 디바이스를 이해하고 디바이스와 상호 작용을 시작할 수 있습니다.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770429"
---
# <a name="what-is-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이 미리 보기란?

IoT 플러그 앤 플레이 미리 보기를 사용하면 솔루션 개발자는 포함된 코드를 작성하지 않고도 디바이스를 솔루션과 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심은 디바이스 기능을 설명하는 _디바이스 기능 모델_ 스키마입니다. 이 스키마는 다음 정의를 포함하는 인터페이스 세트로 구성되는 JSON 문서입니다.

- 디바이스 또는 다른 엔터티의 읽기 전용 및 읽기/쓰기 상태를 나타내는 _속성_. 예를 들어 디바이스 일련 번호는 읽기 전용 속성일 수 있으며, 자동 온도 조절기의 목표 온도는 읽기/쓰기 속성일 수 있습니다.
- 데이터가 센서 판독값의 일반 스트림이든, 가끔 발생하는 오류이든, 또는 정보 메시지이든 관계 없이, 디바이스에서 내보내는 데이터인 _원격 분석 데이터_.
- 디바이스에서 수행할 수 있는 함수 또는 작업을 설명하는 _명령_. 예를 들어 명령으로 게이트웨이를 다시 부팅하거나 원격 카메라를 사용하여 사진을 찍을 수 있습니다.

디바이스 기능 모델에서 인터페이스를 다시 사용하여 협업을 더 쉽게 수행하고 개발 속도를 높일 수 있습니다.

IoT 플러그 앤 플레이가 [Azure Digital Twins](../digital-twins/about-digital-twins.md)와 원활하게 작동하도록 하기 위해 IoT 플러그 앤 플레이 스키마는 [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)을 사용하여 정의됩니다. IoT 플러그 앤 플레이 및 DTDL은 커뮤니티에 공개되며, Microsoft는 고객, 파트너 및 업계 기업과의 협업을 환영합니다. 둘 다 서비스와 도구를 더 쉽게 도입할 수 있게 해주는 JSON-LD 및 RDF 같은 개방형 W3C 표준을 기반으로 합니다. 또한 IoT 플러그 앤 플레이 및 DTDL을 사용하기 위한 추가 비용이 없습니다. [Azure IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/core/overview-iot-central.md) 및 다른 Azure 서비스의 표준 요금은 동일하게 유지됩니다.

IoT Hub 또는 IoT Central 기반의 솔루션은 IoT 플러그 앤 플레이의 이점을 누릴 수 있습니다.

이 문서에서는 다음 내용을 간략하게 설명합니다.

- IoT 플러그 앤 플레이를 사용하는 프로젝트와 연결된 일반적인 역할
- 애플리케이션에서 IoT 플러그 앤 플레이 디바이스를 사용하는 방법
- IoT 플러그 앤 플레이를 지원하는 IoT 디바이스 애플리케이션을 개발하는 방법
- IoT 플러그 앤 플레이 디바이스를 인증하고 [IoT용 Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)에 게시하는 방법

## <a name="user-roles"></a>사용자 역할

IoT 플러그 앤 플레이는 다음과 같은 두 가지 유형의 개발자에게 유용합니다.

- _솔루션 개발자_는 Azure IoT 및 기타 Azure 리소스를 사용하여 IoT 솔루션을 개발하고 통합할 IoT 디바이스를 식별하는 일을 합니다.
- _디바이스 개발자_는 솔루션에 연결된 디바이스에서 실행되는 코드를 만듭니다.

## <a name="use-iot-plug-and-play-devices"></a>IoT 플러그 앤 플레이 디바이스 사용

솔루션 개발자는 IoT 플러그 앤 플레이 디바이스를 사용하는 클라우드 호스팅 IoT 솔루션을 개발할 수 있습니다. 다음 Azure 서비스 중 하나를 사용할 수 있습니다.

- [Azure IoT Central](../iot-central/core/overview-iot-central.md) - 실제 세계와 디지털 세계를 연결하는 제품을 쉽게 만들 수 있는 완전 관리형 IoT SaaS(Software-as-a-Service) 솔루션입니다.
- [IoT Hub](../iot-hub/about-iot-hub.md) - IoT 애플리케이션과 디바이스 간의 안전한 양방향 통신을 위한 메시지 허브 역할을 하는 관리형 클라우드 서비스입니다.

IoT용 Azure Certified 디바이스 카탈로그를 통해 IoT 플러그 앤 플레이 디바이스를 찾을 수 있습니다. 카탈로그의 각 IoT 플러그 앤 플레이 디바이스는 유효성 검사를 마쳤으며, 디바이스 기능 모델을 포함하고 있습니다. 디바이스 기능 모델을 살펴보고 디바이스의 기능을 이해하거나 Azure IoT Central에서 디바이스를 시뮬레이션하는 데 사용할 수 있습니다.

IoT 플러그 앤 플레이 디바이스를 연결하면 해당 디바이스 기능 모델, 모델에 포함된 인터페이스, 해당 인터페이스에 정의된 원격 분석, 속성 및 명령을 볼 수 있습니다.

## <a name="develop-an-iot-device-application"></a>IoT 디바이스 애플리케이션 개발

디바이스 개발자는 IoT 플러그 앤 플레이를 지원하는 IoT 하드웨어 제품을 개발할 수 있습니다. 이 프로세스에는 다음과 같은 두 가지 주요 단계가 포함됩니다.

1. 디바이스 기능 모델 및 인터페이스를 정의합니다. [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)을 사용하여 디바이스의 기능을 선언하는 JSON 파일 세트를 작성합니다. 디바이스 기능 모델은 물리적 제품과 같은 전체 엔터티를 설명하고, 해당 엔터티에 의해 구현된 인터페이스 세트를 정의합니다. 인터페이스는 디바이스에서 지원되는 원격 분석, 속성 및 명령을 고유하게 식별하는 공유 계약입니다. 인터페이스는 여러 디바이스 기능 모델에서 재사용할 수 있습니다.

1. 디바이스 기능 모델 및 인터페이스에 선언된 기능을 구현하는 디바이스 소프트웨어 또는 펌웨어를 제작합니다. Azure IoT SDK에는 디바이스 기능 모델을 구현하는 API가 포함되어 있습니다.

[Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 확장 팩은 다양한 지원 기능을 제공합니다. 예를 들어 디바이스 개발자는 확장을 사용하여 기능 모델에서 기본 C 프로젝트를 생성할 수 있습니다. 그러나 모든 IDE를 사용하여 디바이스 기능 모델을 작성하고 구현할 수 있습니다.

## <a name="certify-an-iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 디바이스 인증

디바이스 개발자는 IoT 하드웨어 제품을 제출하여 인증을 받을 수 있습니다. 인증된 디바이스를 IoT용 Certified 디바이스 카탈로그에 게시할 수 있습니다. 인증 프로세스 단계는 다음과 같습니다.

- [Microsoft 파트너 네트워크](https://partner.microsoft.com)에 참가합니다.
- IoT용 Azure Certified 포털에 온보딩합니다.
- IoT 플러그 앤 플레이 디바이스 기능 모델 및 마케팅 정보를 제출하여 새 디바이스 레코드를 만듭니다.
- 디바이스에 대한 자동화된 유효성 검사 테스트 세트를 통과합니다.
- IoT용 Certified 디바이스 카탈로그에 게시합니다.

## <a name="regional-availability"></a>국가별 가용성

공개 미리 보기로 제공 중에는 모든 지역에서 IoT 플러그 앤 플레이를 사용할 수 있습니다.

## <a name="message-quotas-in-iot-hub"></a>IoT Hub의 메시지 할당량
공개 미리 보기 기간에는 IoT 플러그 앤 플레이 디바이스가 인터페이스마다 별도의 메시지를 전송하며, 이로 인해 [메시지 할당량](../iot-hub/iot-hub-devguide-quotas-throttling.md)에 집계되는 메시지 수가 증가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이의 개요를 살펴보았으므로, 그 다음 단계로 다음 빠른 시작 중 하나를 도전해보세요.

- [디바이스 기능 모델을 사용하여 IoT 플러그 앤 플레이 디바이스 만들기](./quickstart-create-pnp-device-windows.md)
- [디바이스를 IoT Hub에 연결](./quickstart-connect-pnp-device-c-windows.md)
- [솔루션에서 디바이스에 연결](./quickstart-connect-pnp-device-solution-node.md)
