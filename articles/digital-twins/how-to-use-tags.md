---
title: 디지털 쌍에 태그 추가
titleSuffix: Azure Digital Twins
description: 디지털 쌍에서 태그를 구현 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 59f68909e2f3704fea5c38e3f1535f5996b284ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099173"
---
# <a name="add-tags-to-digital-twins"></a>디지털 쌍에 태그 추가 

태그 개념을 사용 하 여 디지털 쌍을 추가로 식별 하 고 분류할 수 있습니다. 특히 사용자는 해당 Azure 디지털 Twins 인스턴스 내에서 [줄이면서도 태그](https://project-haystack.org/doc/TagModel)와 같은 기존 시스템의 태그를 복제할 수 있습니다. 

이 문서에서는 디지털 쌍에서 태그를 구현 하는 데 사용할 수 있는 패턴에 대해 설명 합니다.

태그는 먼저 디지털 쌍을 설명 하는 [모델](concepts-models.md) 내에 속성으로 추가 됩니다. 그런 다음 해당 속성은 모델을 기반으로 만들 때 쌍에 대해 설정 됩니다. 그런 다음 [쿼리에서](concepts-query-language.md) 태그를 사용 하 여 쌍을 식별 하 고 필터링 할 수 있습니다.

## <a name="marker-tags"></a>표식 태그 

**표식 태그** 는 "blue" 또는 "red"와 같이 디지털 쌍을 표시 하거나 분류 하는 데 사용 되는 간단한 문자열입니다. 이 문자열은 태그의 이름이 고, 표식 태그에는 의미 있는 값이 없습니다. 태그는 현재 상태 (또는 부재)로만 의미가 있습니다. 

### <a name="add-marker-tags-to-model"></a>모델에 표식 태그 추가 

표식 태그는에서로 [Dtdl](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 맵으로 모델링 됩니다 `string` `boolean` . `mapValue`태그의 존재는 모두 중요 하므로 부울은 무시 됩니다. 

다음은 마커 태그를 속성으로 구현 하는 쌍 모델에서 발췌 한 것입니다.

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Digital 쌍에 표식 태그 추가

`tags`속성이 디지털 쌍의 모델의 일부인 경우이 속성의 값을 설정 하 여 디지털 쌍의 마커 태그를 설정할 수 있습니다. 

다음은 세 쌍의 표식을 채우는 예제입니다 `tags` .

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>표식 태그가 있는 쿼리

태그가 디지털 쌍에 추가 되 면 태그를 사용 하 여 쿼리에서 쌍을 필터링 할 수 있습니다. 

"Red"로 태그가 지정 된 모든 쌍을 가져오는 쿼리는 다음과 같습니다. 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

더 복잡 한 쿼리에 대 한 태그를 결합할 수도 있습니다. 다음은 빨간색이 아닌 라운드 된 모든 쌍을 가져오는 쿼리입니다. 

```sql
select * from digitaltwins where not is_defined(tags.red) and is_defined(tags.round) 
```

## <a name="value-tags"></a>값 태그 

**값 태그** 는 각 태그에 또는 등의 값을 지정 하는 데 사용 되는 키-값 쌍입니다 `"color": "blue"` `"color": "red"` . 값 태그를 만든 후에는 태그의 값을 무시 하 여 표식 태그로 사용할 수도 있습니다. 

### <a name="add-value-tags-to-model"></a>모델에 값 태그 추가 

값 태그는에서로의 [Dtdl](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 맵으로 모델링 `string` 됩니다 `string` . 및가 모두 `mapKey` `mapValue` 중요 합니다. 

다음은 값 태그를 속성으로 구현 하는 쌍 모델에서 발췌 한 것입니다.

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Digital 쌍에 값 태그 추가

표식 태그와 마찬가지로 모델에서이 속성의 값을 설정 하 여 디지털 쌍에서 value 태그를 설정할 수 있습니다 `tags` . 값 태그를 표식 태그로 사용 하려면 `tagValue` 필드를 빈 문자열 값 ()으로 설정 하면 `""` 됩니다. 

다음은 `tags` 3 개의 twins에 대 한 값을 채우는 예입니다.

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

`red` `purple` 이 예제에서는 및가 표식 태그로 사용 됩니다.

### <a name="query-with-value-tags"></a>값 태그가 있는 쿼리

표식 태그와 마찬가지로 값 태그를 사용 하 여 쿼리에서 쌍를 필터링 할 수 있습니다. 값 태그와 표식 태그를 함께 사용할 수도 있습니다.

위의 예제에서 `red` 는 표식 태그로 사용 됩니다. "Red"로 태그가 지정 된 모든 쌍을 가져오는 쿼리는 다음과 같습니다. 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

다음은 작은 (값 태그) 인 모든 엔터티를 가져오는 쿼리 이며 빨간색은 아닙니다. 

```sql
select * from digitaltwins where not is_defined(tags.red) and tags.size = 'small' 
```

## <a name="next-steps"></a>다음 단계

디지털 쌍 모델 디자인 및 관리에 대해 자세히 알아보세요.
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)

쌍 그래프 쿼리를 참조 하세요.
* [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)