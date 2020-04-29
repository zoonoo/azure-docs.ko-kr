---
title: 엔터티
description: Azure 원격 렌더링 API 범위에서 엔터티 정의
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681949"
---
# <a name="entities"></a>엔터티

*엔터티* 는 영역 이동 가능한 개체를 나타내며 원격으로 렌더링 된 콘텐츠의 기본 구성 요소입니다.

## <a name="entity-properties"></a>엔터티 속성

엔터티는 위치, 회전 및 눈금으로 정의 된 변환을 포함 합니다. 직접 엔터티는 관찰 가능한 기능을 포함 하지 않습니다. 대신 엔터티에 연결 된 구성 요소를 통해 동작이 추가 됩니다. 예를 들어 [CutPlaneComponent](../overview/features/cut-planes.md) 를 연결 하면 엔터티 위치에 잘린 평면이 생성 됩니다.

엔터티 자체의 가장 중요 한 측면은 계층 구조와 결과 계층적 변환입니다. 예를 들어 여러 엔터티가 공유 부모 엔터티에 자식으로 연결 된 경우 이러한 모든 엔터티는 부모 엔터티의 변환을 변경 하 여 한꺼번에에서 이동, 회전 및 크기를 조정할 수 있습니다.

엔터티는 부모에 의해 고유 하 게 소유 됩니다. 즉 `Entity.Destroy()`, 부모는에서 제거 될 때 자식 및 연결 된 모든 [구성 요소](components.md)입니다. 따라서 장면에서 모델을 제거 하는 것은 `Destroy` `AzureSession.Actions.LoadModelAsync()` 또는 해당 SAS 변형 `AzureSession.Actions.LoadModelFromSASAsync()`에서 반환 된 모델의 루트 노드에서를 호출 하 여 수행 됩니다.

엔터티는 서버에서 콘텐츠를 로드 하거나 사용자가 장면에 개체를 추가 하려는 경우에 생성 됩니다. 예를 들어 사용자가 메시의 내부를 시각화 하기 위해 잘린 평면을 추가 하려는 경우 사용자는 평면이 존재 해야 하는 엔터티를 만든 다음이를 해당 영역에 추가할 수 있습니다.

## <a name="query-functions"></a>쿼리 함수

엔터티에는 동기 및 비동기 호출 이라는 두 가지 유형의 쿼리 함수가 있습니다. 동기 쿼리는 클라이언트에 있는 데이터에만 사용할 수 있으며 많은 계산을 포함 하지 않습니다. 예를 들어 구성 요소, 상대 개체 변환 또는 부모/자식 관계에 대 한 쿼리를 사용할 수 있습니다. 비동기 쿼리는 서버에만 존재 하거나 클라이언트에서 실행 하기에 너무 많은 비용이 소요 되는 추가 계산과 관련 된 데이터에 사용 됩니다. 예를 들면 공간 범위 쿼리 또는 메타 데이터 쿼리가 있습니다.

### <a name="querying-components"></a>구성 요소 쿼리

특정 형식의 구성 요소를 찾으려면 다음을 사용 `FindComponentOfType`합니다.

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>변환 쿼리

변환 쿼리는 개체에 대 한 동기 호출입니다. API를 통해 쿼리 된 변환은 개체의 부모를 기준으로 하는 로컬 공간 변환입니다. 예외는 로컬 공간과 세계 공간이 동일한 루트 개체입니다.

> [!NOTE]
> 임의 개체의 세계 공간 변환을 쿼리 하는 전용 API는 없습니다.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>공간 범위 쿼리

범위 쿼리는 하나의 엔터티를 루트로 사용 하 여 전체 개체 계층 구조에서 작동 하는 비동기 호출입니다. [개체 범위](object-bounds.md)에 대 한 전용 챕터를 참조 하세요.

### <a name="querying-metadata"></a>메타데이터 쿼리

메타 데이터는 서버에서 무시 되는 개체에 저장 되는 추가 데이터입니다. 개체 메타 데이터는 기본적으로 (이름, 값) 쌍의 집합입니다. 여기서 _값_ 은 숫자, 부울 또는 문자열 형식일 수 있습니다. 메타 데이터는 모델을 사용 하 여 내보낼 수 있습니다.

메타 데이터 쿼리는 특정 엔터티에 대 한 비동기 호출입니다. 이 쿼리는 하위 그래프의 병합 된 정보가 아니라 단일 엔터티의 메타 데이터만 반환 합니다.

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

개체가 메타 데이터를 포함 하지 않는 경우에도 쿼리가 성공 합니다.

## <a name="next-steps"></a>다음 단계

* [요소도](components.md)
* [개체 경계](object-bounds.md)
