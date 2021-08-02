---
title: Blob의 암호화 상태 확인 - Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용하여 지정된 blob이 암호화되었는지 여부를 확인하는 방법을 알아봅니다. Blob이 암호화되지 않은 경우 blob을 다운로드하고 다시 업로드하여 AzCopy를 통해 암호화를 강제 적용하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f9392b0645b86ea055d67a7d11f9e62f1bf08743
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110677269"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Blob의 암호화 상태 확인

2017년 10월 20일 이후에 Azure Storage로 작성된 모든 블록 blob, 추가 blob 또는 페이지 blob은 Azure Storage 암호화를 사용하여 암호화됩니다. 이 날짜 이전에 만들어진 blob은 백그라운드 프로세스에 의해 계속 암호화됩니다.

이 문서에서는 지정된 blob이 암호화되었는지 여부를 확인하는 방법을 보여 줍니다.

## <a name="check-a-blobs-encryption-status"></a>Blob의 암호화 상태 확인

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 blob이 코드 없이 암호화되는지 여부를 확인합니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 blob이 암호화되었는지 여부를 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. 계정의 컨테이너 목록으로 이동하려면 **컨테이너** 를 선택합니다.
1. blob을 찾아 **개요** 탭을 표시합니다.
1. **서버 암호화** 속성을 봅니다. 다음 이미지에 표시된 대로 **True** 이면 blob이 암호화됩니다. Blob의 속성에는 blob이 생성된 날짜 및 시간도 포함됩니다.

    ![Azure Portal에서 서버 암호화된 속성을 확인하는 방법을 보여 주는 스크린샷](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 blob이 암호화되었는지 확인하려면 blob의 **IsServerEncrypted** 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

blob이 언제 만들어졌는지 확인하려면 **Created** 속성 값을 확인합니다.

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 blob이 암호화되었는지 확인하려면 blob의 **IsServerEncrypted** 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

blob이 언제 만들어졌는지 확인하려면 **created** 속성 값을 확인합니다.

---

## <a name="force-encryption-of-a-blob"></a>Blob의 암호화 강제 적용

2017년 10월 20일 이전에 만들어진 blob이 백그라운드 프로세스에서 아직 암호화되지 않은 경우 blob을 다운로드하고 다시 업로드하여 즉시 암호화가 강제 적용되도록 할 수 있습니다. 이를 수행하는 간단한 방법은 AzCopy를 사용하는 것입니다.

AzCopy를 사용하여 로컬 파일 시스템에 blob을 다운로드하려면 다음 구문을 사용합니다.

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

AzCopy를 사용하여 Azure Storage에 blob을 다시 업로드하려면 다음 구문을 사용합니다.

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

AzCopy를 사용하여 blob 데이터를 복사하는 방법에 대한 자세한 내용은 [AzCopy 및 Blob Storage를 사용하여 데이터 전송](../common/storage-use-azcopy-v10.md#transfer-data)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
