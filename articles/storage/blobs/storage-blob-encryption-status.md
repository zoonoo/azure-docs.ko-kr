---
title: Blob의 암호화 상태를 확인 합니다.-Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 지정 된 blob이 암호화 되었는지 여부를 확인 하는 방법을 알아봅니다. Blob이 암호화 되지 않은 경우 blob을 다운로드 하 고 다시 업로드 하 여 AzCopy를 사용 하 여 암호화를 강제 적용 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707588"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Blob의 암호화 상태를 확인 합니다.

2017 년 10 월 20 일 이후에 Azure Storage으로 작성 된 모든 블록 blob, 추가 blob 또는 페이지 blob은 Azure Storage 암호화를 사용 하 여 암호화 됩니다. 이 날짜 이전에 만들어진 blob은 백그라운드 프로세스에 의해 계속 암호화 됩니다.

이 문서에서는 지정 된 blob이 암호화 되었는지 여부를 확인 하는 방법을 보여 줍니다.

## <a name="check-a-blobs-encryption-status"></a>Blob의 암호화 상태를 확인 합니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 blob이 코드 없이 암호화 되는지 여부를 확인 합니다.

### <a name="azure-portaltabportal"></a>[Azure 포털](#tab/portal)

Azure Portal를 사용 하 여 blob이 암호화 되었는지 여부를 확인 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **컨테이너** 를 선택 하 여 계정의 컨테이너 목록으로 이동 합니다.
1. Blob을 찾아 **개요** 탭을 표시 합니다.
1. 암호화 된 **서버** 속성을 봅니다. 다음 그림에 표시 된 것 처럼 **True**이면 blob이 암호화 됩니다. Blob의 속성에는 blob이 생성 된 날짜 및 시간도 포함 됩니다.

    ![Azure Portal에서 서버 암호화 된 속성을 확인 하는 방법을 보여 주는 스크린샷](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 blob이 암호화 되었는지 여부를 확인 하려면 blob의 **Isserverencrypted** 속성을 확인 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Blob이 생성 된 시간을 확인 하려면 **created** 속성의 값을 확인 합니다.

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용 하 여 blob가 암호화 되었는지 여부를 확인 하려면 blob의 **Isserverencrypted** 속성을 확인 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Blob이 생성 된 시간을 확인 하려면 **created** 속성의 값을 확인 합니다.

---

## <a name="force-encryption-of-a-blob"></a>Blob의 암호화 강제 적용

2017 년 10 월 20 일 이전에 만들어진 blob이 백그라운드 프로세스에서 아직 암호화 되지 않은 경우 blob을 다운로드 하 고 다시 업로드 하 여 즉시 암호화가 수행 되도록 할 수 있습니다. 이 작업을 수행 하는 간단한 방법은 AzCopy를 사용 하는 것입니다.

AzCopy를 사용 하 여 로컬 파일 시스템에 blob을 다운로드 하려면 다음 구문을 사용 합니다.

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

AzCopy를 사용 하 여 Azure Storage blob을 다시 업로드 하려면 다음 구문을 사용 합니다.

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

AzCopy를 사용 하 여 blob 데이터를 복사 하는 방법에 대 한 자세한 내용은 [AzCopy 및 blob storage로 데이터 전송](../common/storage-use-azcopy-blobs.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[휴지 상태의 데이터에 대 한 암호화 Azure Storage](../common/storage-service-encryption.md)
