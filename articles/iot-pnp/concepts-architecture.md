---
title: IoT 플러그 앤 플레이 아키텍처 | Microsoft Docs
description: 솔루션 빌더는 IoT 플러그 앤 플레이의 핵심 아키텍처 요소를 이해 합니다.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 32e67bd7f30fecee3449935a35235844a047957b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574327"
---
# <a name="iot-plug-and-play-architecture"></a>IoT 플러그 앤 플레이 아키텍처

IoT 플러그 앤 플레이를 사용 하면 솔루션 빌더가 수동 구성 없이 스마트 장치를 솔루션과 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심은 IoT 플러그 앤 플레이 사용 응용 프로그램에 대 한 장치의 기능을 설명 하는 장치 _모델_ 입니다. 이 모델은 다음을 정의 하는 인터페이스 집합으로 구성 됩니다.

- 속성 - 디바이스 또는 다른 엔터티의 읽기 전용 또는 쓰기 가능 상태를 나타냅니다. 예를 들어 디바이스 일련 번호는 읽기 전용 속성일 수 있으며, 자동 온도 조절기의 목표 온도는 쓰기 가능 속성일 수 있습니다.
- 원격 분석 - 데이터가 규칙적인 센서 판독 스트림, 가끔 발생하는 오류 또는 정보 메시지인지 여부에 관계 없이 디바이스가 내보내는 데이터입니다.
- 디바이스에서 수행할 수 있는 함수 또는 작업을 설명하는 _명령_. 예를 들어 명령으로 게이트웨이를 다시 부팅하거나 원격 카메라를 사용하여 사진을 찍을 수 있습니다.

모든 모델 및 인터페이스에는 고유 ID가 있습니다.

다음 다이어그램은 IoT 플러그 앤 플레이 솔루션의 주요 요소를 보여 줍니다.

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 플러그 앤 플레이 아키텍처":::

## <a name="model-repository"></a>모델 리포지토리

[모델 리포지토리](./concepts-model-repository.md) 는 모델 및 인터페이스 정의에 대 한 저장소입니다. [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)를 사용 하 여 모델 및 인터페이스를 정의 합니다.

웹 UI를 사용 하 여 모델 및 인터페이스를 관리할 수 있습니다.

모델 리포지토리는 RBAC를 사용 하 여 인터페이스 정의에 대 한 액세스를 제한할 수 있습니다.

## <a name="devices"></a>디바이스

장치 빌더는 [Azure iot 장치 sdk](./libraries-sdks.md)중 하나를 사용 하 여 iot 스마트 장치에서 실행 되는 코드를 구현 합니다. 장치 Sdk는 다음과 같은 장치 빌더를 지원 합니다.

- IoT hub에 안전 하 게 연결 합니다.
- IoT hub에 장치를 등록 하 고 장치에서 구현 하는 DTDL 인터페이스의 컬렉션을 식별 하는 모델 ID를 발표 합니다.
- 장치 및 IoT hub 간에 DTDL 인터페이스에 정의 된 속성을 동기화 합니다.
- DTDL 인터페이스에 정의 된 명령에 대 한 명령 처리기를 추가 합니다.
- IoT hub에 원격 분석을 보냅니다.

## <a name="iot-edge-gateway"></a>IoT Edge 게이트웨이

IoT Edge 게이트웨이는 iot hub에 직접 연결할 수 없는 IoT 플러그 앤 플레이 장치를 연결 하기 위한 중개자 역할을 합니다. 자세히 알아보려면 [IoT Edge 장치를 게이트웨이로 사용할 수 있는 방법](../iot-edge/iot-edge-as-gateway.md)을 참조 하세요.

## <a name="iot-edge-modules"></a>IoT Edge 모듈

_IoT Edge 모듈_ 을 사용 하면에 지에서 비즈니스 논리를 배포 하 고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 계산의 최소 단위이며 Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션별 코드를 포함할 수 있습니다.

_IoT Edge 허브_ 는 Azure IoT Edge 런타임을 구성 하는 모듈 중 하나입니다. IoT Hub와 동일한 프로토콜 엔드포인트를 노출하여 IoT Hub에 대한 로컬 프록시 역할을 합니다. 이 일관성은 클라이언트(디바이스 또는 모듈)에서 IoT Hub와 마찬가지로 IoT Edge 런타임에 연결할 수 있음을 의미합니다.

장치 Sdk를 통해 모듈 작성기를 통해 다음을 할 수 있습니다.

- IoT Edge 허브를 사용 하 여 IoT hub에 안전 하 게 연결 합니다.
- IoT hub를 사용 하 여 모듈을 등록 하 고 장치에서 구현 하는 DTDL 인터페이스의 컬렉션을 식별 하는 모델 ID를 발표 합니다.
- 장치 및 IoT hub 간에 DTDL 인터페이스에 정의 된 속성을 동기화 합니다.
- DTDL 인터페이스에 정의 된 명령에 대 한 명령 처리기를 추가 합니다.
- IoT hub에 원격 분석을 보냅니다.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) 은 IoT 솔루션과이 솔루션에서 관리 하는 장치 간의 양방향 통신을 위해 중앙 메시지 허브 역할을 하는 클라우드 호스팅 서비스입니다.

IoT hub:

- 장치에서 구현 된 모델 ID를 백 엔드 솔루션에 사용할 수 있도록 합니다.
- 허브에 연결 된 각 플러그 앤 플레이 장치와 연결 된 디지털 쌍을 유지 관리 합니다.
- 처리 또는 저장을 위해 다른 서비스에 원격 분석 스트림을 전달 합니다.
- 장치 모니터링을 사용 하도록 디지털 쌍 변경 이벤트를 다른 서비스로 라우팅합니다.

## <a name="backend-solution"></a>백 엔드 솔루션

백 엔드 솔루션은 IoT hub에서 디지털 쌍과 상호 작용 하 여 연결 된 장치를 모니터링 하 고 제어 합니다. 서비스 Sdk 중 하나를 사용 하 여 백 엔드 솔루션을 구현 합니다. 연결 된 장치의 기능을 이해 하기 위해 솔루션 백 엔드는 다음과 같습니다.

1. 장치가 IoT hub에 등록 된 모델 ID를 검색 합니다.
1. 모델 ID를 사용 하 여 모델 리포지토리에서 인터페이스 정의를 검색 합니다.
1. 모델 파서를 사용 하 여 인터페이스 정의에서 정보를 추출 합니다.

백 엔드 솔루션은 인터페이스 정의의 정보를 사용 하 여 다음을 수행할 수 있습니다.

- 장치에서 보고 한 속성 값을 읽습니다.
- 장치에서 쓰기 가능한 속성을 업데이트 합니다.
- 장치에 의해 구현 되는 명령을 호출 합니다.
- 장치에서 보내는 원격 분석의 형식을 이해 합니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 솔루션의 아키텍처에 대 한 개요를 만들었으므로 다음 단계는에 대 한 자세한 내용입니다.

- [모델 리포지토리](./concepts-model-repository.md)
- [디지털 쌍 모델 통합](./concepts-model-discovery.md)
- [IoT 플러그 앤 플레이 용 개발](./concepts-developer-guide-device-csharp.md)
