---
title: 모델
description: Azure 원격 렌더링에 있는 모델에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 770c88ddfb44004b76633cbeb726d28e1626a72c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681728"
---
# <a name="models"></a>모델

Azure 원격 렌더링의 *모델은* [엔터티](entities.md) 및 [구성 요소로](components.md)구성된 전체 개체 표현을 나타냅니다. 모델은 사용자 지정 데이터를 원격 렌더링 서비스로 변환하는 주요 방법입니다.

## <a name="model-structure"></a>모델 구조

모델에는 정확히 하나의 [엔터티가](entities.md) 루트 노드입니다. 아래에는 자식 엔터티의 임의 계층 구조가 있을 수 있습니다. 모델을 로드할 때 이 루트 엔터티에 대한 참조가 반환됩니다.

각 엔터티에는 [구성 요소가](components.md) 연결되어 있을 수 있습니다. 가장 일반적인 경우 엔터티에는 메시 [리소스를](meshes.md)참조하는 *MeshComponents가*있습니다.

## <a name="creating-models"></a>모델 작성

런타임에 대한 모델을 만드는 것은 FBX 및 GLTF와 같은 파일 형식에서 [입력 모델을 변환하여](../how-tos/conversion/model-conversion.md) 달성됩니다. 변환 프로세스는 텍스처, 재질 및 메시와 같은 모든 리소스를 추출하고 최적화된 런타임 형식으로 변환합니다. 또한 구조 정보를 추출하여 ARR의 엔터티/구성 요소 그래프 구조로 변환합니다.

> [!IMPORTANT]
>
> [모델 변환은](../how-tos/conversion/model-conversion.md) [메시생성을](meshes.md)만드는 유일한 방법입니다. 메시는 런타임시 엔터티 간에 공유할 수 있지만 모델을 로드하는 것 이외에는 런타임에 메시를 가져오기 위한 다른 방법은 없습니다.

## <a name="loading-models"></a>로드 모델

모델이 변환되면 Azure Blob 저장소에서 런타임으로 로드할 수 있습니다.

Blob 저장소에서 자산을 처리하는 방식에 따라 다른 두 가지 로딩 함수가 있습니다.

* 모델은 SAS URI로 해결할 수 있습니다. 관련 로딩 `LoadModelFromSASAsync` 함수는 매개 변수가 `LoadModelFromSASParams`있습니다. [기본 제공 모델을](../samples/sample-model.md)로드할 때도 이 변형을 사용합니다.
* Blob 저장소가 계정에 연결된 경우 모델을 [Blob 저장소](../how-tos/create-an-account.md#link-storage-accounts)매개 변수로 직접 해결할 수 있습니다. 이 경우 관련 로딩 `LoadModelAsync` `LoadModelParams`함수는 매개 변수가 있습니다.

다음 코드 조각은 두 함수 중 하나를 사용하여 모델을 로드하는 방법을 보여 준다. SAS URI를 사용하여 모델을 로드하려면 아래와 같은 코드를 사용합니다.

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

Blob 저장소 매개 변수를 직접 사용하여 모델을 로드하려면 다음 코드와 유사한 코드를 사용합니다.

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

    // ... (identical to the SAS URI snippet above)
}
```

그런 다음 엔터티 계층구조를 탐색하고 엔터티 및 구성 요소를 수정할 수 있습니다. 동일한 모델을 여러 번 로드하면 엔터티/구성 요소 구조의 고유한 복사본이 있는 여러 인스턴스가 만들어집니다. 그러나 메시, 재질 및 텍스처는 [공유 리소스이므로](../concepts/lifetime.md)해당 데이터가 다시 로드되지 않습니다. 따라서 모델을 두 번 이상 인스턴스화하면 메모리 오버헤드가 상대적으로 적습니다.

> [!CAUTION]
> ARR의 모든 *비동기* 함수는 비동기 작업 개체를 반환합니다. 작업이 완료될 때까지 해당 개체에 대한 참조를 저장해야 합니다. 그렇지 않으면 C# 가비지 수집기는 작업을 일찍 삭제할 수 있으며 완료할 수 없습니다. 위의 샘플 코드에서 *await를* 사용하면 모델 로드가 완료될 때까지 로컬 변수 'loadOp'가 참조를 보유합니다. 그러나 *대신 Completed* 이벤트를 사용 하려는 경우 멤버 변수에 비동기 작업을 저장 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Entities](entities.md)
* [메시](meshes.md)
