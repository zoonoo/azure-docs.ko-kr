---
title: Azure Storage 인벤토리를 사용하여 Blob 데이터 관리(미리 보기)
description: Azure Storage 인벤토리는 스토리지 계정 내의 모든 Blob 데이터에 대한 개요를 가져오는 데 도움이 되는 도구입니다.
services: storage
author: normesta
ms.service: storage
ms.date: 04/01/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 14092e4d6ea63c96b653aaf09160e69a8ad30299
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753776"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Azure Storage Blob 인벤토리를 사용하여 Blob 데이터 관리(미리 보기)

Azure Storage Blob 인벤토리 기능은 스토리지 계정 내의 Blob 데이터에 대한 개요를 제공합니다. 인벤토리 보고서를 사용하여 총 데이터 크기, 사용 기간, 액세스 계층, 암호화 상태 등을 파악합니다. 이 보고서는 비즈니스 및 규정 준수 요구 사항에 대한 데이터 개요를 제공합니다. 일단 사용하도록 설정되면 인벤토리 보고서가 매일 자동으로 만들어집니다.

## <a name="availability"></a>가용성

Blob 인벤토리는 GPv2(범용 버전 2) 및 프리미엄 블록 Blob 스토리지 계정 둘 다에 대해 지원됩니다. 이 기능은 [계층 구조 네임스페이스](data-lake-storage-namespace.md) 기능을 사용하도록 설정하거나 제외하고 지원됩니다.

> [!IMPORTANT]
> Blob 인벤토리는 현재 **미리 보기** 로 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="preview-regions"></a>미리 보기 지역

Blob 인벤토리 미리 보기는 미국 서부를 제외한 모든 공용 지역의 스토리지 계정에서 사용할 수 있습니다.

### <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

인벤토리 보고서에 대한 요금은 미리 보기 기간 동안 청구되지 않습니다. 이 기능이 일반 공급이 될 때 가격이 결정됩니다.

## <a name="enable-inventory-reports"></a>인벤토리 보고서 사용

스토리지 계정에 정책을 추가하여 Blob 인벤토리 보고서를 사용하도록 설정합니다. [Azure Portal](https://portal.azure.com/)을 사용하여 정책을 추가, 편집 또는 제거합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.
1. 스토리지 계정 중 하나를 선택합니다.
1. **Blob service** 에서 **Blob 인벤토리** 를 선택합니다.
1. **Blob 인벤토리 사용** 이 선택되어 있는지 확인합니다.
1. **규칙 추가** 를 선택합니다.
1. 새 규칙에 이름을 지정합니다.
1. 인벤토리 보고서에 대한 **Blob 형식** 을 선택합니다.
1. 인벤토리 보고서를 필터링하기 위해 접두사 일치를 추가합니다.
1. 인벤토리 보고서에 **Blob 버전을 포함할지** 아니면 **스냅샷을 포함할지** 를 선택합니다. 해당하는 옵션을 사용하는 새 규칙을 저장하려면 계정에서 버전 및 스냅샷을 사용하도록 설정해야 합니다.
1. **저장** 을 선택합니다.

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Azure Portal을 사용하여 Blob 인벤토리 규칙을 추가하는 방법을 보여 주는 스크린샷":::

인벤토리 정책은 전체로 읽거나 쓸 수 있습니다. 부분 업데이트는 지원되지 않습니다.

> [!IMPORTANT]
> 스토리지 계정에 대해 방화벽 규칙을 사용하도록 설정하면 인벤토리 요청이 차단될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 예외를 제공하여 이러한 요청의 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](../common/storage-network-security.md#exceptions)의 예외 섹션을 참조하세요.

Blob 인벤토리 실행은 매일 자동으로 예약됩니다. 인벤토리 실행을 완료하는 데 최대 24시간이 걸릴 수 있습니다. 인벤토리 보고서는 하나 이상의 규칙을 포함하는 인벤토리 정책을 추가하여 구성됩니다.

## <a name="inventory-policy"></a>인벤토리 정책

인벤토리 정책은 JSON 문서에 있는 규칙의 컬렉션입니다.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Azure Portal의 **Blob 인벤토리** 섹션에서 **코드 보기** 탭을 선택하여 인벤토리 정책에 대한 JSON을 표시합니다.

| 매개 변수 이름 | 매개 변수 형식        | 참고 | 필수 여부 |
|----------------|-----------------------|-------|-----------|
| destination    | 문자열                | 모든 인벤토리 파일이 생성될 대상 컨테이너입니다. 대상 컨테이너는 이미 있어야 합니다. | 예 |
| 사용        | 부울               | 전체 정책을 비활성화하는 데 사용됩니다. **true** 로 설정되면 규칙 수준 사용 필드가 이 매개 변수를 재정의합니다. 사용하지 않도록 설정하면 모든 규칙에 대한 인벤토리가 사용하지 않도록 설정됩니다. | 예 |
| 규칙          | 규칙 개체의 배열 | 정책에 하나 이상의 규칙이 필요합니다. 최대 10개의 규칙이 지원됩니다. | 예 |

## <a name="inventory-rules"></a>인벤토리 규칙

규칙은 인벤토리 보고서를 생성하기 위한 필터링 조건 및 출력 매개 변수를 캡처합니다. 각 규칙은 인벤토리 보고서를 만듭니다. 규칙에는 겹치는 접두사가 있을 수 있습니다. Blob은 규칙 정의에 따라 1개보다 많은 인벤토리에 표시될 수 있습니다.

정책 내의 각 규칙에는 다음과 같은 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식                 | 참고 | 필수 여부 |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | 규칙 이름은 최대 256자의 대/소문자를 구분하는 영숫자를 포함할 수 있습니다. 이름은 정책 내에서 고유해야 합니다. | 예 |
| 사용        | 부울                        | 규칙을 사용하거나 사용하지 않도록 설정할 수 있는 플래그입니다. 기본값은 **true** 입니다. | 예 |
| 정의     | JSON 인벤토리 규칙 정의 | 각 정의는 규칙 필터 집합으로 구성됩니다. | 예 |

글로벌 **Blob 인벤토리 사용** 플래그는 규칙의 *사용* 매개 변수보다 우선 적용됩니다.

### <a name="rule-filters"></a>규칙 필터

Blob 인벤토리 보고서를 사용자 지정하는 데 사용할 수 있는 몇 가지 필터는 다음과 같습니다.

| 필터 이름         | 필터 형식                     | 참고 | 필수 여부 |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | 미리 정의된 열거형 값의 배열 | 유효한 값은 계층 구조 네임스페이스 사용 계정의 경우 `blockBlob` 및 `appendBlob`이고, 다른 계정의 경우 `blockBlob`, `appendBlob` 및 `pageBlob`입니다. | 예 |
| prefixMatch         | 일치시킬 접두사에 대한 최대 10개의 문자열 배열입니다. 접두사는 컨테이너 이름으로 시작해야 합니다(예: "container1/foo"). | *prefixMatch* 를 정의하지 않거나 빈 접두사를 제공하는 경우 규칙은 스토리지 계정 내의 모든 Blob에 적용됩니다. | No |
| includeSnapshots    | 부울                         | 인벤토리에 스냅샷을 포함할지 여부를 지정합니다. 기본값은 **false** 입니다. | No |
| includeBlobVersions | 부울                         | 인벤토리에 Blob 버전을 포함할지 여부를 지정합니다. 기본값은 **false** 입니다. | No |

Azure Portal의 **Blob 인벤토리** 섹션에서 **코드 보기** 탭을 선택하여 인벤토리 규칙에 대한 JSON을 표시합니다. 필터는 규칙 정의 내에서 지정됩니다.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>인벤토리 출력

각 인벤토리 실행은 지정된 인벤토리 대상 컨테이너에 CSV 형식의 파일 집합을 생성합니다. 인벤토리 출력이 다음 경로: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/`에서 생성됩니다. 여기에서

- *accountName* 은 Azure Blob Storage 계정 이름입니다.
- *inventory-destination-container* 는 인벤토리 정책에서 지정한 대상 컨테이너입니다.
- *YYYY/MM/DD/HH-MM-SS* 는 인벤토리가 실행되기 시작한 시간입니다.

### <a name="inventory-files"></a>인벤토리 파일

각 인벤토리 실행은 다음 파일을 생성합니다.

- **인벤토리 CSV 파일**: 각 인벤토리 규칙에 대한 쉼표로 구분된 값(CSV) 파일입니다. 각 파일에는 일치하는 개체와 해당 메타데이터가 포함됩니다. 모든 CSV 형식 파일의 첫 번째 행은 항상 스키마 행입니다. 다음 이미지는 Microsoft Excel에서 열린 인벤토리 CSV 파일을 보여 줍니다.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Microsoft Excel에서 열린 인벤토리 CSV 파일의 스크린샷":::

- **매니페스트 파일**: 해당 실행의 모든 규칙에 대해 생성된 인벤토리 파일의 세부 정보가 포함된 manifest.json 파일입니다. 또한 매니페스트 파일은 사용자가 제공하는 규칙 정의와 해당 규칙에 대한 인벤토리의 경로를 캡처합니다.

- **체크섬 파일**: manifest.json 파일의 콘텐츠에 대한 MD5 체크섬을 포함하는 manifest.checksum 파일입니다. manifest.checksum 파일의 생성은 인벤토리 실행 완료를 표시합니다.

## <a name="inventory-completed-event"></a>인벤토리 완료 이벤트

인벤토리 실행이 완료되면 알림을 받도록 인벤토리 완료 이벤트를 구독합니다. 이 이벤트는 매니페스트 체크섬 파일이 만들어질 때 생성됩니다. 인벤토리 완료 이벤트는 실행을 시작하기 전에 인벤토리 실행이 사용자 오류로 실패하는 경우에도 발생합니다. 예를 들어 잘못된 정책 또는 대상 컨테이너 없음 오류는 이벤트를 트리거합니다. 이벤트가 Blob 인벤토리 토픽에 게시됩니다.

샘플 이벤트:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="known-issues"></a>알려진 문제

이 섹션에서는 Azure Storage Blob 인벤토리 기능의 제한 사항 및 알려진 문제에 대해 설명합니다.

### <a name="inventory-job-fails-to-complete"></a>인벤토리 작업이 완료되지 못함

수백만 개의 Blob 및 계층 구조 네임스페이스를 사용하는 계정에 대해 24시간 이내에 인벤토리 작업이 완료되지 않을 수 있습니다. 이 경우 인벤토리 파일이 생성되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [컨테이너당 Blob의 개수 및 총 크기 계산](calculate-blob-count-size.md)
- [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
