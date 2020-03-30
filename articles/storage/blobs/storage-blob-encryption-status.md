---
title: Blob - Azure 저장소의 암호화 상태 확인
description: Azure 포털, PowerShell 또는 Azure CLI를 사용하여 지정된 Blob이 암호화되었는지 확인하는 방법을 알아봅니다. Blob이 암호화되지 않은 경우 AzCopy를 사용하여 Blob을 다운로드하고 다시 업로드하여 암호화를 강제하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707588"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Blob의 암호화 상태 확인

2017년 10월 20일 이후에 Azure 저장소에 작성된 모든 블록 Blob, 부속 Blob 또는 페이지 Blob은 Azure Storage 암호화로 암호화됩니다. 이 날짜 이전에 생성된 Blob은 백그라운드 프로세스에 의해 계속 암호화됩니다.

이 문서에서는 지정된 Blob이 암호화되었는지 여부를 확인하는 방법을 보여 주었습니다.

## <a name="check-a-blobs-encryption-status"></a>Blob의 암호화 상태 확인

Azure 포털, PowerShell 또는 Azure CLI를 사용하여 Blob이 코드 없이 암호화되는지 여부를 확인합니다.

### <a name="azure-portal"></a>[Azure 포털](#tab/portal)

Azure 포털을 사용하여 Blob이 암호화되었는지 확인하려면 다음 단계를 따르세요.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **컨테이너를** 선택하여 계정의 컨테이너 목록으로 이동합니다.
1. Blob을 찾아 **개요** 탭을 표시합니다.
1. 서버 **암호화** 된 속성을 봅니다. **True인**경우 다음 이미지와 같이 Blob이 암호화됩니다. Blob의 속성에는 Blob이 작성된 날짜와 시간도 포함됩니다.

    ![Azure 포털에서 서버 암호화 된 속성을 확인하는 방법을 보여주는 스크린 샷](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell을 사용하여 Blob이 암호화되었는지 확인하려면 Blob의 **IsServerEncrypted** 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Blob이 생성된 시기를 확인하려면 **Created** 속성의 값을 확인합니다.

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 Blob이 암호화되었는지 확인하려면 Blob의 **IsServerEncrypted** 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Blob이 생성된 시기를 확인하려면 **생성된** 속성의 값을 확인합니다.

---

## <a name="force-encryption-of-a-blob"></a>Blob의 강제 암호화

2017년 10월 20일 이전에 생성된 Blob이 백그라운드 프로세스에 의해 아직 암호화되지 않은 경우 Blob을 다운로드하고 다시 업로드하여 암호화를 즉시 수행할 수 있습니다. 이 작업을 수행하는 간단한 방법은 AzCopy를 사용하면 됩니다.

AzCopy를 사용하여 로컬 파일 시스템에 Blob을 다운로드하려면 다음 구문을 사용합니다.

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

AzCopy를 사용하여 Blob을 Azure 저장소에 다시 업로드하려면 다음 구문을 사용합니다.

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

AzCopy를 사용하여 Blob 데이터를 복사하는 데 대한 자세한 내용은 [AzCopy 및 Blob 저장소를 사용하여 데이터 전송을](../common/storage-use-azcopy-blobs.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

[미사용 데이터에 대한 Azure 저장소 암호화](../common/storage-service-encryption.md)
