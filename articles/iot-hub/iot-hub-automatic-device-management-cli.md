---
title: Azure IoT Hub(CLI)를 통한 대규모 자동 장치 관리 | 마이크로 소프트 문서
description: Azure IoT Hub 자동 구성을 사용하여 여러 IoT 장치 또는 모듈 관리
author: Philmea
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: philmea
ms.openlocfilehash: 76728b99f1e9309294079d4df88e563054e780e7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767577"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Azure CLI를 사용하는 자동 IoT 장치 및 모듈 관리

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub의 자동 장치 관리는 대규모 장치 플릿을 관리하는 반복적이고 복잡한 많은 작업을 자동화합니다. 자동 장치 관리를 사용하면 해당 속성에 따라 장치 집합을 대상으로 지정하고, 원하는 구성을 정의한 다음 IoT Hub가 범위에 올 때 장치를 업데이트하도록 할 수 있습니다. 이 업데이트는 _자동 장치 구성_ 또는 자동 모듈 _구성을_사용하여 수행되며, 이를 통해 완료 및 규정 준수를 요약하고 병합 및 충돌을 처리하고 단계별 방식으로 구성을 롤아웃할 수 있습니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

자동 장치 관리는 원하는 속성으로 장치 쌍 또는 모듈 쌍 집합을 업데이트하고 두 개의 보고된 속성을 기반으로 하는 요약을 보고하여 작동합니다.  세 부분으로 구성이라는 새 클래스및 *Configuration* JSON 문서를 소개합니다.

* **대상 조건은** 업데이트할 장치 쌍 또는 모듈 쌍의 범위를 정의합니다. 대상 조건은 디바이스 쌍 태그 및/또는 보고된 속성에서 쿼리로 지정됩니다.

* **대상 콘텐츠는** 대상 장치 쌍 또는 모듈 쌍에 추가하거나 업데이트할 원하는 속성을 정의합니다. 콘텐츠에는 변경할 원하는 속성의 섹션에 대한 경로가 포함됩니다.

* **메트릭**은 **성공**, **진행 중** 및 **오류**와 같은 다양한 구성 상태의 요약 횟수를 정의합니다. 사용자 지정 메트릭은 보고된 쌍 속성에 대한 쿼리로 지정됩니다.  시스템 메트릭은 대상으로 지정된 쌍둥이 수 및 성공적으로 업데이트된 쌍둥이 수와 같은 쌍 업데이트 상태를 측정하는 기본 메트릭입니다.

자동 구성은 구성이 생성된 직후에 처음으로 실행된 다음 5분 간격으로 실행됩니다. 메트릭 쿼리는 자동 구성이 실행될 때마다 실행됩니다.

## <a name="cli-prerequisites"></a>CLI 필수 조건

* Azure 구독의 [IoT 허브입니다.](../iot-hub/iot-hub-create-using-cli.md) 

* 사용자 환경의 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 최소한 Azure CLI 버전은 2.0.70 이상이어야 합니다. `az –-version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다. 

* [Azure CLI에 대한 IoT 확장.](https://github.com/Azure/azure-cli)

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>쌍둥이 구현

자동 디바이스 구성은 클라우드와 디바이스 간의 상태를 동기화하는 디바이스 쌍을 사용해야 합니다.  자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

자동 모듈 구성은 클라우드와 모듈 간의 상태를 동기화하기 위해 모듈 트윈을 사용해야 합니다. 자세한 내용은 [IoT Hub의 모듈 쌍둥이 이해 및 사용을](iot-hub-devguide-module-twins.md)참조하십시오.

## <a name="use-tags-to-target-twins"></a>태그를 사용하여 쌍둥이 대상

구성을 만들기 전에 영향을 줄 장치 또는 모듈을 지정해야 합니다. Azure IoT Hub는 장치 및 장치 쌍에서 태그를 사용하여 장치를 식별하고 모듈 쌍에 있는 태그를 사용하여 모듈을 식별합니다. 각 장치 또는 모듈에는 여러 태그가 있을 수 있으며 솔루션에 적합한 방식으로 정의할 수 있습니다. 예를 들어 다른 위치에서 장치를 관리하는 경우 장치 쌍에 다음 태그를 추가합니다.

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>대상 콘텐츠 및 메트릭 정의

대상 콘텐츠 및 메트릭 쿼리는 측정할 속성을 설정하고 보고할 장치 쌍 또는 모듈 쌍 원하는 속성을 설명하는 JSON 문서로 지정됩니다.  Azure CLI를 사용하여 자동 구성을 만들려면 대상 콘텐츠 및 메트릭을 로컬로 .txt 파일로 저장합니다. 장치를 적용 하기 위해 명령을 실행할 때 이후 섹션에서 파일 경로 사용 합니다.

자동 장치 구성에 대한 기본 대상 콘텐츠 샘플은 다음과 같습니다.

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

자동 모듈 구성은 매우 유사하게 `moduleContent` 행동하지만 `deviceContent`대상 대신 에 대한 대상입니다.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

다음은 메트릭 쿼리 예입니다.

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

모듈에 대한 메트릭 쿼리는 장치의 쿼리와 유사하지만 `moduleId` `devices.modules`에서 선택합니다. 예를 들어: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>구성 만들기

대상 콘텐츠 및 메트릭으로 구성된 구성을 만들어 대상 디바이스를 구성합니다. 

다음 명령을 사용하여 구성을 만듭니다.

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - IoT Hub에 만들 구성의 이름입니다. 구성에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다.

* --**labels** - 구성을 추적하는 데 도움이 되는 레이블을 추가합니다. 레이블은 배포를 설명하는 이름, 값 쌍입니다. 예를 들어 `HostPlatform, Linux` 또는 `Version, 3.0.1`와 같습니다.

* --**content** - 원하는 쌍 속성으로 설정할 대상 콘텐츠의 파일 경로 또는 인라인 JSON입니다. 

* --**hub-name** - 구성을 만들 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

* --**대상 조건** - 대상 조건을 입력하여 이 구성으로 대상으로 지정할 장치 또는 모듈을 결정합니다.자동 장치 구성의 경우 조건은 장치 쌍 태그 또는 장치 쌍 원하는 속성을 기반으로 하며 식 형식과 일치해야 합니다.예를 들어 `tags.environment='test'` 또는 `properties.desired.devicemodel='4000x'`입니다.자동 모듈 구성의 경우 이 조건은 모듈 트윈 태그 또는 모듈 트윈 원하는 속성을 기반으로 합니다. 예를 들어 `from devices.modules where tags.environment='test'` 또는 `from devices.modules where properties.reported.chillerProperties.model='4000x'`입니다.

* --**priority** - 양의 정수입니다. 두 개 이상의 구성이 동일한 장치 또는 모듈을 대상으로 하는 경우 Priority에 대한 수치가 가장 높은 구성이 적용됩니다.

* --**metrics** - 메트릭 쿼리의 파일 경로입니다. 메트릭은 장치 또는 모듈이 구성 콘텐츠를 적용한 후 다시 보고할 수 있는 다양한 상태의 요약 수를 제공합니다. 예를 들어 보류 중인 설정 변경에 대한 메트릭, 오류에 대한 메트릭 및 성공적인 설정 변경에 대한 메트릭을 만들 수 있습니다. 

## <a name="monitor-a-configuration"></a>구성 모니터링

다음 명령을 사용하여 구성 내용을 표시합니다.

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - IoT Hub에 있는 구성의 이름입니다.

* --**hub-name** - 구성이 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

명령 창에서 구성을 검사합니다.**metrics** 속성은 각 허브에서 평가되는 각 메트릭의 개수를 나열합니다.

* **targetedCount** - 대상 지정 조건과 일치하는 IoT Hub의 장치 쌍 또는 모듈 쌍 수를 지정하는 시스템 메트릭입니다.

* **appliedCount** - 시스템 메트릭은 대상 콘텐츠가 적용된 장치 또는 모듈 수를 지정합니다.

* **맞춤 측정항목** - 정의한 모든 측정항목은 사용자 측정항목입니다.

다음 명령을 사용하여 각 메트릭에 대한 장치 ID, 모듈 ID 또는 개체 목록을 표시할 수 있습니다.

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - IoT Hub에 있는 배포의 이름입니다.

* --**메트릭 ID** - 예를 들어 `appliedCount`장치 ID 또는 모듈 ID 목록을 보려는 메트릭의 이름입니다.

* --**hub-name** - 배포가 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

* --**metric-type** - 메트릭 유형은 `system` 또는 `user`일 수 있습니다.  시스템 메트릭은 `targetedCount` 및 `appliedCount`입니다. 다른 모든 메트릭은 사용자 메트릭입니다.

## <a name="modify-a-configuration"></a>구성 수정

구성을 수정하면 변경 내용이 즉시 모든 대상 디바이스에 복제됩니다. 

대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

* 쌍이 이전 대상 조건을 충족하지 않았지만 새 대상 조건을 충족하고 이 구성이 해당 쌍의 최우선 순위인 경우 이 구성이 적용됩니다. 

* 현재 이 구성을 실행 중인 트윈이 대상 조건을 더 이상 충족하지 않으면 구성의 설정이 제거되고 다음 우선 순위가 높은 구성에 의해 쌍이 수정됩니다. 

* 현재 이 구성을 실행 중인 트윈이 대상 조건을 더 이상 충족하지 않고 다른 구성의 대상 조건을 충족하지 않는 경우 구성의 설정이 제거되고 쌍에 대한 다른 변경 사항이 없습니다. 

다음 명령을 사용하여 구성을 업데이트합니다.

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - IoT Hub에 있는 구성의 이름입니다.

* --**hub-name** - 구성이 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

* --**set** - 구성에서 속성을 업데이트합니다. 다음 속성을 업데이트할 수 있습니다.

    * targetCondition - 예: `targetCondition=tags.location.state='Oregon'`

    * 레이블 

    * priority

## <a name="delete-a-configuration"></a>구성 삭제

구성을 삭제하면 모든 장치 쌍 또는 모듈 쌍이 다음으로 높은 우선 순위 구성을 수행합니다. 쌍이 다른 구성의 대상 조건을 충족하지 않으면 다른 설정이 적용되지 않습니다. 

다음 명령을 사용하여 구성을 삭제합니다.

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - IoT Hub에 있는 구성의 이름입니다.

* --**hub-name** - 구성이 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 대규모로 IoT 장치를 구성하고 모니터링하는 방법을 배웠습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [대량으로 IoT Hub 디바이스 ID 관리](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 메트릭](iot-hub-metrics.md)
* [작업 모니터링](iot-hub-operations-monitoring.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)

IoT Hub Device Provisioning Service를 사용하여 무인 Just-In-Time 프로비저닝을 수행하는 방법을 알아보려면 다음을 참조하세요. 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
