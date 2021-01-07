---
title: Azure Portal에서 규모에 맞게 모듈 배포-Azure IoT Edge
description: Azure Portal을 사용하여 IoT Edge 디바이스 그룹에 대한 자동 배포 만들기
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9d03b6f4a512c22564480405ec0f0e0c0e62a958
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048426"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Azure Portal를 사용 하 여 규모에 IoT Edge 모듈 배포

Azure Portal에서 **IoT Edge 자동 배포** 를 만들어 한 번에 여러 장치에 대 한 지속적인 배포를 관리 합니다. IoT Edge의 자동 배포는 IoT Hub의 [자동 디바이스 관리](../iot-hub/iot-hub-automatic-device-management.md) 기능의 일부입니다. 배포는 여러 모듈을 여러 디바이스에 배포하고, 모듈의 상태를 추적하며, 필요한 경우 변경할 수 있게 하는 동적 프로세스입니다.

자세한 내용은 [단일 디바이스 또는 대규모 IoT Edge 자동 배포에 대한 이해](module-deployment-monitoring.md)를 참조하세요.

## <a name="identify-devices-using-tags"></a>태그를 사용하여 디바이스 식별

배포를 만들려면 먼저 적용할 디바이스를 지정할 수 있어야 합니다. Azure IoT Edge는 디바이스 쌍의 **태그**를 사용하여 디바이스를 식별합니다. 각 디바이스는 솔루션에 적합한 방식으로 정의하는 여러 개의 태그를 포함할 수 있습니다.

예를 들어, 스마트 빌딩의 캠퍼스를 관리 하는 경우 장치에 위치, 방 유형 및 환경 태그를 추가할 수 있습니다.

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

디바이스 쌍 및 태그에 대한 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

## <a name="create-a-deployment"></a>배포 만들기

IoT Edge는 시나리오를 사용자 지정 하는 데 사용할 수 있는 두 가지 유형의 자동 배포를 제공 합니다. 시스템 런타임 모듈과 추가 모듈 및 경로를 포함 하는 표준 *배포*를 만들 수 있습니다. 각 장치는 배포를 하나만 적용할 수 있습니다. 또는 시스템 런타임이 아닌 사용자 지정 모듈과 경로만 포함 하는 *계층화 된 배포*를 만들 수 있습니다. 많은 계층화 된 배포를 표준 배포 위에 있는 장치에서 결합할 수 있습니다. 두 가지 유형의 자동 배포를 함께 사용 하는 방법에 대 한 자세한 내용은 [단일 장치에 대 한 자동 배포 IoT Edge 이해](module-deployment-monitoring.md)를 참조 하세요.

배포 및 계층화 된 배포를 만드는 단계는 매우 유사 합니다. 모든 차이점은 다음 단계에서 호출 됩니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동 합니다.
1. 왼쪽 창의 메뉴에 있는 **자동 장치 관리**에서 **IoT Edge** 를 선택 합니다.
1. 위쪽 막대에서 **배포 만들기** 또는 **계층화 된 배포 만들기**를 선택 합니다.

배포를 만드는 데에는 5개 단계가 있습니다. 다음 섹션에서는 각 단계로 안내합니다.

>[!NOTE]
>이 문서의 단계는 IoT Edge 에이전트 및 허브의 최신 스키마 버전을 반영 합니다. 스키마 버전 1.1은 IoT Edge 버전 1.0.10 함께 출시 되었으며 모듈 시작 순서 및 경로 우선 순위 지정 기능을 사용 하도록 설정 합니다.
>
>1.0.9 또는 이전 버전을 실행 하는 장치에 배포 하는 경우 마법사의 **모듈** 단계에서 **런타임 설정을** 편집 하 여 스키마 버전 1.0을 사용 합니다.

### <a name="step-1-name-and-label"></a>1 단계: 이름 및 레이블

1. 배포에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다.
1. 배포 추적에 도움이 되도록 레이블을 키-값 쌍으로 추가할 수 있습니다. 예를 들어 **HostPlatform** 및 **Linux** 또는 **Version** 및 **3.0.1**입니다.
1. **다음: 모듈** 을 선택 하 여 2 단계로 이동 합니다.

### <a name="step-2-modules"></a>2 단계: 모듈

배포에 최대 50 개의 모듈을 추가할 수 있습니다. 모듈이 없는 배포를 만드는 경우 대상 장치에서 모든 현재 모듈을 제거 합니다.

배포에서 IoT Edge 에이전트 및 IoT Edge 허브 모듈의 설정을 관리할 수 있습니다. **런타임 설정** 을 선택 하 여 두 개의 런타임 모듈을 구성 합니다. 계층화 된 배포에서는 런타임 모듈이 포함 되지 않으므로 구성할 수 없습니다.

사용자 지정 코드를 모듈로 추가하거나 Azure 서비스 모듈을 수동으로 추가하려면 다음 단계를 수행합니다.

1. 페이지의 **Container Registry 설정** 섹션에서 모듈 이미지를 포함 하는 개인 컨테이너 레지스트리에 액세스 하기 위한 자격 증명을 제공 합니다.
1. 페이지의 **IoT Edge 모듈** 섹션에서 **추가**를 선택 합니다.
1. 드롭다운 메뉴에서 다음 세 가지 유형의 모듈 중 하나를 선택 합니다.

   * **IoT Edge 모듈** -모듈 이름과 컨테이너 이미지 URI를 제공 합니다. 예를 들어 샘플 SimulatedTemperatureSensor 모듈에 대 한 이미지 URI는 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` 입니다. 모듈 이미지가 개인 컨테이너 레지스트리에 저장 되어 있는 경우이 페이지의 자격 증명을 추가 하 여 이미지에 액세스 합니다.
   * **Marketplace 모듈** -Azure Marketplace에서 호스트 되는 모듈입니다. 일부 마켓플레이스 모듈에는 추가 구성이 필요 하므로 [Azure Marketplace IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 목록에서 모듈 세부 정보를 검토 하세요.
   * **모듈 Azure Stream Analytics** Azure Stream Analytics 워크 로드에서 생성 된 모듈입니다.

1. 필요한 경우 2 단계와 3 단계를 반복 하 여 배포에 모듈을 더 추가 합니다.

배포에 모듈을 추가한 후에는 해당 이름을 선택 하 여 **IoT Edge 모듈 업데이트** 페이지를 열 수 있습니다. 이 페이지에서 모듈 설정, 환경 변수, 만들기 옵션, 시작 순서 및 모듈 쌍을 편집할 수 있습니다. Marketplace에서 모듈을 추가한 경우 이러한 매개 변수 중 일부가 이미 채워져 있을 수 있습니다. 사용 가능한 모듈 설정에 대 한 자세한 내용은 [모듈 구성 및 관리](module-composition.md#module-configuration-and-management)를 참조 하세요.

계층화 된 배포를 만드는 경우 동일한 장치를 대상으로 하는 다른 배포에 존재 하는 모듈을 구성 하 고 있을 수 있습니다. 다른 버전을 덮어쓰지 않고 모듈 쌍을 업데이트 하려면 모듈 쌍 **설정** 탭을 엽니다. 모듈 쌍의 desired 속성 내에서 하위 섹션에 대 한 고유한 이름으로 새 **모듈 쌍 속성** 을 만듭니다 (예:) `properties.desired.settings` . 필드 내에서 속성을 정의 하는 경우 `properties.desired` 우선 순위가 낮은 배포에 정의 된 모듈의 desired 속성을 덮어씁니다.

![계층화 된 배포에 대 한 모듈 쌍 속성 설정](./media/how-to-deploy-monitor/module-twin-property.png)

계층화 된 배포의 모듈 쌍 구성에 대 한 자세한 내용은 [계층화 된 배포](module-deployment-monitoring.md#layered-deployment)를 참조 하세요.

배포에 대 한 모든 모듈을 구성 했으면 **다음: 경로** 를 선택 하 여 세 단계로 이동 합니다.

### <a name="step-3-routes"></a>3 단계: 경로

**경로** 탭에서 모듈과 IoT Hub 사이에서 메시지가 전달되는 방식을 정의합니다. 메시지는 이름/값 쌍을 사용하여 생성됩니다.

예를 들어 이름 **경로** 를 포함 하는 경로와 **/messages/의 값이 \* $upstream** 되 면 모든 모듈에서 출력 하는 모든 메시지를 가져와 IoT hub에 보냅니다.  

**우선 순위** 및 **ttl (Time to live** ) 매개 변수는 경로 정의에 포함할 수 있는 선택적 매개 변수입니다. Priority 매개 변수를 사용 하 여 메시지를 먼저 처리 해야 하는 경로 또는 마지막으로 처리 해야 하는 경로를 선택할 수 있습니다. 우선 순위는 숫자 0-9을 설정 하 여 결정 됩니다. 여기서 0은 우선 순위가 가장 높습니다. Time to live 매개 변수를 사용 하면 해당 경로에 있는 메시지를 큐에서 처리 하거나 제거할 때까지 보유 해야 하는 기간을 선언할 수 있습니다.

경로를 만드는 방법에 대 한 자세한 내용은 [Declare 경로](module-composition.md#declare-routes)를 참조 하세요.

**다음: 메트릭**을 선택 합니다.

### <a name="step-4-metrics"></a>4 단계: 메트릭

메트릭은 디바이스가 구성 콘텐츠를 적용한 결과로 다시 보고할 수 있는 다양한 상태의 요약 수를 제공합니다.

1. **메트릭 이름**에 대한 이름을 입력합니다.

1. **메트릭 조건**에 대한 쿼리를 입력합니다. 쿼리는 IoT Edge 허브 모듈 쌍의 [보고된 속성](module-edgeagent-edgehub.md#edgehub-reported-properties)을 기반으로 합니다. 메트릭은 쿼리에 의해 반환되는 행 수를 나타냅니다.

   예를 들면 다음과 같습니다.

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

**다음: 대상 장치**를 선택 합니다.

### <a name="step-5-target-devices"></a>5 단계: 대상 장치

디바이스의 tags 속성을 사용하여 이 배포를 받아야 하는 특정 디바이스를 대상으로 지정합니다.

여러 배포에서 동일한 디바이스를 대상으로 할 수 있으므로 각 배포에 우선 순위 번호를 부여해야 합니다. 충돌이 발생 한 경우 우선 순위가 가장 높은 배포 (더 큰 값은 높은 우선 순위를 나타냄)가 적용 됩니다. 두 배포의 우선 순위 번호가 동일하면 가장 최근에 만든 배포가 먼저 적용됩니다.

여러 배포가 동일한 장치를 대상으로 하는 경우 우선 순위가 더 높은 하나만 적용 됩니다. 여러 계층화 된 배포가 동일한 장치를 대상으로 하는 경우 모두 적용 됩니다. 그러나 이름이 같은 두 개의 경로가 있는 경우와 같이 속성이 중복 된 경우에는 우선 순위가 높은 계층화 된 배포에서 남은 항목을 덮어씁니다.

장치를 대상으로 하는 계층화 된 배포는 적용 하기 위해 기본 배포 보다 높은 우선 순위를 갖고 있어야 합니다.

1. 배포 **우선 순위**에 대해 양의 정수를 입력합니다.
1. **대상 조건**을 입력하여 이 배포의 대상으로 지정할 디바이스를 결정합니다. 조건은 디바이스 쌍 태그 또는 보고되는 디바이스 쌍 속성을 기반으로 하며, 표현 형식이 일치해야 합니다.예를 들어 `tags.environment='test'` 또는 `properties.reported.devicemodel='4000x'`입니다.

**다음: 검토 + 만들기** 를 선택 하 여 마지막 단계로 이동 합니다.

### <a name="step-6-review-and-create"></a>6 단계: 검토 및 만들기

배포 정보를 검토 한 다음 **만들기**를 선택 합니다.

배포를 모니터링 하려면 [IoT Edge 배포 모니터링](how-to-monitor-iot-edge-deployments.md)을 참조 하세요.

## <a name="modify-a-deployment"></a>배포 수정

배포를 수정하면 변경 내용이 모든 대상 디바이스에 즉시 복제됩니다. 기존 배포에 대 한 다음 설정 및 기능을 수정할 수 있습니다.

* 대상 조건
* 사용자 지정 메트릭
* 레이블
* 태그
* Desired 속성

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>대상 조건, 사용자 지정 메트릭 및 레이블 수정

1. IoT hub의 왼쪽 창 메뉴에서 **IoT Edge** 를 선택 합니다.
1. **IoT Edge 배포** 탭을 선택 하 고 구성 하려는 배포를 선택 합니다.
1. **대상 조건** 탭을 선택 합니다. **대상 조건을** 변경 하 여 원하는 장치를 대상으로 합니다. **우선 순위**를 조정할 수도 있습니다.  **저장**을 선택합니다.

    대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

    * 디바이스에서 이전 대상 조건을 충족하지 않았지만 새 대상 조건은 충족하고 이 배포가 해당 디바이스에 대해 가장 높은 순위이면 이 배포가 디바이스에 적용됩니다.
    * 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않으면 이 배포가 제거되고 다음으로 우선 순위가 가장 높은 배포가 적용됩니다.
    * 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않고 다른 배포의 대상 조건도 충족하지 않으면 디바이스에서 변경되지 않습니다. 디바이스는 현재 모듈을 현재 상태로 계속 실행하지만 더 이상 이 배포의 일부로 관리되지 않습니다. 다른 배포의 대상 조건을 충족하면 이 배포를 제거하고 새 배포가 적용됩니다.

1. **메트릭** 탭을 선택 하 고 **메트릭 편집** 단추를 클릭 합니다. 예제 구문을 지침으로 사용 하 여 사용자 지정 메트릭을 추가 하거나 수정 합니다. **저장**을 선택합니다.

    ![배포에서 사용자 지정 메트릭 편집](./media/how-to-deploy-monitor/metric-list.png)

1. **레이블** 탭을 선택 하 고 원하는 대로 변경 하 고 **저장**을 선택 합니다.

## <a name="delete-a-deployment"></a>배포 삭제

배포를 삭제 하는 경우 배포 된 모든 장치는 우선 순위가 가장 높은 다음 배포를 수행 합니다. 디바이스에서 다른 배포의 대상 조건을 충족하지 않으면 배포를 삭제해도 모듈이 제거되지 않습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포** 탭을 선택 합니다.

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 확인란을 사용하여 삭제하려는 배포를 선택합니다.
1. **삭제**를 선택합니다.
1. 이 작업으로 이 배포가 삭제되고 모든 디바이스에 대한 이전 상태로 되돌릴 것임을 알리는 메시지가 표시됩니다.낮은 우선 순위의 배포가 적용 됩니다.다른 배포가 대상으로 지정되지 않으면 모듈이 제거되지 않습니다. 디바이스에서 모든 모듈을 제거하려는 경우 모듈이 없는 배포를 만들어서 동일한 디바이스에 배포합니다.계속하려면 **예**를 선택합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 디바이스에 모듈 배포](module-deployment-monitoring.md)에 대해 자세히 알아봅니다.