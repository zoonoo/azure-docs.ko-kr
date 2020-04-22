---
title: 공통 인터페이스 - IoT 플러그 앤 플레이 미리보기 | 마이크로 소프트 문서
description: IoT 플러그 앤 플레이 개발자를 위한 공통 인터페이스에 대한 설명
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770482"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT 플러그 앤 플레이 프리뷰 공통 인터페이스

모든 IoT 플러그 앤 플레이 장치는 몇 가지 일반적인 인터페이스를 구현해야 합니다. 공통 인터페이스는 일관된 기능을 제공하기 때문에 IoT 솔루션에 이점을 제공합니다. [인증을](tutorial-build-device-certification.md) 위해서는 장치가 몇 가지 공통 인터페이스를 구현해야 합니다. 공용 모델 리포지토리에서 공통 인터페이스 정의를 검색할 수 있습니다.

## <a name="summary-of-common-interfaces"></a>공통 인터페이스 요약

| 이름 | ID | 설명 | Azure IoT SDK에 의해 구현 | 기능 모델에서 선언해야 합니다. |
| -------- | -------- | -------- | -------- | -------- | -------- |
| 모델 정보 | 항아리:azureiot:모델 디스커버리:모델 정보:1 | 장치가 기능 모델 ID 및 인터페이스를 선언합니다. 모든 IoT 플러그 앤 플레이 장치에 필요합니다. | 예 | 예 |
| 디지털 트윈 클라이언트 SDK 정보 | 항아리:azureiot:클라이언트:SDK정보:1 | 장치를 Azure와 연결하기 위한 클라이언트 SDK입니다. [인증에](tutorial-build-device-certification.md) 필요 | 예 | 예 |
| 장치 정보 | 항아리:azureiot:장치 관리:장치 정보:1 | 장치에 대한 하드웨어 및 운영 체제 정보입니다. [인증에](tutorial-build-device-certification.md) 필요 | 예 | 예 |
| 모델 정의 | 항아리:azureiot:모델 디스커버리:모델 정의:1 | 장치가 해당 기능 모델 및 인터페이스에 대한 전체 정의를 선언합니다. 모델 정의가 모델 리포지토리에서 호스팅되지 않는 경우 구현해야 합니다. | 예 | 예 |
| Digital Twin | 항아리:azureiot:모델 디스커버리:디지털트윈:1 | 솔루션 개발자가 디지털 트윈에 대한 기능 모델 ID 및 인터페이스 ID를 검색할 수 있습니다. 이 인터페이스는 IoT 플러그 앤 플레이 장치에 의해 선언또는 구현되지 않습니다. | 예 | 예 |

- Azure IoT SDK에 의해 구현됨 - Azure IoT SDK가 인터페이스에 선언된 기능을 구현하는지 여부입니다. Azure IoT SDK를 사용하는 IoT 플러그 앤 플레이 장치는 이 인터페이스를 구현할 필요가 없습니다.
- 기능 모델에서 선언해야 합니다 - '예'인 경우 `"implements":` 이 IoT 플러그 앤 플레이 장치의 장치 기능 모델 섹션 내에서 이 인터페이스를 선언해야 합니다.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>공용 리포지토리에서 인터페이스 정의 검색

### <a name="cli"></a>CLI

Azure CLI에 대한 Azure IoT 확장을 사용하여 공용 모델 리포지토리에서 공통 인터페이스를 검색할 수 있습니다.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS 코드

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이를** 입력한 다음 **IoT 플러그 앤 플레이: 모델 리포지토리 커맨드를** 선택합니다. **공용 리포지토리를**선택합니다. VS Code에서 공용 모델 리포지토리가 열립니다.

1. 공용 모델 리포지토리에서 검색 필드에 인터페이스 이름을 입력합니다.

1. 인터페이스의 로컬 복사본을 만들려면 검색 결과에서 인터페이스를 선택한 다음 **다운로드를**선택합니다.

## <a name="next-steps"></a>다음 단계

이제 일반적인 인터페이스에 대해 배웠으니 몇 가지 추가 리소스는 다음과 같습니다.

- [디지털 트윈 정의 언어(DTDL)](https://aka.ms/DTDL)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
