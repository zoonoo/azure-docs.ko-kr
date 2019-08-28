---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880463"
---
## <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 장치

IoT 플러그 앤 플레이 장치는 일반적으로 데이터를 수집 하거나 다른 장치를 제어할 수 있고 [장치 기능 모델](#device-capability-model)에 선언 된 기능을 구현 하는 소프트웨어 또는 펌웨어를 실행 하는 소규모의 독립 실행형 컴퓨팅 장치입니다.  예를 들어 IoT 플러그 앤 플레이 장치는 환경 모니터링 장치 이거나 스마트 agrigulture 관개 시스템용 컨트롤러 일 수 있습니다. 클라우드 호스팅 IoT 솔루션은 IoT 플러그 앤 플레이 장치에서 명령, 제어 및 수신 데이터를 쓸 수 있습니다. IoT 플러그 앤 플레이 장치는 [IoT용 Azure Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)를 통해 찾을 수 있습니다. 카탈로그의 각 IoT 플러그 앤 플레이 장치는 유효성을 검사 하 고 [장치 기능 모델](#device-capability-model)을 포함 합니다.

## <a name="digital-twin"></a>디지털 쌍

Digital 쌍는 IoT 플러그 앤 플레이 장치의 모델입니다.  디지털 쌍은 [디지털 쌍 정의 언어](https://aka.ms/DTDL)를 사용 하 여 모델링 됩니다.  Azure IoT API를 사용 하 여 디지털 쌍과 상호 작용할 수 있습니다. 

## <a name="digital-twin-definition-language"></a>디지털 쌍 정의 언어

[IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)에 대 한 모델 및 인터페이스를 설명 하는 언어입니다.  디지털 쌍 [정의 언어](https://aka.ms/DTDL) 를 사용 하 여 [디지털 쌍의](#digital-twin) 기능을 설명 하 고 iot 플랫폼과 iot 솔루션을 사용 하도록 설정 하 여 엔터티의 의미 체계를 활용할 수 있습니다.

## <a name="device-capability-model"></a>장치 기능 모델

장치 기능 모델은 장치에 대해 설명 하 고 장치에서 구현 하는 인터페이스 집합을 정의 합니다. 물리적 장치, 제품 또는 SKU에 해당 하는 장치 기능 모델을 만듭니다.

## <a name="interface"></a>인터페이스

인터페이스는 장치 또는 디지털 쌍에 의해 구현 되는 관련 기능을 설명 합니다. 인터페이스는 여러 기능 모델에서 재사용할 수 있습니다.

## <a name="property"></a>속성

속성은 디지털 쌍의 일부 상태를 나타내는 [인터페이스](#interface) 에 정의 된 데이터 필드입니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 읽기 전용 속성은 일련 번호와 같이 IoT 플러그 앤 플레이 장치 자체의 컨텍스트에서 실행 되는 코드에 의해 설정 됩니다.  쓰기 가능한 속성은 경보 임계값과 같은 외부 엔터티에 의해 설정 됩니다.

## <a name="telemetry"></a>원격 분석

[인터페이스](#interface) 에 정의 된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정은 일반적으로 센서 판독값과 같은 값입니다.

## <a name="command"></a>명령

[인터페이스](#interface) 에 정의 된 명령은 디지털 쌍에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어 장치를 다시 설정 하는 reset 명령이 있습니다.
