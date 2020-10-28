---
title: StorageBlobSelector UI 요소
description: Azure Portal에 대 한 Microsoft StorageBlobSelector UI 요소에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754532"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft 저장소. StorageBlobSelector UI 요소

Azure 저장소 계정에서 blob을 선택 하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

사용 가능한 저장소 blob을 검색 하는 옵션이 사용자에 게 제공 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft 저장소. StorageBlobSelector-찾아보기":::

**찾아보기** 를 선택 하면 사용자가 저장소 계정을 선택할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft 저장소. StorageBlobSelector-찾아보기":::

사용자는 저장소 계정의 컨테이너를 확인 하 고 하나를 선택할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft 저장소. StorageBlobSelector-찾아보기":::

컨테이너에서 사용자는 파일을 선택할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft 저장소. StorageBlobSelector-찾아보기":::

선택한 파일 이름을 표시 하도록 컨트롤이 업데이트 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft 저장소. StorageBlobSelector-찾아보기":::

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

**AllowedFileExtensions** 속성은 허용 되는 파일 형식을 지정 합니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
