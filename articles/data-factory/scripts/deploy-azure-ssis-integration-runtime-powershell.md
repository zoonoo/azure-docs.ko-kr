---
title: "PowerShell 스크립트 ‑ Azure‑SSIS 통합 런타임 배포 | Microsoft Docs"
description: "이 PowerShell 스크립트는 클라우드에서 SSIS 패키지를 실행할 수 있는 Azure-SSIS 통합 런타임을 만듭니다."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: 9be0ed15f5bea06b499c696a63829f7898eb7b74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell 스크립트 ‑ Azure‑SSIS 통합 런타임 배포

이 샘플 PowerShell 스크립트는 Azure에서 SSIS 패키지를 실행할 수 있는 Azure-SSIS 통합 런타임을 만듭니다.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.

| 명령 | 참고 사항 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | 클라우드에서 SSIS 패키지를 실행할 수 있는 Azure-SSIS 통합 런타임을 만듭니다. |
| [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | Azure-SSIS 통합 런타임을 시작합니다. |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | Azure-SSIS 통합 런타임에 대한 정보를 가져옵니다. |
| [Get-AzureRmDataFactoryV2IntegrationRuntimeStatus](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntimestatus) | Azure-SSIS 통합 런타임의 상태에 대한 정보를 가져옵니다. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Cosmos DB PowerShell 스크립트 샘플은 [Azure Cosmos DB PowerShell 스크립트](../samples-powershell.md)에 있습니다.