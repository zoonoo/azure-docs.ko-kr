---
title: Azure Digital Twins의 데이터 처리 및 사용자 정의 함수 | Microsoft Docs
description: Azure Digital Twins의 데이터 처리, 검사기 및 사용자 정의 함수의 개요
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 2703778cd2eab582a9e7311aaf2024f100261889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624525"
---
# <a name="data-processing-and-user-defined-functions"></a>데이터 처리 및 사용자 정의 함수

Azure Digital Twins는 고급 계산 기능을 제공합니다. 개발자는 들어오는 원격 분석 메시지에 대해 사용자 지정 함수를 정의하고 실행하여 미리 정의된 엔드포인트에 이벤트를 보낼 수 있습니다.

## <a name="data-processing-flow"></a>데이터 처리 흐름

디바이스에서 원격 분석 데이터를 Azure Digital Twins에 보내면 개발자는 유효성 검사, 일치, 계산, 디스패치의 네 개 단계로 데이터를 처리할 수 있습니다.

![Azure Digital Twins 데이터 처리 흐름][1]

1. 유효성 검사 단계에서는 들어오는 원격 분석 메시지를 일반적으로 이해되는 [데이터 전송 개체](https://en.wikipedia.org/wiki/Data_transfer_object)형식으로 변환합니다. 이 단계에서는 디바이스 및 센서 유효성 검사도 실행합니다.
1. 일치 단계는 실행할 적절한 UDF(사용자 정의 함수)를 찾습니다. 미리 정의된 검사기가 들어오는 원격 분석 메시지의 디바이스, 센서 및 공간 정보를 기반으로 UDF를 찾습니다.
1. 계산 단계는 이전 단계에서 일치한 UDF를 실행합니다. 이러한 함수는 공간 그래프 노드에서 계산된 값을 읽고 업데이트할 수 있으며 사용자 지정 알림을 내보낼 수 있습니다.
1. 디스패치 단계는 계산 단계에서 그래프에 정의된 엔드포인트로 사용자 지정 알림을 라우팅합니다.

## <a name="data-processing-objects"></a>데이터 처리 개체

Azure Digital Twins의 데이터 처리는 검사기, 사용자 정의 함수 및 역할 할당의 세 개 개체로 구성됩니다.

![Azure Digital Twins 데이터 처리 개체][2]

### <a name="matchers"></a>검사기

검사기는 들어오는 센서 원격 분석을 기반으로 수행할 작업을 평가하는 조건 집합을 정의합니다. 일치를 판별하기 위한 조건에는 센서의 속성, 센서의 상위 디바이스, 센서의 상위 공간이 포함될 수 있습니다. 조건은 이 예제에 설명된 대로 [JSON 경로](http://jsonpath.com/)에 대한 비교로 표현됩니다.

- **온도** 데이터 형식의 모든 센서입니다.
- 포트에 `01`이 있습니다.
- `"GoodCorp"` 값으로 설정된 확장 속성 키 **제조업체**가 있는 디바이스에 속합니다.
- `"Venue"` 유형의 공간에 속합니다.
- 상위 **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`의 하위 항목입니다.

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON 경로는 대/소문자를 구분합니다.
> - JSON 페이로드는 다음에 의해 반환되는 페이로드와 동일합니다.
>   - 센서의 `/sensors/{id}?includes=properties,types`
>   - 센서의 상위 장치의 `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`
>   - 센서의 상위 공간의 `/spaces/{id}?includes=properties,types,location,timezone`
> - 모든 비교는 대/소문자를 구분하지 않습니다.

### <a name="user-defined-functions"></a>사용자 정의 함수

사용자 정의 함수는 Azure Digital Twins의 격리된 환경 내에서 실행되는 사용자 지정 함수입니다. UDF는 수신된 원시 센서 원격 분석 메시지에 액세스할 수 있습니다. UDF는 공간 그래프 및 디스패처 서비스에 액세스할 수도 있습니다. UDF가 그래프 내에 등록되면 UDF를 실행할 시기를 지정하도록 검사기(위에서 자세히 설명)를 만들어야 합니다. Azure Digital Twins가 지정 센서에서 새 원격 분석을 수신하는 경우 일치하는 UDF는 마지막 몇 개의 센서 값에 대한 이동 평균을 계산할 수 있습니다.

UDF는 JavaScript로 작성할 수 있습니다. 개발자는 센서 원격 분석 메시지에 대해 사용자 지정 코드 조각을 실행할 수 있습니다. 도우미 메서드는 사용자 정의 실행 환경에서 그래프와 상호 작용합니다. UDF를 사용하면 개발자는 다음을 수행할 수 있습니다.

- 센서 값을 그래프 내의 센서 개체에 직접 설정합니다.
- 다양한 센서 값을 기반으로 그래프의 공간 내에서 작업을 수행합니다.
- 들어오는 센서 값에 대해 특정 조건이 충족되면 알림을 생성합니다.
- 알림을 전송하기 전에 센서 값에 그래프 메타데이터를 첨부합니다.

자세한 내용은 [사용자 정의 함수를 사용하는 방법](how-to-user-defined-functions.md)을 참조하세요.

### <a name="role-assignment"></a>역할 할당

UDF의 작업은 서비스 내의 데이터를 보호하는 Azure Digital Twins의 역할 기반 액세스 제어에 따릅니다. 역할 할당은 지정된 UDF에 공간 그래프와 상호 작용할 수 있는 적절한 권한이 있는지 확인합니다. 예를 들어, UDF는 지정된 공간에서 그래프 데이터를 생성, 읽기, 업데이트 또는 삭제할 수 있습니다. UDF의 액세스 수준은 UDF가 데이터에 대한 그래프를 요청하거나 작업을 시도할 때 검사됩니다. 자세한 내용은 [역할 기반 액세스 제어](security-create-manage-role-assignments.md)를 참조하세요.

검사기가 역할 할당이 없는 UDF를 트리거할 수 있습니다. 이 경우 UDF는 그래프에서 데이터를 읽을 수 없습니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 및 원격 분석 메시지를 다른 Azure 서비스로 라우팅하는 방법에 대한 자세한 내용은 [이벤트 및 메시지 라우팅](concepts-events-routing.md)을 참조하세요.

* 검사기, 사용자 정의 함수 및 역할 할당을 생성하는 방법에 대한 자세한 내용은 [Guide for using user-defined functions](how-to-user-defined-functions.md)(사용자 정의 함수 사용 가이드)를 참조하세요.

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
