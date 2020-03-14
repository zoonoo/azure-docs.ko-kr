---
title: Azure IoT Hub를 사용 하 여 대규모로 자동 장치 관리 Microsoft Docs
description: Azure IoT Hub 자동 구성을 사용 하 여 여러 IoT 장치 및 모듈 관리
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 75c6b7d89e7ae540e7428afde127281aa3f15fc6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271305"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Azure Portal를 사용 하 여 자동 IoT 장치 및 모듈 관리

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub의 자동 장치 관리는 여러 가지 반복적인 작업 및 복잡 한 장치 대규모 관리 작업을 자동화 합니다. 자동 장치 관리를 사용 하면 해당 속성을 기준으로 장치 집합을 대상으로 지정 하 고, 원하는 구성을 정의 하 고, 범위에 들어오면 장치를 IoT Hub 업데이트할 수 있습니다. 이 업데이트는 _자동 장치 구성_ 또는 _자동 모듈 구성을_사용 하 여 수행 되며,이를 통해 완료 및 규정 준수를 요약 하 고, 병합 및 충돌을 처리 하 고, 단계별 접근 방식으로 구성을 롤아웃할 수 있습니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

자동 장치 관리는 원하는 속성으로 장치 쌍 또는 모듈 쌍의 집합을 업데이트 하 고, 보고 된 쌍 속성을 기반으로 하는 요약을 보고 하는 방식으로 작동 합니다.  다음 세 부분으로 *구성* 된 새 클래스 및 JSON 문서를 소개 합니다.

* **대상 조건은** 업데이트할 장치 쌍 또는 모듈 쌍의 범위를 정의 합니다. 대상 조건은 쌍 태그 및/또는 보고 된 속성에 대 한 쿼리로 지정 됩니다.

* **대상 콘텐츠** 는 대상 장치 쌍 또는 모듈 쌍에서 추가 또는 업데이트할 desired 속성을 정의 합니다. 콘텐츠에는 변경할 원하는 속성의 섹션에 대한 경로가 포함됩니다.

* **메트릭**은 **성공**, **진행 중** 및 **오류**와 같은 다양한 구성 상태의 요약 횟수를 정의합니다. 사용자 지정 메트릭은 쌍으로 보고 된 속성에 대 한 쿼리로 지정 됩니다.  시스템 메트릭은 대상으로 지정 된 쌍의 수와 성공적으로 업데이트 된 쌍의 수와 같은 쌍 업데이트 상태를 측정 하는 기본 메트릭입니다.

자동 구성은 구성이 생성 된 후 즉시 실행 되며 5 분 간격으로 실행 됩니다. 메트릭 쿼리는 자동 구성이 실행 될 때마다 실행 됩니다.

## <a name="implement-twins"></a>쌍 구현

자동 디바이스 구성은 클라우드와 디바이스 간의 상태를 동기화하는 디바이스 쌍을 사용해야 합니다.  자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

자동 모듈 구성에서는 모듈 쌍을 사용 하 여 클라우드와 모듈 간의 상태를 동기화 해야 합니다. 자세한 내용은 [IoT Hub에서 모듈 쌍 이해 및 사용](iot-hub-devguide-module-twins.md)을 참조 하세요.

## <a name="use-tags-to-target-twins"></a>태그를 사용 하 여 쌍 대상

구성을 만들기 전에 영향을 원하는 장치 또는 모듈을 지정 해야 합니다. Azure IoT Hub는 장치를 식별 하 고 장치 쌍의 태그를 사용 하며 모듈 쌍의 태그를 사용 하 여 모듈을 식별 합니다. 각 장치 또는 모듈은 여러 태그를 포함할 수 있으며 솔루션에 적합 한 방식으로 정의할 수 있습니다. 예를 들어 다른 위치에서 장치를 관리 하는 경우 장치 쌍에 다음 태그를 추가 합니다.

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>구성 만들기

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다. 

2. **IoT 디바이스 구성**을 선택합니다.

3. **장치 구성 추가** 또는 **모듈 구성 추가**를 선택 합니다.

   ![장치 구성 또는 모듈 구성 추가](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

구성을 만드는 데에는 5단계가 있습니다. 다음 섹션에서는 각 단계로 안내합니다. 

### <a name="name-and-label"></a>이름 및 레이블

1. 구성에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다.

2. 구성을 추적하는 데 도움이 되는 레이블을 추가합니다. 레이블은 구성을 설명하는 **이름**, **값** 쌍입니다. 예를 들어 `HostPlatform, Linux` 또는 `Version, 3.0.1`입니다.

3. **다음**을 선택하여 다음 단계로 이동합니다. 

### <a name="specify-settings"></a>설정 지정

이 섹션에서는 대상 장치 또는 모듈 쌍에서 설정할 콘텐츠를 정의 합니다. 각 설정 집합에 대한 두 개의 입력이 있습니다. 첫 번째는 설정 될 쌍 desired 속성 내에서 JSON 섹션의 경로인 쌍 경로입니다.  두 번째는 해당 섹션에 삽입할 JSON 콘텐츠입니다. 

예를 들어 `properties.desired.chiller-water`에 대 한 쌍 경로를 설정한 후 다음 JSON 콘텐츠를 제공할 수 있습니다. 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![쌍 경로 및 콘텐츠 설정](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


전체 쌍 경로를 지정 하 고 대괄호 없이 값을 제공 하 여 개별 설정을 지정할 수도 있습니다. 예를 들어 쌍 경로 `properties.desired.chiller-water.temperature`를 사용 하 여 콘텐츠를 `66`로 설정 합니다. 그런 다음 압력 속성에 대해 새 쌍 설정을 만듭니다. 

두 개 이상의 구성이 동일한 쌍 경로를 대상으로 하는 경우 우선 순위가 가장 높은 구성의 콘텐츠가 적용 됩니다 (우선 순위는 4 단계에서 정의 됨).

기존 속성을 제거 하려는 경우 `null`속성 값을 지정 합니다.

**장치 쌍 설정 추가** 또는 **모듈 쌍 추가**설정을 선택 하 여 추가 설정을 추가할 수 있습니다.

### <a name="specify-metrics-optional"></a>메트릭 지정(선택 사항)

메트릭은 구성 콘텐츠를 적용 한 후 장치 또는 모듈에서 다시 보고할 수 있는 다양 한 상태의 요약 개수를 제공 합니다. 예를 들어 보류 중인 설정 변경에 대한 메트릭, 오류에 대한 메트릭 및 성공적인 설정 변경에 대한 메트릭을 만들 수 있습니다.

각 구성에는 최대 5 개의 사용자 지정 메트릭이 있을 수 있습니다. 

1. **메트릭 이름**에 대한 이름을 입력합니다.

2. **메트릭 조건**에 대한 쿼리를 입력합니다.  쿼리는 보고된 디바이스 쌍 속성을 기준으로 합니다.  메트릭은 쿼리에 의해 반환되는 행 수를 나타냅니다.

다음은 그 예입니다.

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

구성이 적용된 절을 포함할 수 있습니다. 예를 들면 다음과 같습니다. 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

구성 된 모듈에 대해 보고할 메트릭을 작성 하는 경우 `devices.modules`에서 `moduleId`를 선택 합니다. 다음은 그 예입니다.

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>대상 디바이스

이 구성을 받아야 하는 특정 장치 또는 모듈을 대상으로 지정 하려면 쌍의 tags 속성을 사용 합니다. 또한 보고 된 쌍의 속성을 대상으로 지정할 수 있습니다.

자동 장치 구성은 장치 쌍 태그를 대상으로 지정할 수 있으며 자동 모듈 구성은 모듈 쌍 태그를 대상 으로만 지정할 수 있습니다. 

여러 구성에서 동일한 장치 또는 모듈을 대상으로 할 수 있으므로 각 구성에 우선 순위 번호가 필요 합니다. 충돌하는 경우 우선 순위가 가장 높은 구성이 먼저 적용됩니다. 

1. 구성 **우선 순위**에 대해 양의 정수를 입력합니다. 가장 높은 숫자 값이 우선 순위가 가장 높은 것으로 간주됩니다. 두 구성의 우선 순위 번호가 동일한 경우 가장 최근에 만든 구성이 먼저 적용됩니다. 

2. **대상 조건을** 입력 하 여이 구성의 대상으로 지정할 장치 또는 모듈을 결정 합니다. 조건은 쌍 태그 또는 쌍으로 보고 된 속성을 기반으로 하며 식 형식과 일치 해야 합니다. 

   자동 장치 구성의 경우 태그 또는 보고 된 속성만 대상으로 지정할 수 있습니다. 예를 들어 `tags.environment='test'` 또는 `properties.reported.chillerProperties.model='4000x'`입니다. 모든 디바이스를 대상으로 지정하도록 `*`를 지정할 수 있습니다. 
   
   자동 모듈 구성의 경우 쿼리를 사용 하 여 IoT hub에 등록 된 모듈에서 태그 또는 보고 된 속성을 지정 합니다. 예를 들어 `from devices.modules where tags.environment='test'` 또는 `from devices.modules where properties.reported.chillerProperties.model='4000x'`입니다. 와일드 카드를 사용 하 여 모든 모듈을 대상으로 지정할 수는 없습니다. 

3. **다음**을 선택하여 최종 단계로 이동합니다.

### <a name="review-configuration"></a>구성 검토

구성 정보를 검토한 다음, **제출**을 선택합니다.

## <a name="monitor-a-configuration"></a>구성 모니터링

구성의 세부 정보를 확인하고 이를 실행하는 디바이스를 모니터링하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다. 

2. **IoT 디바이스 구성**을 선택합니다.

3. 구성 목록을 검사합니다. 각 구성의 경우 다음 세부 정보를 볼 수 있습니다.

   * **ID** - 구성의 이름입니다.

   * **대상 조건** -대상 장치 또는 모듈을 정의 하는 데 사용 되는 쿼리입니다.

   * **우선 순위** - 구성에 할당된 우선 순위 번호입니다.

   * **만든 시간** - 구성을 만든 시기의 타임스탬프입니다. 이 타임스탬프는 두 구성의 우선 순위가 동일한 경우 연결을 중단하는 데 사용됩니다. 

   * **시스템 메트릭** - IoT Hub에 의해 계산되며 개발자가 사용자 지정할 수 없는 메트릭입니다. 대상 지정은 대상 조건과 일치하는 디바이스 쌍의 수를 지정합니다. 구성에 의해 수정된 디바이스 쌍의 수가 지정되어 적용됩니다. 여기에는 별도의 우선 순위가 높은 구성에서 변경이 발생하는 경우의 부분적인 수정이 포함될 수 있습니다. 

   * **사용자 지정 메트릭** -개발자가 보고 한 쌍의 보고 된 속성에 대 한 쿼리로 지정 된 메트릭입니다.  구성마다 최대 5개의 사용자 지정 메트릭을 정의할 수 있습니다. 
   
4. 모니터링하려는 구성을 선택합니다.  

5. 구성 세부 정보를 검사합니다. 탭을 사용하여 구성을 받은 디바이스에 대한 특정 세부 정보를 볼 수 있습니다.

   * **대상 조건** -대상 조건과 일치 하는 장치 또는 모듈입니다. 

   * **메트릭** - 시스템 메트릭 및 사용자 지정 메트릭의 목록입니다.  드롭다운 목록에서 메트릭을 선택한 다음 **장치 보기** 또는 **모듈 보기**를 선택 하 여 각 메트릭에 대해 계산 되는 장치 또는 모듈 목록을 볼 수 있습니다.

   * **장치 쌍 설정** 또는 **모듈 쌍 설정** -구성에서 설정 하는 쌍 설정입니다. 

   * **구성 레이블** - 구성을 설명하는 데 사용되는 키-값 쌍입니다.  레이블은 기능에 영향을 미치지 않습니다. 

## <a name="modify-a-configuration"></a>구성 수정

구성을 수정 하는 경우 변경 내용은 대상으로 지정 된 모든 장치 또는 모듈에 즉시 복제 됩니다. 

대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

* 쌍이 이전 대상 조건을 충족 하지 않았지만 새 대상 조건을 충족 하 고이 구성이 해당 쌍의 가장 높은 우선 순위 이면이 구성이 적용 됩니다. 

* 현재이 구성을 실행 중인 쌍이 더 이상 대상 조건을 충족 하지 않는 경우 구성의 설정이 제거 되 고 다음으로 가장 높은 우선 순위 구성에 의해 쌍이 수정 됩니다. 

* 현재이 구성을 실행 하는 쌍이 더 이상 대상 조건을 충족 하지 않고 다른 구성의 대상 조건을 충족 하지 않는 경우 구성의 설정이 제거 되 고 쌍에 대 한 다른 변경 내용은 제거 되지 않습니다. 

구성을 수정하려면 다음 단계를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다. 

2. **IoT 디바이스 구성**을 선택합니다. 

3. 수정하려는 구성을 선택합니다. 

4. 다음 필드를 업데이트합니다. 

   * 대상 조건 
   * 레이블 
   * 우선 순위 
   * 메트릭

4. **저장**을 선택합니다.

5. [구성 모니터링](#monitor-a-configuration)의 단계에 따라 변경 내용이 롤아웃되는지 확인합니다. 

## <a name="delete-a-configuration"></a>구성 삭제

구성을 삭제하면 모든 디바이스 쌍은 다음으로 가장 높은 우선 순위 구성을 적용합니다. 디바이스 쌍이 다른 구성의 대상 조건을 충족하지 않는 경우에는 다른 설정이 적용됩니다. 

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다. 

2. **IoT 디바이스 구성**을 선택합니다. 

3. 확인란을 사용하여 삭제하려는 구성을 선택합니다. 

4. **삭제**를 선택합니다.

5. 메시지에서 확인을 요청합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 대규모 IoT 장치를 구성 하 고 모니터링 하는 방법을 배웠습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [대량으로 IoT Hub 디바이스 ID 관리](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 메트릭](iot-hub-metrics.md)
* [작업 모니터링](iot-hub-operations-monitoring.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)

IoT Hub Device Provisioning Service를 사용하여 무인 Just-In-Time 프로비저닝을 수행하는 방법을 알아보려면 다음을 참조하세요. 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
