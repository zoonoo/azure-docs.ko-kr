---
title: 'PowerShell 스크립트: Azure Lab Services의 VHD 파일에서 사용자 지정 이미지 만들기 | Microsoft Docs'
description: 이 PowerShell 스크립트는 Azure Lab Services에서 VHD 파일을 사용하여 사용자 지정 이미지를 만듭니다.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 19b7c3c6018ec56b056761c336bc56c8b63b47a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636405"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>PowerShell을 사용하여 Azure Lab Services의 VHD 파일에서 사용자 지정 이미지 만들기

이 샘플 PowerShell 스크립트는 Azure Lab Services에서 VHD 파일을 사용하여 사용자 지정 이미지를 만듭니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>필수 조건
* **랩**. 스크립트를 사용하려면 기존 랩이 있어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | 리소스를 가져옵니다. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Azure Storage 계정의 선택키를 가져옵니다. |
| [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | 리소스 그룹에 Azure 배포를 추가합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Lab Services PowerShell 스크립트 샘플은 [Azure Lab Services PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.