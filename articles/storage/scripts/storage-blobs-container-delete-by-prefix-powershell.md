---
title: Azure PowerShell 스크립트 샘플 - 접두사별 컨테이너 삭제 | Microsoft Docs
description: 컨테이너 이름 접두사를 기준으로 Azure Storage Blob 컨테이너를 삭제합니다.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: e0c41ed1fc2cdb7559d36a7056ebb7b9a6ecc186
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242773"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>컨테이너 이름 접두사를 기준으로 컨테이너 삭제

이 스크립트는 컨테이너 이름에 접두사에 따라 Azure Blob Storage의 컨테이너를 삭제합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 나머지 컨테이너 및 모든 관련된 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 컨테이너 이름 접두사를 기준으로 컨테이너를 삭제합니다. 표에 있는 각 항목은 명령 관련 설명서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 리소스 그룹 또는 구독의 지정된 저장소 계정 또는 모든 저장소 계정을 가져옵니다. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | 저장소 계정에 연결된 저장소 컨테이너를 나열합니다. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | 지정된 저장소 컨테이너를 제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 스토리지 PowerShell 스크립트 샘플은 [Azure Blob Storage에 대한 PowerShell 샘플](../blobs/storage-samples-blobs-powershell.md)에서 찾을 수 있습니다.
