---
title: 모델
description: Azure 원격 렌더링에서 모델의 정의에 대해 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617952"
---
# <a name="models"></a>모델

Azure 원격 렌더링의 *모델* 은 [엔터티](entities.md) 및 [구성 요소로](components.md)구성 된 전체 개체 표현을 나타냅니다. 모델은 원격 렌더링 서비스에 사용자 지정 데이터를 가져오는 주요 방법입니다.

## <a name="model-structure"></a>모델 구조

모델에는 루트 노드와 정확히 하나의 [엔터티가](entities.md) 있습니다. 자식 엔터티의 임의 계층이 있을 수 있습니다. 모델을 로드할 때이 루트 엔터티에 대 한 참조가 반환 됩니다.

각 엔터티에는 연결 된 [구성 요소가](components.md) 있을 수 있습니다. 가장 일반적인 경우 엔터티에는 [메시 리소스](meshes.md)를 참조 하는 *MeshComponents*가 있습니다.

## <a name="creating-models"></a>모델 만들기

런타임에 대 한 모델을 만드는 방법은 [입력 모델](../how-tos/conversion/model-conversion.md) 을 FBX 및 글 지와 같은 파일 형식으로 변환 하는 것입니다. 변환 프로세스는 질감, 재질 및 메시와 같은 모든 리소스를 추출 하 여 최적화 된 런타임 형식으로 변환 합니다. 또한 구조적 정보를 추출 하 고이를 ARR의 엔터티/구성 요소 그래프 구조로 변환 합니다.

> [!IMPORTANT]
>
> [모델 변환은](../how-tos/conversion/model-conversion.md) [메시](meshes.md)를 만드는 유일한 방법입니다. 메시는 런타임에 엔터티 사이에서 공유할 수 있지만 모델을 로드 하는 것을 제외 하 고는 다른 방법으로는 런타임을 런타임에 가져올 수 없습니다.

## <a name="loading-models"></a>모델 로드

모델을 변환한 후에는 Azure blob 저장소에서 런타임으로 로드할 수 있습니다.

Blob storage에서 자산의 주소가 지정 되는 방식에 따라 서로 다른 두 개의 로드 함수가 있습니다.

* 모델은 SAS URI로 주소를 지정할 수 있습니다. 관련 로드 함수는 `LoadModelFromSASAsync` 매개 변수 `LoadModelFromSASParams`를 사용 합니다. [기본 제공 모델](../samples/sample-model.md)을 로드 하는 경우에도이 변형을 사용 합니다.
* Blob [저장소를 계정에 연결](../how-tos/create-an-account.md#link-storage-accounts)하는 경우 blob 저장소 매개 변수를 통해 모델에 직접 주소를 지정할 수 있습니다. 이 경우 관련 로드 함수는 `LoadModelAsync` 매개 변수 `LoadModelParams`를 사용 합니다.

다음 코드 조각에서는 두 함수를 사용 하 여 모델을 로드 하는 방법을 보여 줍니다. SAS URI를 사용 하 여 모델을 로드 하려면 아래와 같은 코드를 사용 합니다.

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

Blob 저장소 매개 변수를 직접 사용 하 여 모델을 로드 하려면 다음 코드 조각과 비슷한 코드를 사용 합니다.

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

그런 다음 엔터티 계층 구조를 트래버스 하 고 엔터티 및 구성 요소를 수정할 수 있습니다. 동일한 모델을 여러 번 로드 하면 각각 엔터티/구성 요소 구조의 자체 복사본이 있는 여러 인스턴스가 생성 됩니다. 메시, 재질 및 질감이 [공유 리소스](../concepts/lifetime.md)이므로 해당 데이터는 다시 로드 되지 않습니다. 따라서 모델을 두 번 이상 인스턴스화하면 메모리 오버 헤드가 거의 발생 하지 않습니다.

> [!CAUTION]
> ARR의 모든 *비동기* 함수는 비동기 작업 개체를 반환 합니다. 작업이 완료 될 때까지 해당 개체에 대 한 참조를 저장 해야 합니다. 그렇지 않으면 c # 가비지 수집기가 작업을 일찍 삭제할 수 있으며 완료 되지 않습니다. 위의 샘플 코드에서 *wait* 를 사용 하면 모델 로드가 완료 될 때까지 지역 변수 ' loadop '에 참조가 포함 됩니다. 그러나 *완료* 된 이벤트를 대신 사용 하는 경우에는 멤버 변수에 비동기 작업을 저장 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Entities](entities.md)
* [메시](meshes.md)
