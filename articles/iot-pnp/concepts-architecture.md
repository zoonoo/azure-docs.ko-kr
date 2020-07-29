---
title: IoT 플러그 앤 플레이 아키텍처 | Microsoft Docs
description: 솔루션 빌더는 IoT 플러그 앤 플레이의 핵심 아키텍처 요소를 이해 합니다.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f2d9407d9c7172839027e2650486695244f67889
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352219"
---
# <a name="iot-plug-and-play-preview-architecture"></a>IoT 플러그 앤 플레이 미리 보기 아키텍처

IoT 플러그 앤 플레이 미리 보기를 사용 하면 솔루션 빌더가 수동 구성 없이 스마트 장치를 솔루션과 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심은 IoT 플러그 앤 플레이 사용 응용 프로그램에 대 한 장치의 기능을 설명 하는 장치 _모델_ 입니다. 이 모델은 다음을 정의 하는 인터페이스 집합으로 구성 됩니다.

- 장치 또는 다른 엔터티의 읽기 전용 또는 쓰기 가능한 상태를 나타내는 _속성_ 입니다. 예를 들어 장치 일련 번호는 읽기 전용 속성 일 수 있으며, 자동 온도 조절기의 대상 온도는 쓰기 가능 속성 일 수 있습니다.
- 데이터가 센서 판독값의 일반 스트림 인지 가끔 오류 또는 정보 메시지 인지 여부에 관계 없이 장치에서 내보내는 데이터의 _원격 분석_ 입니다.
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
- IoT hub에 장치를 등록 하 고 장치에서 구현 하는 인터페이스의 컬렉션을 식별 하는 모델 ID를 발표 합니다.
- 장치가 구현 하는 DTDL 인터페이스에 정의 된 속성을 업데이트 합니다. 이러한 속성은 IoT hub와의 동기화를 관리 하는 디지털 쌍을 사용 하 여 구현 됩니다.
- 장치가 구현 하는 DTDL 인터페이스에 정의 된 명령에 대 한 명령 처리기를 추가 합니다.
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
- [모델 검색 프로세스](./concepts-model-discovery.md)
- [IoT 플러그 앤 플레이 용 개발](./concepts-developer-guide.md)
