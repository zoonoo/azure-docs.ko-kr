---
title: IoT Edge 배포 모니터링 - Azure IoT Edge
description: edgeHub 및 edgeAgent 보고된 속성과 자동 배포 메트릭을 비롯한 대략적인 모니터링입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4ca22c39fcd7b488f527326e4fdeaa56b0485cb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220300"
---
# <a name="monitor-iot-edge-deployments"></a>IoT Edge 배포 모니터링

Azure IoT Edge는 IoT Edge 디바이스에 배포된 모듈에 대한 실시간 정보를 모니터링할 수 있는 보고 기능을 제공합니다. IoT Hub 서비스는 디바이스에서 상태를 검색하여 운영자에게 제공합니다. 자동 배포 및 계층화된 배포를 포함하는 [규모에 맞게 수행된 배포](module-deployment-monitoring.md)에도 모니터링이 중요합니다.

디바이스와 모듈 모두 연결과 같은 유사한 데이터를 포함하므로 디바이스 ID 또는 모듈 ID에 따라 값을 가져옵니다.

IoT Hub 서비스는 디바이스 및 모듈 쌍에서 보고하는 데이터를 수집하고 디바이스에 포함될 수 있는 다양한 상태의 수를 제공합니다. IoT Hub 서비스는 이 데이터를 4개의 메트릭 그룹으로 구성합니다.

| Type | Description |
| --- | ---|
| 대상 지정됨 | 배포 대상 조건과 일치하는 IoT Edge 디바이스가 표시됩니다. |
| 적용됨 | 더 높은 우선 순위의 다른 배포에서 대상으로 지정하지 않은 대상 IoT Edge 디바이스가 표시됩니다. |
| 성공 보고 | 모듈이 성공적으로 배포되었다고 보고한 IoT Edge 디바이스가 표시됩니다. |
| 오류 보고 | 하나 이상의 모듈이 성공적으로 배포되지 않았다고 보고한 IoT Edge 디바이스가 표시됩니다. 오류를 자세히 조사하려면 해당 디바이스에 원격으로 연결하고 로그 파일을 살펴봅니다. |

IoT Hub 서비스를 사용하면 이 데이터를 Azure Portal 및 Azure CLI에서 모니터링할 수 있습니다.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Azure Portal에서 배포 모니터링

배포의 세부 정보를 확인하고 이를 실행하는 디바이스를 모니터링하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 창 메뉴에서 **IoT Edge**를 선택합니다.
1. **IoT Edge 배포** 탭을 선택합니다.
1. 배포 목록을 검사합니다. 각 배포에 대해 다음 세부 정보를 볼 수 있습니다.

    | 열 | Description |
    | --- | --- |
    | ID | 배포의 이름입니다. |
    | Type | 배포 유형(**배포** 또는 **계층화된 배포**)입니다. |
    | 대상 조건 | 대상 디바이스를 정의하는 데 사용된 태그입니다. |
    | 우선 순위 | 배포에 할당된 우선 순위 번호입니다. |
    | 시스템 메트릭 | 대상 조건과 일치하는 IoT Hub의 디바이스 쌍의 수입니다. **적용됨** IoT Hub에서 해당 모듈 쌍에 배포 콘텐츠를 적용한 디바이스 수를 지정합니다. |
    | 디바이스 메트릭 | IoT Edge 클라이언트 런타임에서 성공 또는 오류를 보고하는 IoT Edge 디바이스의 수입니다. |
    | 사용자 지정 메트릭 | 배포에 대해 정의한 메트릭 데이터를 보고하는 IoT Edge 디바이스 수입니다. |
    | 만든 시간 | 배포를 만든 시점의 타임스탬프입니다. 이 타임스탬프는 두 배포의 우선 순위가 동일한 경우 연결을 중단하는 데 사용됩니다. |

1. 모니터링하려는 배포를 선택합니다.  
1. **배포 세부 정보** 페이지에서 아래쪽 섹션으로 스크롤하고 **대상 조건** 탭을 선택합니다. **보기**를 선택하여 대상 조건과 일치하는 디바이스를 나열합니다. 조건 및 **우선 순위**도 변경할 수 있습니다. 변경 내용을 적용한 경우 **저장**을 선택합니다.

   ![배포를 위한 대상 디바이스 보기](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. **메트릭** 탭을 선택합니다. **메트릭 선택** 드롭다운에서 메트릭을 선택하면 결과를 표시하는 **보기** 단추가 나타납니다. **메트릭 편집**을 선택하여 정의한 사용자 지정 메트릭에 대한 조건을 조정할 수도 있습니다. 변경 내용을 적용한 경우 **저장**을 선택합니다.

   ![배포에 대한 메트릭 보기](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


배포를 변경하려면 [배포 수정](how-to-deploy-at-scale.md#modify-a-deployment)을 참조하세요.

## <a name="monitor-a-deployment-with-azure-cli"></a>Azure CLI를 사용하여 배포 모니터링

[az IoT Edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show) 명령을 사용하여 단일 배포에 대한 세부 정보를 표시합니다.

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

deployment show 명령은 다음 매개 변수를 사용합니다.

* **--deployment-id** - IoT Hub에 있는 배포의 이름입니다. 필수 매개 변수입니다.
* **--hub-name** - 배포가 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

명령 창에서 배포를 검사합니다. **metrics** 속성은 각 허브에서 평가되는 각 메트릭의 개수를 나열합니다.

* **targetedCount** - 대상 지정 조건과 일치하는 IoT Hub의 디바이스 쌍의 수를 지정하는 시스템 메트릭입니다.
* **appliedCount** - 시스템 메트릭은 IoT Hub에서 해당 모듈 쌍에 배포 콘텐츠를 적용한 디바이스 수를 지정합니다.
* **reportedSuccessfulCount** - IoT Edge 클라이언트 런타임의 성공을 보고하는 배포에서 IoT Edge 디바이스의 수를 지정하는 디바이스 메트릭입니다.
* **reportedFailedCount** - IoT Edge 클라이언트 런타임의 오류를 보고하는 배포에서 IoT Edge 디바이스의 수를 지정하는 디바이스 메트릭입니다.

[az IoT Edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show-metric) 명령을 사용하여 각 메트릭에 대한 디바이스 ID 또는 개체 목록을 표시할 수 있습니다.

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

deployment show-metric 명령은 다음 매개 변수를 사용합니다.

* **--deployment-id** - IoT Hub에 있는 배포의 이름입니다.
* **--metric-id** - 디바이스 ID 목록을 보려는 메트릭의 이름(예: `reportedFailedCount`)입니다.
* **--hub-name** - 배포가 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

배포를 변경하려면 [배포 수정](how-to-deploy-cli-at-scale.md#modify-a-deployment)을 참조하세요.

## <a name="next-steps"></a>다음 단계

IoT Edge 배포의 연결 및 상태에 대 한 모듈 쌍, 주로 IoT Edge 에이전트 및 IoT Edge 허브 런타임 모듈을 [모니터링](how-to-monitor-module-twins.md)하는 방법에 대해 알아봅니다.
