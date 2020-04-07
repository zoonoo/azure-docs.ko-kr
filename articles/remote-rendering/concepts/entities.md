---
title: 엔터티
description: Azure 원격 렌더링 API 범위의 엔터티 정의
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681949"
---
# <a name="entities"></a>엔터티

*엔터티는* 공간에서 이동 가능한 개체를 나타내며 원격으로 렌더링된 콘텐츠의 기본 구성 요소입니다.

## <a name="entity-properties"></a>엔터티 속성

엔터티에는 위치, 회전 및 배율로 정의된 변환이 있습니다. 그 자체로 엔티티는 관찰 할 수있는 기능이 없습니다. 대신 엔터티에 연결된 구성 요소를 통해 동작이 추가됩니다. 예를 [들어, 절단 평면 구성 요소를](../overview/features/cut-planes.md) 첨부하면 도면요소의 위치에 절단 평면이 작성됩니다.

엔터티 자체의 가장 중요한 측면은 계층 구조와 결과 계층 변환입니다. 예를 들어 여러 엔터티가 공유 상위 엔터티의 자식으로 연결된 경우 상위 엔터티의 변환을 변경하여 이러한 모든 엔터티를 이동, 회전 및 함께 확장할 수 있습니다.

엔터티는 부모가 고유하게 소유하므로 부모가 `Entity.Destroy()`소멸될 때 해당 하위 구성 요소와 연결된 모든 구성 [요소도](components.md)소멸됩니다. 따라서 장면에서 모델을 제거하는 작업은 SAS `Destroy` 변형에 `AzureSession.Actions.LoadModelFromSASAsync()`의해 반환되는 모델의 `AzureSession.Actions.LoadModelAsync()` 루트 노드를 호출하여 수행됩니다.

엔터티는 서버가 콘텐츠를 로드하거나 사용자가 장면에 개체를 추가하려는 경우에 만들어집니다. 예를 들어 사용자가 메시의 내부를 시각화하기 위해 절단 평면을 추가하려는 경우 사용자는 평면이 있어야 하는 엔티티를 만든 다음 절단 평면 구성요소를 추가할 수 있습니다.

## <a name="query-functions"></a>쿼리 함수

엔터티에는 동기 호출과 비동기 호출의 두 가지 유형의 쿼리 함수가 있습니다. 동기 쿼리는 클라이언트에 있는 데이터에만 사용할 수 있으며 많은 계산을 포함하지 않습니다. 예를 들어 구성 요소, 상대 개체 변환 또는 부모/하위 관계에 대한 쿼리를 예로 들 수 있습니다. 비동기 쿼리는 서버에만 있거나 클라이언트에서 실행하기에 너무 많은 비용이 드는 추가 계산이 포함된 데이터에 사용됩니다. 예를 들어 공간 경계 쿼리 또는 메타 데이터 쿼리입니다.

### <a name="querying-components"></a>구성 요소 쿼리

특정 유형의 구성 요소를 찾으려면 다음을 사용합니다. `FindComponentOfType`

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>변환 쿼리

변환 쿼리는 개체에 대한 동기 호출입니다. API를 통해 쿼리되는 변환은 개체의 부모를 기준으로 로컬 공간 변환입니다. 예외는 로컬 공간과 월드 공간이 동일한 루트 개체입니다.

> [!NOTE]
> 임의의 개체의 월드 공간 변환을 쿼리하는 전용 API는 없습니다.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>공간 경계 쿼리

경계 쿼리는 하나의 엔터티를 루트로 사용하여 전체 개체 계층 구조에서 작동하는 비동기 호출입니다. [개체 경계에](object-bounds.md)대한 전용 장을 참조하십시오.

### <a name="querying-metadata"></a>메타데이터 쿼리

메타데이터는 서버에서 무시되는 개체에 저장된 추가 데이터입니다. 개체 메타데이터는 기본적으로 _값은_ 숫자, 부울 또는 문자열 형식일 수 있는 (이름, 값) 쌍의 집합입니다. 메타데이터는 모델과 함께 내보낼 수 있습니다.

메타데이터 쿼리는 특정 엔터티에 대한 비동기 호출입니다. 쿼리는 하위 그래프의 병합된 정보가 아닌 단일 엔터티의 메타데이터만 반환합니다.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

개체에 메타데이터가 없는 경우에도 쿼리가 성공합니다.

## <a name="next-steps"></a>다음 단계

* [구성 요소](components.md)
* [개체 경계](object-bounds.md)
