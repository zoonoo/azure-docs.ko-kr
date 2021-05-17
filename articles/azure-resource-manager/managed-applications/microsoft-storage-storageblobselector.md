---
title: StorageBlobSelector UI 요소
description: Azure Portal의 Microsoft.Storage.StorageBlobSelector UI 요소에 관해 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754532"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft.Storage.StorageBlobSelector UI 요소

Azure Storage 계정에서 Blob을 선택하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

사용자에게 사용 가능한 스토리지 Blob을 검색하는 옵션이 제공됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft.Storage.StorageBlobSelector - 찾아보기":::

**찾아보기** 를 선택하면 사용자가 스토리지 계정을 선택할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft.Storage.StorageBlobSelector - 스토리지 선택":::

사용자가 스토리지 계정의 컨테이너를 보고 하나를 선택할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft.Storage.StorageBlobSelector - 컨테이너 선택":::

컨테이너에서 사용자가 파일을 선택할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft.Storage.StorageBlobSelector - 파일":::

선택한 파일 이름을 표시하도록 컨트롤이 업데이트됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft.Storage.StorageBlobSelector - 파일 선택 표시":::

## <a name="schema"></a>스키마

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>설명

**constraints.allowedFileExtensions** 속성은 허용되는 파일 유형을 지정합니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
