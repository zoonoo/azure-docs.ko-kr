---
title: 공통 인터페이스-IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: IoT 플러그 앤 플레이 개발자를 위한 일반적인 인터페이스 설명
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770482"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT 플러그 앤 플레이 미리 보기 공통 인터페이스

모든 IoT 플러그 앤 플레이 디바이스는 일반적인 일부 인터페이스를 구현해야 합니다. 공통 인터페이스는 일관 된 기능을 제공 하기 때문에 IoT 솔루션 혜택을 제공 합니다. [인증](tutorial-build-device-certification.md) 을 사용 하려면 장치에서 몇 가지 공통 인터페이스를 구현 해야 합니다. 퍼블릭 모델 리포지토리에서 일반적인 인터페이스 정의를 검색할 수 있습니다.

## <a name="summary-of-common-interfaces"></a>공용 인터페이스 요약

| 속성 | ID | 설명 | Azure IoT SDK에 의해 구현 됨 | 기능 모델에서 선언 해야 합니다. |
| -------- | -------- | -------- | -------- | -------- | -------- |
| 모델 정보 | urn: azureiot: ModelDiscovery: Modeldiscovery: 1 | 장치에서 기능 모델 ID 및 인터페이스를 선언 합니다. 모든 IoT 플러그 앤 플레이 장치에 필요 합니다. | 예 | 아니요 |
| 디지털 쌍 클라이언트 SDK 정보 | urn: azureiot: 클라이언트: SDKInformation: 1 | Azure에 장치를 연결 하기 위한 클라이언트 SDK [인증](tutorial-build-device-certification.md) 에 필요 | 예 | 아니요 |
| 디바이스 정보 | urn: azureiot: DeviceManagement: DeviceInformation: 1 | 장치에 대 한 하드웨어 및 운영 체제 정보 [인증](tutorial-build-device-certification.md) 에 필요 | 예 | 예 |
| 모델 정의 | urn: azureiot: ModelDiscovery: Modeldiscovery: 1 | 장치에서 해당 기능 모델 및 인터페이스에 대 한 전체 정의를 선언 합니다. 모델 리포지토리에서 모델 정의를 호스팅하지 않는 경우에는를 구현 해야 합니다. | 예 | 예 |
| Digital Twin | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | 솔루션 개발자가 디지털 쌍의 기능 모델 ID 및 인터페이스 Id를 검색 하는 데 사용할 수 있습니다. IoT 플러그 앤 플레이 장치에서이 인터페이스를 선언 하거나 구현 하지 않습니다. | 아니요 | 아니요 |

- Azure IoT SDK에서 구현-Azure IoT SDK가 인터페이스에 선언 된 기능을 구현 하는지 여부입니다. Azure IoT SDK를 사용 하는 IoT 플러그 앤 플레이 장치는이 인터페이스를 구현할 필요가 없습니다.
- 기능 모델에서 선언 해야 합니다. ' 예 ' 인 경우이 인터페이스는 `"implements":` 이 IoT 플러그 앤 플레이 장치에 대 한 장치 기능 모델의 섹션 내에서 선언 되어야 합니다.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>공용 리포지토리에서 인터페이스 정의 검색

### <a name="cli"></a>CLI

Azure CLI에 대 한 Azure IoT 확장을 사용 하 여 공용 모델 리포지토리에서 공통 인터페이스를 검색할 수 있습니다.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS 코드

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이** 를 입력 한 다음 **IoT 플러그 앤 플레이: 모델 리포지토리 열기** 명령을 선택 합니다. **공용 리포지토리**를 선택 합니다. VS Code에서 공용 모델 리포지토리가 열립니다.

1. 공용 모델 리포지토리의 검색 필드에 인터페이스 이름을 입력 합니다.

1. 인터페이스의 로컬 복사본을 만들려면 검색 결과에서 해당 복사본을 선택 하 고 **다운로드**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이제 공통 인터페이스에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL (디지털 쌍 정의 언어)](https://aka.ms/DTDL)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
