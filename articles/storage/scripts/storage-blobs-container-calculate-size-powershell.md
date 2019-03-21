---
title: Azure PowerShell 스크립트 샘플 - Blob 컨테이너 크기 계산 | Microsoft Docs
description: 컨테이너에 있는 각 Blob의 크기를 합계해서 Azure Blob Storage의 컨테이너 크기를 계산합니다.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: d8baec875c25556f1080cdd105c7fa466ffce74e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094010"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob Storage 컨테이너 크기 계산

이 스크립트는 컨테이너에 있는 Blob의 크기를 합계해서 Azure Blob Storage의 컨테이너 크기를 계산합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 이 PowerShell 스크립트는 컨테이너의 예상 크기를 제공하므로 청구 요금 계산에 사용하면 안 됩니다. 청구 목적으로 컨테이너 크기를 계산하는 스크립트는 [청구 목적으로 Blob Storage 컨테이너 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)을 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 컨테이너 및 모든 관련된 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Blob Storage 컨테이너의 크기를 계산합니다. 표에 있는 각 항목은 명령 관련 설명서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 리소스 그룹 또는 구독의 지정된 저장소 계정 또는 모든 저장소 계정을 가져옵니다. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | 컨테이너의 Blob을 나열합니다. |

## <a name="next-steps"></a>다음 단계

청구 목적으로 컨테이너 크기를 계산하는 스크립트는 [청구 목적으로 Blob Storage 컨테이너 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)을 참조하세요.

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 스토리지 PowerShell 스크립트 샘플은 [Azure Storage에 대한 PowerShell 샘플](../blobs/storage-samples-blobs-powershell.md)에서 찾을 수 있습니다.
