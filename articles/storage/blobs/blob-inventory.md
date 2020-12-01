---
title: Azure Storage 인벤토리를 사용 하 여 blob 데이터 관리 (미리 보기)
description: Azure Storage inventory는 저장소 계정 내의 모든 blob 데이터에 대 한 개요를 가져오는 데 도움이 되는 도구입니다.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 11/04/2020
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 149fb9c888c54ea45d273890f3fe2cd59730fa01
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355007"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Azure Storage blob 인벤토리를 사용 하 여 blob 데이터 관리 (미리 보기)

Azure Storage blob 인벤토리 기능은 저장소 계정 내의 blob 데이터에 대 한 개요를 제공 합니다. 인벤토리 보고서를 사용 하 여 총 데이터 크기, 사용 기간, 암호화 상태 등을 파악 합니다. 이 보고서는 비즈니스 및 규정 준수 요구 사항에 대 한 데이터 개요를 제공 합니다. 일단 사용 하도록 설정 하면 인벤토리 보고서가 매일 자동으로 만들어집니다.

## <a name="availability"></a>가용성

Blob 인벤토리는 GPv2 (범용 버전 2), 프리미엄 블록 blob storage 및 Azure DataLake Storage Gen2 (ADLS Gen2) 계정에 대해 지원 됩니다.

### <a name="preview-regions"></a>미리 보기 영역

Blob 인벤토리 미리 보기는 다음 지역의 저장소 계정에서 사용할 수 있습니다.

- 프랑스 중부
- 캐나다 중부
- 캐나다 동부

### <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

인벤토리 보고서에 대 한 요금은 미리 보기 기간 동안 요금이 청구 되지 않습니다. 이 기능을 일반적으로 사용할 수 있는 경우 가격은 결정 됩니다.

## <a name="enable-inventory-reports"></a>인벤토리 보고서 사용

저장소 계정에 정책을 추가 하 여 blob 인벤토리 보고서를 사용 하도록 설정 합니다. [Azure Portal](https://portal.azure.com/)를 사용 하 여 정책을 추가, 편집 또는 제거 합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.
1. 저장소 계정 중 하나를 선택 합니다.
1. **Blob service** 에서 **Blob 인벤토리** 를 선택 합니다.
1. **Blob inventory enabled** 가 선택 되어 있는지 확인 합니다.
1. **규칙 추가를 선택 합니다** .
1. 새 규칙 이름
1. 인벤토리 보고서에 대 한 **Blob 유형** 선택
1. 목록 보고서를 필터링 하기 위해 접두사 일치 추가
1. 인벤토리 보고서에 **blob 버전을 포함할지** 아니면 **스냅숏을** 포함할지를 선택 합니다. 해당 하는 옵션을 사용 하는 새 규칙을 저장 하려면 계정에서 버전 및 스냅숏을 사용 하도록 설정 해야 합니다.
1. **저장** 을 선택합니다.

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Azure Portal 사용 하 여 blob 인벤토리 규칙을 추가 하는 방법을 보여 주는 스크린샷":::

인벤토리 정책은 전체로 읽거나 쓸 수 있습니다. 부분 업데이트는 지원 되지 않습니다.

> [!IMPORTANT]
> 저장소 계정에 대해 방화벽 규칙을 사용 하도록 설정 하면 인벤토리 요청이 차단 될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대 한 예외를 제공 하 여 이러한 요청의 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](../common/storage-network-security.md#exceptions)의 예외 섹션을 참조하세요.

Blob 인벤토리 실행은 매일 자동으로 예약 됩니다. 인벤토리 실행을 완료 하는 데 최대 24 시간이 걸릴 수 있습니다. 인벤토리 보고서는 하나 이상의 규칙을 포함 하는 인벤토리 정책을 추가 하 여 구성 됩니다.

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

Azure Portal의 **Blob inventory** 섹션에서 **코드 보기** 탭을 선택 하 여 인벤토리 정책에 대 한 JSON을 표시 합니다.

| 매개 변수 이름 | 매개 변수 형식        | 참고 | 필수 여부 |
|----------------|-----------------------|-------|-----------|
| destination    | String                | 모든 인벤토리 파일이 생성 될 대상 컨테이너입니다. 대상 컨테이너는 이미 존재 해야 합니다. | Yes |
| 사용        | 부울               | 전체 정책을 사용 하지 않도록 설정 하는 데 사용 됩니다. **True** 로 설정 되 면 규칙 수준 사용 필드가이 매개 변수를 재정의 합니다. 사용 하지 않도록 설정 하면 모든 규칙에 대 한 인벤토리가 사용 하지 않도록 설정 됩니다. | Yes |
| 규칙          | Rule 개체의 배열 | 정책에 하나 이상의 규칙이 필요 합니다. 최대 10 개의 규칙이 지원 됩니다. | Yes |

## <a name="inventory-rules"></a>인벤토리 규칙

규칙은 인벤토리 보고서를 생성 하기 위한 필터링 조건 및 출력 매개 변수를 캡처합니다. 각 규칙은 인벤토리 보고서를 만듭니다. 규칙에는 겹치는 접두사가 있을 수 있습니다. Blob는 규칙 정의에 따라 둘 이상의 인벤토리에 표시 될 수 있습니다.

정책 내의 각 규칙에는 다음과 같은 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식                 | 참고 | 필수 여부 |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | 규칙 이름에는 대/소문자를 구분 하는 영숫자 문자를 256 개까지 포함할 수 있습니다. 이 이름은 정책 내에서 고유 해야 합니다. | Yes |
| 사용        | 부울                        | 규칙을 사용 하거나 사용 하지 않도록 설정할 수 있는 플래그입니다. 기본값은 **true** 입니다. | Yes |
| 정의     | JSON 인벤토리 규칙 정의 | 각 정의는 규칙 필터 집합으로 구성 됩니다. | Yes |

전역 **Blob inventory enabled** 플래그는 규칙의 *사용* 매개 변수 보다 우선 적용 됩니다.

### <a name="rule-filters"></a>규칙 필터

Blob 인벤토리 보고서를 사용자 지정 하는 데 사용할 수 있는 몇 가지 필터는 다음과 같습니다.

| 필터 이름         | 필터 형식                     | 참고 | 필수 여부 |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | 미리 정의 된 열거형 값의 배열 | 유효한 값은 `blockBlob` 및 `appendBlob` 계층 구조 네임 스페이스를 사용 하는 계정의 경우이 고 `blockBlob` , `appendBlob` `pageBlob` 다른 계정에 대해서는, 및입니다. | Yes |
| prefixMatch         | 일치 시킬 접두사에 대 한 최대 10 개의 문자열 배열입니다. 접두사는 컨테이너 이름으로 시작 해야 합니다 (예: "container1/foo"). | *PrefixMatch* 를 정의 하지 않거나 빈 접두사를 제공 하는 경우 규칙은 저장소 계정 내의 모든 blob에 적용 됩니다. | 예 |
| includeSnapshots    | 부울                         | 인벤토리에 스냅숏을 포함할지 여부를 지정 합니다. 기본값은 **false** 입니다. | 예 |
| includeBlobVersions | 부울                         | 인벤토리에 blob 버전을 포함할지 여부를 지정 합니다. 기본값은 **false** 입니다. | 예 |

Azure Portal의 **Blob inventory** 섹션에서 **코드 보기** 탭을 선택 하 여 인벤토리 규칙에 대 한 JSON을 표시 합니다. 필터는 규칙 정의 내에서 지정 됩니다.

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

각 인벤토리 실행은 지정 된 인벤토리 대상 컨테이너에 CSV 형식의 파일 집합을 생성 합니다. 인벤토리 출력이 생성 되는 경로 `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` 는 다음과 같습니다.

- *accountName* 은 Azure Blob Storage 계정 이름입니다.
- *인벤토리-대상-컨테이너* 는 인벤토리 정책에서 지정한 대상 컨테이너입니다.
- *YYYY/mm/DD/HH-MM-SS* 는 인벤토리가 실행 되기 시작한 시간입니다.

### <a name="inventory-files"></a>인벤토리 파일

각 인벤토리 실행은 다음 파일을 생성 합니다.

- **인벤토리 csv 파일**: 각 인벤토리 규칙에 대 한 쉼표로 구분 된 값 (csv) 파일입니다. 각 파일에는 일치 하는 개체와 해당 메타 데이터가 포함 됩니다. 모든 CSV 형식 파일의 첫 번째 행은 항상 스키마 행입니다. 다음 이미지는 Microsoft Excel에서 열린 인벤토리 CSV 파일을 보여 줍니다.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Microsoft Excel에서 열린 인벤토리 CSV 파일의 스크린샷":::

- **매니페스트 파일**: 실행의 모든 규칙에 대해 생성 된 인벤토리 파일의 세부 정보가 포함 된 파일에 대 한 manifest.js입니다. 또한 매니페스트 파일은 사용자가 제공 하는 규칙 정의와 해당 규칙에 대 한 인벤토리의 경로를 캡처합니다.

- **체크섬 파일**: 파일의 manifest.js내용에 대 한 MD5 체크섬을 포함 하는 매니페스트 체크섬 파일입니다. 매니페스트 생성. checksum 파일은 인벤토리 실행 완료를 표시 합니다.

## <a name="inventory-completed-event"></a>인벤토리 완료 이벤트

인벤토리 실행이 완료 되 면 알림을 받으려면 inventory completed 이벤트를 구독 합니다. 이 이벤트는 매니페스트 체크섬 파일이 만들어질 때 생성 됩니다. 인벤토리 완료 이벤트는 실행을 시작 하기 전에 인벤토리 실행이 사용자 오류에 실패 하는 경우에도 발생 합니다. 예를 들어 잘못 된 정책 또는 대상 컨테이너 없음 오류가 발생 하면 이벤트를 트리거합니다. 이벤트가 Blob 인벤토리 항목에 게시 됩니다.

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

## <a name="next-steps"></a>다음 단계

[Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
