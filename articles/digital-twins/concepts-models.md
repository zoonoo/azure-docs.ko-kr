---
title: 사용자 지정 모델
titleSuffix: Azure Digital Twins
description: Azure Digital Twins가 사용자 환경에서 엔터티를 설명 하는 데 사용자 정의 모델을 사용 하는 방식을 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0a38f9b8135fed08a95df68f108e44c34fec6325
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955330"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Azure Digital Twins의 쌍 모델 이해

Azure Digital Twins의 주요 특징은 고유한 어휘를 정의 하 고 자체 정의 된 비즈니스 조건에서 쌍 그래프를 작성 하는 기능입니다. 이 기능은 사용자 정의 **모델** 을 통해 제공 됩니다. 모델을 전 세계 설명의 명사로 간주할 수 있습니다. 

모델은 개체 지향 프로그래밍 언어의 **클래스** 와 비슷하며 실제 작업 환경에서 하나의 특정 개념에 대 한 데이터 셰이프를 정의 합니다. 모델에는 이름 (예: *Room* 또는 *TemperatureSensor*)이 있고 속성, 원격 분석/이벤트, 환경에서이 유형의 엔터티를 설명 하는 명령 등의 요소가 포함 되어 있습니다. 나중에 이러한 모델을 사용 하 여이 유형 설명을 충족 하는 특정 엔터티를 나타내는 [**digital 쌍**](concepts-twins-graph.md) 를 만듭니다.

Azure Digital Twins 모델은 JSON-LD 기반의 **DTDL (디지털 쌍 정의 언어)** 로 표시 됩니다.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>모델에 대 한 DTDL (디지털 쌍 정의 언어)

Azure Digital Twins의 모델은 DTDL (디지털 Twins 정의 언어)를 사용 하 여 정의 됩니다. DTDL은 JSON을 기반으로 하며 프로그래밍 언어와 독립적입니다. DTDL은 Azure 디지털 쌍에만 국한 되는 것이 아니라 [iot 플러그 앤 플레이](../iot-pnp/overview-iot-plug-and-play.md)와 같은 다른 iot 서비스에서 장치 데이터를 표시 하는 데도 사용 됩니다. 

Azure Digital Twins는 **Dtdl _버전 2_** 를 사용 합니다. 이 DTDL 버전에 대 한 자세한 내용은 GitHub의 사양 설명서 [*(dtdl)-버전 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 참조 하세요. 이제 Azure Digital Twins에서 DTDL _버전 1_ 을 사용 하는 것은 더 이상 사용 되지 않습니다.

> [!NOTE] 
> DTDL을 사용 하는 모든 서비스는 동일한 DTDL 기능을 구현 하지 않습니다. 예를 들어 IoT 플러그 앤 플레이는 그래프에 해당 하는 DTDL 기능을 사용 하지 않지만 Azure Digital Twins는 현재 DTDL 명령을 구현 하지 않습니다.
>
> Azure Digital Twins와 관련 된 DTDL 기능에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 [Azure Digital TWINS dtdl 구현 세부](#azure-digital-twins-dtdl-implementation-specifics)사항 섹션을 참조 하세요.

## <a name="elements-of-a-model"></a>모델 요소

모델 정의 내에서 최상위 코드 항목은 **인터페이스** 입니다. 이는 전체 모델을 캡슐화 하 고 모델의 나머지는 인터페이스 내에서 정의 됩니다. 

DTDL 모델 인터페이스에는 다음의 각 필드를 0 개, 1 개 또는 여러 개 포함할 수 있습니다.
* **속성** -속성은 엔터티의 상태를 나타내는 데이터 필드입니다 (예: 많은 개체 지향 프로그래밍 언어의 속성). 속성은 백업 저장소를 포함 하며 언제 든 지 읽을 수 있습니다.
* **원격 분석** -원격 분석 필드는 측정값이 나 이벤트를 나타내며 장치 센서 판독값을 설명 하는 데 종종 사용 됩니다. 속성과 달리 원격 분석은 디지털 쌍에 저장 되지 않습니다. 처리 해야 하는 일련의 시간 바인딩 데이터 이벤트입니다. 속성과 원격 분석 간의 차이점에 대 한 자세한 내용은 아래의 [*속성 및 원격 분석*](#properties-vs-telemetry) 섹션을 참조 하십시오.
* **구성** 요소 구성 요소를 사용 하면 원하는 경우 모델 인터페이스를 다른 인터페이스의 어셈블리로 빌드할 수 있습니다. 구성 요소의 예로는 *휴대폰* 에 대 한 모델을 정의 하는 데 사용 되는 *frontCamera* 인터페이스 (및 다른 구성 요소 인터페이스 *백 카메라*)가 있습니다. 먼저 자체 모델 처럼 *frontCamera* 에 대 한 인터페이스를 정의한 다음 *휴대폰* 을 정의할 때 참조할 수 있습니다.

    구성 요소를 사용 하 여 솔루션의 핵심 부분 이지만 별도의 id가 필요 하지 않은 항목을 설명할 수 있으며 쌍으로 된 쌍으로 생성, 삭제 또는 다시 정렬할 필요가 없습니다. 쌍이 쌍으로 연결 된 쌍을 쌍으로 연결 하려면 서로 다른 모델의 개별 디지털 쌍으로 연결 하 여 *관계* (다음 글머리 기호 참조)를 표시 합니다.
    
    >[!TIP] 
    >구성 요소를 사용 하 여 모델 인터페이스 내에서 관련 된 속성 집합을 그룹화 할 수도 있습니다. 이 경우 각 구성 요소를 인터페이스 내의 네임 스페이스 또는 "폴더"로 간주할 수 있습니다.
* **관계** -관계를 사용 하면 디지털 쌍이 다른 디지털 쌍과 관련 되는 방식을 나타낼 수 있습니다. 관계는 *contains* ("floor 포함 방"), *사항이* ("hvac 사항이 대화방"), *isBilledTo* ("압축기가 사용자에 게 청구 됨") 등 다양 한 의미 체계를 나타낼 수 있습니다. 관계를 통해 솔루션은 상호 관련 엔터티의 그래프를 제공할 수 있습니다.

> [!NOTE]
> [DTDL의 사양](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 에서는 디지털 쌍 (예: 리셋 명령 또는 팬을 전환 하는 명령)에서 실행할 수 있는 메서드인 **명령도** 정의 합니다. 그러나 *Azure Digital Twins에서는 현재 명령이 지원 되지 않습니다.*

### <a name="properties-vs-telemetry"></a>속성 및 원격 분석

Azure Digital Twins의 DTDL **속성과** **원격 분석** 필드를 구분 하는 몇 가지 추가 지침은 다음과 같습니다.

Azure Digital Twins 모델에 대 한 속성 및 원격 분석의 차이점은 다음과 같습니다.
* **속성** 에는 백업 저장소가 있어야 합니다. 즉, 언제 든 지 속성을 읽고 해당 값을 검색할 수 있습니다. 속성에 쓸 수 있으면 속성에 값을 저장할 수도 있습니다.  
* **원격 분석** 은 이벤트 스트림과 매우 유사 합니다. short lifespans 있는 데이터 메시지 집합입니다. 이벤트가 발생할 때 수행할 작업에 대 한 수신 대기를 설정 하지 않은 경우에는 나중에 이벤트를 추적 하지 않습니다. 다시 돌아가서 나중에 읽을 수 없습니다. 
  - C # 용어로 원격 분석은 c # 이벤트와 비슷합니다. 
  - IoT 용어로 원격 분석은 일반적으로 장치에서 전송 되는 단일 측정입니다.

대부분의 장치는 생성 하는 측정 값을 저장 하거나 관심이 없으므로 IoT 장치에서 **원격 분석** 을 사용 하는 경우가 많습니다. "원격 분석" 이벤트 스트림으로 전송 하기만 하면 됩니다. 이 경우 언제 든 지 원격 분석 필드의 최신 값에 대해 장치를 검색할 수 없습니다. 대신 장치에서 메시지를 수신 대기 하 고 메시지가 도착할 때 동작을 수행 해야 합니다. 

결과적으로, Azure Digital Twins에서 모델을 디자인할 때 대부분의 경우 **속성** 을 사용 하 여 쌍을 모델링할 것입니다. 이를 통해 백업 저장소와 데이터 필드를 읽고 쿼리 하는 기능을 사용할 수 있습니다.

원격 분석 및 속성은 종종 장치에서 수신 되는 데이터를 처리 하기 위해 함께 작동 합니다. Azure Digital Twins로의 모든 수신은 [api](how-to-use-apis-sdks.md)를 통해 발생 하므로 일반적으로 수신 함수를 사용 하 여 장치에서 원격 분석 또는 속성 이벤트를 읽고 ADT의 응답으로 속성을 설정 합니다. 

Azure Digital Twins API에서 원격 분석 이벤트를 게시할 수도 있습니다. 다른 원격 분석과 마찬가지로이 이벤트는를 처리 하는 수신기를 필요로 하는 수명이 짧은 이벤트입니다.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins DTDL 구현 세부 사항

DTDL 모델은 Azure Digital Twins와 호환 되려면 이러한 요구 사항을 충족 해야 합니다.

* 모델의 모든 최상위 DTDL 요소는 *인터페이스* 형식 이어야 합니다. Azure Digital Twins 모델 Api는 인터페이스 또는 인터페이스 배열을 나타내는 JSON 개체를 받을 수 있기 때문입니다. 그 결과, 최상위 수준에서 다른 DTDL 요소 형식이 허용 되지 않습니다.
* Azure Digital Twins 용 DTDL은 *명령을* 정의 하지 않아야 합니다.
* Azure Digital Twins는 단일 수준의 구성 요소 중첩만 허용 합니다. 즉, 구성 요소로 사용 되는 인터페이스는 구성 요소를 포함할 수 없습니다. 
* 인터페이스는 다른 DTDL 인터페이스 내에서 인라인으로 정의 될 수 없습니다. 자체 Id를 사용 하 여 별도의 최상위 엔터티로 정의 해야 합니다. 그런 다음 다른 인터페이스가 해당 인터페이스를 구성 요소나 상속을 통해 포함 하려는 경우 해당 ID를 참조할 수 있습니다.

또한 Azure Digital Twins는 `writable` 속성 또는 관계에 대 한 특성을 관찰 하지 않습니다. 이 값은 DTDL 사양에 따라 설정할 수 있지만 Azure Digital Twins에서 사용 되지 않습니다. 대신 항상 Azure Digital Twins 서비스에 대 한 일반 쓰기 권한이 있는 외부 클라이언트에서 쓰기 가능으로 처리 됩니다.

## <a name="example-model-code"></a>예제 모델 코드

쌍 형식 모델은 텍스트 편집기로 작성할 수 있습니다. DTDL 언어는 JSON 구문을 따릅니다. *따라서 확장 프로그램* 을 사용 하 여 모델을 저장 해야 합니다. JSON 확장을 사용 하면 많은 프로그래밍 텍스트 편집기에서 DTDL 문서에 대 한 기본 구문 검사 및 강조 표시를 제공할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)에 사용할 수 있는 [dtdl 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) 도 있습니다.

이 섹션에는 DTDL 인터페이스로 작성 된 일반적인 모델의 예가 포함 되어 있습니다. 모델은 각각 이름, 질량 및 온도를 포함 하는 **행성** 을 설명 합니다.
 
행성은 위성 **moons** 상호 작용 하 고 **craters** 를 포함할 수도 있습니다. 아래 예제에서 `Planet` 모델은 두 개의 외부 모델 (및)을 참조 하 여 이러한 다른 엔터티에 대 한 연결을 나타냅니다 `Moon` `Crater` . 이러한 모델은 아래 예제 코드에도 정의 되어 있지만 매우 간단 하 게 유지 되므로 기본 예제에서 저하 되지 않습니다 `Planet` .

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
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

모델의 필드는 다음과 같습니다.

| 필드 | Description |
| --- | --- |
| `@id` | 모델에 대 한 식별자입니다. 형식 이어야 합니다 `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | 설명 하는 정보의 종류를 식별 합니다. 인터페이스의 경우 형식은 *interface* 입니다. |
| `@context` | JSON 문서에 대 한 [컨텍스트](https://niem.github.io/json/reference/json-ld/context/) 를 설정 합니다. 모델은를 사용 해야 `dtmi:dtdl:context;2` 합니다. |
| `displayName` | 필드 원하는 경우 모델에 이름을 지정할 수 있습니다. |
| `contents` | 나머지 모든 인터페이스 데이터는 특성 정의의 배열로 여기에 배치 됩니다. 각 특성은 `@type` (*속성*, *원격 분석*, *명령*, *관계* 또는 *구성 요소*)를 제공 하 여 설명 하는 인터페이스 정보의 종류를 식별 한 다음 실제 특성을 정의 하는 속성 집합 (예: `name` 및 `schema` *속성* 을 정의 하는)을 제공 해야 합니다. |

> [!NOTE]
> 구성 요소 인터페이스 (이 예제에서는 *Crater* )는이 인터페이스 (*행성*)를 사용 하는 인터페이스와 동일한 배열에 정의 되어 있습니다. 인터페이스를 찾기 위해 API 호출에서 구성 요소를 이러한 방식으로 정의 해야 합니다.

### <a name="possible-schemas"></a>가능한 스키마

Dtdl 당 *속성* 및 *원격 분석* 특성의 스키마는 표준 기본 유형인 `integer` ,, 및와 `double` `string` `Boolean` 같은 기타 유형 `DateTime` `Duration` (예:,, 및)이 될 수 있습니다. 

기본 형식 외에도 *속성* 및 *원격 분석* 필드에는 다음과 같은 복합 형식이 있을 수 있습니다.
* `Object`
* `Map`
* `Enum`

*원격 분석* 필드는도 지원 `Array` 합니다.

### <a name="model-inheritance"></a>모델 상속

경우에 따라 모델을 더 특수화 하는 것이 좋습니다. 예를 들어, 일반 모델 *대화방* 및 특수화 된 변형 *ConferenceRoom* 및 *체육관* 를 포함 하는 것이 유용할 수 있습니다. 특수화를 표현 하기 위해 DTDL은 상속을 지원 합니다. 인터페이스는 하나 이상의 다른 인터페이스에서 상속할 수 있습니다. 

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

이 예제에서 *CelestialBody* 은 이름, 질량 및 온도 *에 대 한 온도를 제공* 합니다. 섹션은 인터페이스 `extends` 이름 이거나 인터페이스 이름 배열입니다 (원하는 경우 확장 인터페이스가 여러 부모 모델에서 상속할 수 있도록 허용).

상속이 적용 되 면 확장 인터페이스는 전체 상속 체인의 모든 속성을 노출 합니다.

확장 인터페이스는 부모 인터페이스의 정의를 변경할 수 없습니다. 여기에만 추가할 수 있습니다. 또한 기능이 동일 하 게 정의 된 경우에도 부모 인터페이스에 이미 정의 된 기능을 다시 정의할 수 없습니다. 예를 들어 부모 인터페이스가 속성 질량을 정의 하는 경우 `double` 확장 인터페이스는 이기도 한 경우에도 *질량* 선언을 포함할 수 없습니다 *mass* `double` .

## <a name="best-practices-for-designing-models"></a>모델 디자인에 대 한 모범 사례

사용자 환경에서 엔터티를 반영 하도록 모델을 디자인 하는 동안 디자인의 [쿼리](concepts-query-language.md) 의미를 미리 확인 하는 것이 유용할 수 있습니다. 그래프 순회에서 많은 결과 집합을 방지 하는 방식으로 속성을 디자인할 수 있습니다. 단일 쿼리에서 단일 수준 관계로 응답 하는 관계를 모델링할 수도 있습니다.

### <a name="validating-models"></a>모델 유효성 검사

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="integrating-with-industry-standard-models"></a>업계 표준 모델과 통합

업계 표준을 기반으로 하는 모델을 사용 하거나 RDF 또는 OWL와 같은 표준 ontology 표현을 사용 하면 Azure Digital Twins 모델을 디자인할 때 다양 한 시작 지점을 제공 합니다. 산업 모델을 사용 하면 표준화 및 정보 공유에도 도움이 됩니다.

Azure Digital Twins에서 사용 하려면 모델이 JSON-LD 기반 [**디지털 Twins 정의 언어 (DTDL)**](concepts-models.md)로 표시 되어야 합니다. 따라서이 문서에서는 DTDL에서 업계 표준 모델을 표시 하는 방법에 대해 설명 하 고, Azure Digital Twins에서 사용할 수 있도록 기존 업계 개념을 DTDL 의미 체계와 통합 합니다. 그런 다음 DTDL 모델은 Azure Digital Twins 내에서 모델에 대 한 진위의 원본으로 사용 됩니다.

사용자의 상황에 따라 다음과 같은 두 가지 주요 경로를 사용 하 여 DTDL과 함께 산업 표준 모델을 통합할 수 있습니다.
* 모델을 아직 만들지 않은 경우 업계와 관련 된 언어를 포함 하는 **기존 스타터 DTDL 온톨로지** 을 기반으로 모델을 디자인할 수 있습니다.
* 산업 표준을 기반으로 하는 기존 모델이 이미 있는 경우 Azure Digital Twins로 가져오려면 **DTDL로 변환** 해야 합니다.

이러한 두 프로세스에 대 한 자세한 내용은 [*방법: 업계 표준 모델 통합*](how-to-integrate-models.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

DigitalTwinModels Api를 사용 하 여 모델을 관리 하는 방법을 참조 하세요.
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)

또는 모델을 기반으로 디지털 쌍을 만드는 방법에 대해 알아봅니다.
* [*개념: 디지털 쌍 및 쌍 그래프*](concepts-twins-graph.md)

