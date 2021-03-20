---
title: 온톨로지 확장
titleSuffix: Azure Digital Twins
description: Ontology 확장의 이유 및 전략에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561543"
---
# <a name="extending-ontologies"></a>온톨로지 확장 

[Smart 빌딩의 Dtdl 기반 RealEstateCore ontology](https://github.com/Azure/opendigitaltwins-building)와 같은 업계 표준 [ontology](concepts-ontologies.md)IoT 솔루션 빌드를 시작 하는 좋은 방법입니다. 업계 온톨로지는 도메인에 맞게 설계 되었으며 azure IoT 서비스 (예: Azure Digital Twins)에서 사용할 수 있도록 설계 된 풍부한 기본 인터페이스 집합을 제공 합니다. 

그러나 솔루션에 업계 ontology 적용 되지 않는 특정 요구 사항이 있을 수 있습니다. 예를 들어 별도의 시스템에 저장 된 3D 모델에 디지털 쌍을 연결 하는 것이 좋습니다. 이 경우 원래 ontology의 모든 혜택을 유지 하면서 이러한 온톨로지 중 하나를 확장 하 여 고유한 기능을 추가할 수 있습니다.

이 문서에서는 DTDL 기반 [RealEstateCore](https://www.realestatecore.io/) ontology를 새로운 dtdl 속성으로 확장 하는 예제에 대 한 기반으로 사용 합니다. 그러나 여기에 설명 된 기술은 일반적 이지만 모든 DTDL 기능 (원격 분석, 속성, 관계, 구성 요소 또는 명령)을 사용 하 여 DTDL 기반 ontology의 모든 부분에 적용 될 수 있습니다. 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore space 계층 구조 

DTDL 기반 RealEstateCore ontology에서는 공간 계층 구조를 사용 하 여 방, 빌딩, Zone 등 다양 한 종류의 공간을 정의 합니다. 각 모델에서 계층을 확장 하 여 다양 한 종류의 대화방, 건물 및 영역을 정의 합니다. 

계층 구조의 일부는 아래 다이어그램과 같이 보입니다. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="RealEstateCore space 계층 구조의 일부를 보여 주는 흐름 다이어그램입니다. 최상위 수준에는 Space 라는 요소가 있습니다. 이는 한 수준 아래의 ' 확장 ' 화살표로 연결 되어 있습니다. 대화방은 ConferenceRoom 및 Office에 대 한 수준 아래로 두 개의 ' 확장 ' 화살표로 연결 됩니다."::: 

RealEstateCore ontology에 대 한 자세한 내용은 [*개념: 업계 표준 온톨로지 채택*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)을 참조 하세요.

## <a name="extending-the-realestatecore-space-hierarchy"></a>RealEstateCore space 계층 확장 

경우에 따라 솔루션에 업계 ontology 적용 되지 않는 특정 요구 사항이 있습니다. 이 경우 계층을 확장 하면 요구에 맞게 사용자 지정 하는 동안 업계 ontology을 계속 사용할 수 있습니다. 

이 문서에서는 ontology의 계층을 확장 하는 데 유용한 두 가지 다른 사례에 대해 설명 합니다. 

* 업계 ontology에서 개념에 대 한 새 인터페이스를 추가 합니다. 
* 기존 인터페이스에 추가 속성 (또는 관계, 구성 요소, 원격 분석 또는 명령)을 추가 합니다.

### <a name="add-new-interfaces-for-new-concepts"></a>새 개념에 대 한 새 인터페이스 추가 

이 경우 솔루션에 필요한 개념에 대 한 인터페이스를 추가 하려고 하지만 업계 ontology에는 없습니다. 예를 들어, 솔루션에 DTDL 기반 RealEstateCore ontology에 표시 되지 않는 다른 유형의 대화방이 있는 경우 RealEstateCore 인터페이스에서 직접 확장 하 여 추가할 수 있습니다. 

아래 예제에서는 RealEstateCore ontology에 없는 "포커스 방"을 나타내야 하는 솔루션을 제공 합니다. 포커스 공간은 사용자가 한 번에 몇 시간 동안 작업에 집중할 수 있도록 설계 된 작은 공간입니다. 

이 새로운 개념으로 업계 ontology을 확장 하려면 업계 ontology의 인터페이스 [에서 확장](concepts-models.md#model-inheritance) 되는 새 인터페이스를 만듭니다. 

포커스 공간 인터페이스를 추가한 후 확장 계층에는 새로운 방 유형이 표시 됩니다. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="새 추가를 사용 하 여 위에서 RealEstateCore space 계층 구조를 보여 주는 흐름 다이어그램입니다. ConferenceRoom 및 Office의 최하위 수준에는 FocusRoom 라는 새 요소가 있습니다 (대화방의 ' 확장 ' 화살표를 통해서도 연결 됨)."::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>기존 인터페이스에 추가 기능 추가 

이 경우 업계 ontology에 있는 인터페이스에 더 많은 속성 (또는 관계, 구성 요소, 원격 분석 또는 명령)을 추가 하려고 합니다.

이 섹션에는 두 가지 예가 표시 됩니다. 
* 기존 시스템에 이미 있는 공간의 3D 그리기를 표시 하는 솔루션을 빌드하는 경우 솔루션에서 공간에 대 한 정보를 표시할 때 기존 시스템에서 3D 그리기를 검색할 수도 있도록 각 디지털 쌍을 3D 그리기에 연결 하는 것이 좋습니다 (ID). 
* 솔루션에서 회의실의 온라인/오프 라인 상태를 추적 해야 하는 경우 표시 또는 쿼리에 사용할 회의실 상태를 추적 하는 것이 좋습니다. 

두 예제 모두 새 속성을 사용 하 여 구현할 수 있습니다. `drawingId` 3d 그리기를 디지털 쌍에 연결 하는 속성과 회의실의 온라인 상태 인지 여부를 나타내는 "온라인" 속성을 사용 하 여 구현할 수 있습니다. 

일반적으로 업계 ontology는 나중에 솔루션에 업데이트를 통합할 수 있기 때문에 직접 수정 하지 않는 것이 좋습니다 (추가 사항을 덮어씀). 대신 DTDL 기반 RealEstateCore ontology에서 확장 되는 고유한 인터페이스 계층 구조에서 이러한 종류의 추가 작업을 수행할 수 있습니다. 사용자가 만드는 각 인터페이스는 여러 인터페이스 상속을 사용 하 여 확장 된 인터페이스 계층에서 부모 RealEstateCore 인터페이스 및 해당 부모 인터페이스를 확장 합니다. 이 접근 방식을 사용 하면 업계 ontology 및 추가를 함께 사용할 수 있습니다. 

산업 ontology을 확장 하려면 업계 ontology의 인터페이스에서 확장 되는 고유한 인터페이스를 만들고 확장 된 인터페이스에 새 기능을 추가 합니다. 확장 하려는 각 인터페이스에 대해 새 인터페이스를 만듭니다. 확장 된 인터페이스는 DTDL로 작성 됩니다 (이 문서의 뒷부분에 나오는 확장 인터페이스에 대 한 DTDL 참조). 

위에 표시 된 계층의 일부를 확장 한 후 확장 된 계층 구조는 아래 다이어그램과 같습니다. 여기서 확장 된 공간 인터페이스는 디지털 쌍을 `drawingId` 3d 드로잉과 연결 하는 ID를 포함 하는 속성을 추가 합니다. 또한 ConferenceRoom 인터페이스는 회의실의 온라인 상태를 포함 하는 "온라인" 속성을 추가 합니다. 상속을 통해 ConferenceRoom 인터페이스에는 RealEstateCore ConferenceRoom 인터페이스의 모든 기능 뿐만 아니라 확장 된 공간 인터페이스의 모든 기능이 포함 됩니다. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="위의 확장 된 RealEstateCore 공간 계층 구조를 보여 주는 흐름 다이어그램은 새로 추가 되었습니다. 이제 대화방은 해당 수준을 공간 요소와 공유 하며,이 요소는 수준 아래의 ' 확장 ' 화살표를 사용 하 여 ConferenceRoom 및 Office 옆의 새 Room 요소에 연결 합니다.  새 요소는 더 많은 ' 확장 ' 관계를 사용 하 여 기존 ontology에 연결 됩니다."::: 

## <a name="using-the-extended-space-hierarchy"></a>확장 된 공간 계층 구조 사용 

확장 된 공간 계층 구조를 사용 하 여 디지털 쌍을 만드는 경우 각 디지털 쌍의 모델은 확장 된 공간 계층 구조 (원래 업계 ontology는 아님)의 모델 이며, 업계 ontology의 모든 기능과 인터페이스 상속을 통해 확장 된 인터페이스를 포함 합니다.

각 디지털 쌍의 모델은 아래 다이어그램에 표시 된 확장 계층의 인터페이스입니다. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Space (최상위), 대화방 (중간 수준), ConferenceRoom, Office 및 FocusRoom (하위 수준)를 포함 하 여 확장 된 RealEstateCore space 계층 구조에서 발췌 한 것입니다. 모델의 이름은 각 요소에 연결 됩니다. 예를 들어 대화방은 Room101 라는 모델에 연결 됩니다."::: 

모델 ID (연산자)를 사용 하 여 디지털 쌍에 대해 쿼리 하는 경우 `IS_OF_MODEL` 확장 된 계층의 모델 id를 사용 해야 합니다. 예: `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')` 

## <a name="contributing-back-to-the-original-ontology"></a>원래 ontology 다시 기여 

경우에 따라 대부분의 사용자에 게 널리 유용한 방식으로 업계 ontology을 확장 하 게 됩니다. 이 경우에는 확장을 원래 ontology 다시 참여 하는 것을 고려해 야 합니다. 각 ontology는 서로 다른 참여 프로세스를 포함 하므로 ontology의 GitHub 리포지토리에서 기여 정보를 확인 합니다. 

## <a name="dtdl-for-new-interfaces"></a>새 인터페이스에 대 한 DTDL 

산업 ontology에서 직접 확장 되는 새 인터페이스에 대 한 DTDL은 다음과 같습니다. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>확장 인터페이스에 대 한 DTDL 

위에 설명 된 부분으로 제한 된 확장 된 인터페이스에 대 한 DTDL은 다음과 같습니다. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>다음 단계

온톨로지을 기반으로 모델을 개발 하는 방법에 대 한 경로를 계속 합니다. [*모델 개발 경로에 ontology 전략을 사용*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)합니다.