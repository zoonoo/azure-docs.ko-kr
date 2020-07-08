---
title: 사용자 지정 모델
titleSuffix: Azure Digital Twins
description: Azure Digital Twins가 사용자 환경에서 엔터티를 설명 하는 데 사용자 정의 모델을 사용 하는 방식을 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: a1bb07ee900622b138a556a33469641e4e8310af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392304"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Azure Digital Twins의 쌍 모델 이해

Azure Digital Twins의 주요 특징은 고유한 어휘를 정의 하 고 자체 정의 된 비즈니스 조건에서 쌍 그래프를 작성 하는 기능입니다. 이 기능은 사용자 정의 **모델**을 통해 제공 됩니다. 모델을 전 세계 설명의 명사로 간주할 수 있습니다. 

모델은 개체 지향 프로그래밍 언어의 **클래스** 와 비슷하며 실제 작업 환경에서 하나의 특정 개념에 대 한 데이터 셰이프를 정의 합니다. 모델에는 이름 (예: *Room* 또는 *TemperatureSensor*)이 있고 속성, 원격 분석/이벤트, 환경에서이 유형의 엔터티를 설명 하는 명령 등의 요소가 포함 되어 있습니다. 나중에 이러한 모델을 사용 하 여이 유형 설명을 충족 하는 특정 엔터티를 나타내는 [**digital 쌍**](concepts-twins-graph.md) 를 만듭니다.

모델은 JSON-LD 기반의 **DTDL (디지털 쌍 정의 언어)** 을 사용 하 여 작성 됩니다.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>모델 작성을 위한 DTDL (디지털 쌍 정의 언어)

Azure Digital Twins의 모델은 DTDL (디지털 Twins 정의 언어)를 사용 하 여 정의 됩니다. DTDL은 JSON을 기반으로 하며 프로그래밍 언어와 독립적입니다. DTDL은 Azure 디지털 쌍에만 국한 되는 것이 아니라 [iot 플러그 앤 플레이](../iot-pnp/overview-iot-plug-and-play.md)와 같은 다른 iot 서비스에서 장치 데이터를 표시 하는 데도 사용 됩니다. Azure Digital Twins는 DTDL *버전 2*를 사용 합니다.

> [!TIP] 
> DTDL을 사용 하는 모든 서비스는 동일한 DTDL 기능을 구현 하지 않습니다. 예를 들어 IoT 플러그 앤 플레이는 그래프에 해당 하는 DTDL 기능을 사용 하지 않지만 Azure Digital Twins는 현재 DTDL 명령을 구현 하지 않습니다. Azure Digital Twins와 관련 된 DTDL 기능에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 [Azure Digital TWINS dtdl 구현 세부](#azure-digital-twins-dtdl-implementation-specifics)사항 섹션을 참조 하세요.

일반적으로 DTDL에 대 한 자세한 내용은 GitHub의 spec 설명서 [(dtdl)-버전 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 참조 하세요.

## <a name="elements-of-a-model"></a>모델 요소

모델 정의 내에서 최상위 코드 항목은 **인터페이스**입니다. 이는 전체 모델을 캡슐화 하 고 모델의 나머지는 인터페이스 내에서 정의 됩니다. 

DTDL 모델 인터페이스에는 다음의 각 필드를 0 개, 1 개 또는 여러 개 포함할 수 있습니다.
* **속성** -속성은 엔터티의 상태를 나타내는 데이터 필드입니다 (예: 많은 개체 지향 프로그래밍 언어의 속성). 시간 제한 데이터 이벤트 인 원격 분석과 달리 속성은 백업 저장소를 포함 하며 언제 든 지 읽을 수 있습니다.
* **원격 분석** -원격 분석 필드는 측정값이 나 이벤트를 나타내며 장치 센서 판독값을 설명 하는 데 종종 사용 됩니다. 원격 분석은 디지털 쌍에 저장 되지 않습니다. 데이터 이벤트의 스트림을 어딘가에 보낼 준비가 된 것과 같습니다. 
* **구성** 요소 구성 요소를 사용 하면 원하는 경우 모델 인터페이스를 다른 인터페이스의 어셈블리로 빌드할 수 있습니다. 구성 요소의 예로는 *휴대폰*에 대 한 모델을 정의 하는 데 사용 되는 *frontCamera* 인터페이스 (및 다른 구성 요소 인터페이스 *백 카메라*)가 있습니다. 먼저 자체 모델 처럼 *frontCamera* 에 대 한 인터페이스를 정의한 다음 *휴대폰*을 정의할 때 참조할 수 있습니다.

    구성 요소를 사용 하 여 솔루션의 핵심 부분 이지만 별도의 id가 필요 하지 않은 항목을 설명할 수 있으며 쌍으로 된 쌍으로 생성, 삭제 또는 다시 정렬할 필요가 없습니다. 쌍이 쌍으로 연결 된 쌍을 쌍으로 연결 하려면 서로 다른 모델의 개별 디지털 쌍으로 연결 하 여 *관계* (다음 글머리 기호 참조)를 표시 합니다.
    
    >[!TIP] 
    >구성 요소를 사용 하 여 모델 인터페이스 내에서 관련 된 속성 집합을 그룹화 할 수도 있습니다. 이 경우 각 구성 요소를 인터페이스 내의 네임 스페이스 또는 "폴더"로 간주할 수 있습니다.
* **관계** -관계를 사용 하면 디지털 쌍이 다른 디지털 쌍과 관련 되는 방식을 나타낼 수 있습니다. 관계는 *contains* ("floor 포함 방"), *사항이* ("hvac 사항이 대화방"), *isBilledTo* ("압축기가 사용자에 게 청구 됨") 등 다양 한 의미 체계를 나타낼 수 있습니다. 관계를 통해 솔루션은 상호 관련 엔터티의 그래프를 제공할 수 있습니다.

> [!NOTE]
> DTDL의 사양에서는 디지털 쌍 (예: 리셋 명령 또는 팬을 전환 하는 명령)에서 실행할 수 있는 메서드인 **명령도**정의 합니다. 그러나 *Azure Digital Twins에서는 현재 명령이 지원 되지 않습니다.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins DTDL 구현 세부 사항

DTDL 모델은 Azure Digital Twins와 호환 되려면 이러한 요구 사항을 충족 해야 합니다.

* 모델의 모든 최상위 DTDL 요소는 *인터페이스*형식 이어야 합니다. Azure Digital Twins 모델 Api는 인터페이스 또는 인터페이스 배열을 나타내는 JSON 개체를 받을 수 있기 때문입니다. 그 결과, 최상위 수준에서 다른 DTDL 요소 형식이 허용 되지 않습니다.
* Azure Digital Twins 용 DTDL은 *명령을*정의 하지 않아야 합니다.
* Azure Digital Twins는 단일 수준의 구성 요소 중첩만 허용 합니다. 즉, 구성 요소로 사용 되는 인터페이스는 구성 요소를 포함할 수 없습니다. 
* 인터페이스는 다른 DTDL 인터페이스 내에서 인라인으로 정의 될 수 없습니다. 자체 Id를 사용 하 여 별도의 최상위 엔터티로 정의 해야 합니다. 그런 다음 다른 인터페이스가 해당 인터페이스를 구성 요소나 상속을 통해 포함 하려는 경우 해당 ID를 참조할 수 있습니다.

## <a name="example-model-code"></a>예제 모델 코드

쌍 형식 모델은 텍스트 편집기로 작성할 수 있습니다. DTDL 언어는 JSON 구문을 따릅니다. *따라서 확장 프로그램*을 사용 하 여 모델을 저장 해야 합니다. JSON 확장을 사용 하면 많은 프로그래밍 텍스트 편집기에서 DTDL 문서에 대 한 기본 구문 검사 및 강조 표시를 제공할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)에 사용할 수 있는 [dtdl 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) 도 있습니다.

다음은 DTDL 인터페이스로 작성 된 일반적인 모델의 예입니다. 모델은 각각 이름, 질량 및 온도를 포함 하는 행성을 설명 합니다. 행성은 위성 moons 수 있으며 craters를 포함할 수 있습니다.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

모델의 필드는 다음과 같습니다.

| 필드 | 설명 |
| --- | --- |
| `@id` | 모델에 대 한 식별자입니다. 형식 이어야 합니다 `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | 설명 하는 정보의 종류를 식별 합니다. 인터페이스의 경우 형식은 *interface*입니다. |
| `@context` | JSON 문서에 대 한 [컨텍스트](https://niem.github.io/json/reference/json-ld/context/) 를 설정 합니다. 모델은를 사용 해야 `dtmi:dtdl:context;2` 합니다. |
| `displayName` | 필드 원하는 경우 모델에 이름을 지정할 수 있습니다. |
| `contents` | 나머지 모든 인터페이스 데이터는 특성 정의의 배열로 여기에 배치 됩니다. 각 특성은 `@type` (*속성*, *원격 분석*, *명령*, *관계*또는 *구성 요소*)를 제공 하 여 설명 하는 인터페이스 정보의 종류를 식별 한 다음 실제 특성을 정의 하는 속성 집합 (예: `name` 및 `schema` *속성*을 정의 하는)을 제공 해야 합니다. |

> [!NOTE]
> 구성 요소 인터페이스 (이 예제에서는*Crater* )는이 인터페이스 (*행성*)를 사용 하는 인터페이스와 동일한 배열에 정의 되어 있습니다. 인터페이스를 찾기 위해 API 호출에서 구성 요소를 이러한 방식으로 정의 해야 합니다.

### <a name="possible-schemas"></a>가능한 스키마

Dtdl 당 *속성* 및 *원격 분석* 특성의 스키마는 표준 기본 유형인 `integer` ,, 및와 `double` `string` `Boolean` 같은 기타 유형 `DateTime` `Duration` (예:,, 및)이 될 수 있습니다. 

기본 형식 외에도 *속성* 및 *원격 분석* 필드에는 다음과 같은 복합 형식이 있을 수 있습니다.
* `Object`
* `Map`
* `Enum`

*원격 분석* 필드는도 지원 `Array` 합니다.

### <a name="model-inheritance"></a>모델 상속

경우에 따라 모델을 더 특수화 하는 것이 좋습니다. 예를 들어, 일반 모델 *대화방*및 특수화 된 변형 *ConferenceRoom* 및 *체육관*를 포함 하는 것이 유용할 수 있습니다. 특수화를 표현 하기 위해 DTDL은 상속을 지원 합니다. 인터페이스는 하나 이상의 다른 인터페이스에서 상속할 수 있습니다. 

다음 예에서는 이전 DTDL 예의 *행성* 모델을 더 큰 *CelestialBody* 모델의 하위 형식으로 다시 imagines 합니다. "Parent" 모델은 먼저 정의 된 다음 필드를 사용 하 여 "자식" 모델이 작성 합니다 `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

이 예제에서 *CelestialBody* 은 이름, 질량 및 온도 *에 대 한 온도를 제공*합니다. 섹션은 인터페이스 `extends` 이름 이거나 인터페이스 이름 배열입니다 (원하는 경우 확장 인터페이스가 여러 부모 모델에서 상속할 수 있도록 허용).

상속이 적용 되 면 확장 인터페이스는 전체 상속 체인의 모든 속성을 노출 합니다.

확장 인터페이스는 부모 인터페이스의 정의를 변경할 수 없습니다. 여기에만 추가할 수 있습니다. 또한 기능이 동일 하 게 정의 된 경우에도 부모 인터페이스에 이미 정의 된 기능을 다시 정의할 수 없습니다. 예를 들어 부모 인터페이스가 속성 질량을 정의 하는 경우 `double` 확장 인터페이스는 이기도 한 경우에도 *질량*선언을 포함할 수 없습니다 *mass* `double` .

## <a name="validating-models"></a>모델 유효성 검사

DTDL이 유효한 지 확인 하기 위해 모델 문서의 유효성을 검사 하는 데 사용할 수 있는 샘플이 있습니다. 이 파일은 DTDL 파서 라이브러리를 기반으로 하며 언어를 독립적으로 제공 합니다. [Dtdl Validator 샘플](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)에서 찾을 수 있습니다.

또는 파서 라이브러리에 대 한 자세한 내용은 [방법: 모델 구문 분석 및 유효성 검사](how-to-use-parser.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

DigitalTwinsModels Api를 사용 하 여 모델을 관리 하는 방법을 참조 하세요.
* [방법: 사용자 지정 모델 관리](how-to-manage-model.md)

또는 모델을 기반으로 디지털 쌍을 만드는 방법에 대해 알아봅니다.
* [개념: 디지털 쌍 및 쌍 그래프](concepts-twins-graph.md)

