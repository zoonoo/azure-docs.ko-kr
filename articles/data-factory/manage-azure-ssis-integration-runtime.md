---
title: "Azure-SSIS Integration Runtime 다시 구성 | Microsoft Docs"
description: "이미 프로비전한 Azure Data Factory에서 Azure-SSIS Integration Runtime을 다시 구성하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: spelluru
ms.openlocfilehash: c1743a0d06f911122ed0aba586aec837f81c578c
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2018
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 관리
[Azure-SSIS Integration Runtime 만들기](create-azure-ssis-integration-runtime.md) 문서는 Azure Data Factory를 사용하여 Azure-SSIS 통합 런타임을 만드는 방법을 보여줍니다. 이 문서에서는 기존 Azure-SSIS Integration Runtime을 다시 구성하는 방법을 설명합니다.  

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

Azure-SSIS 통합 런타임 인스턴스를 프로비전하고 시작한 후에는 일련의 `Stop` - `Set` - `Start` PowerShell cmdlet을 연속적으로 실행하여 다시 구성할 수 있습니다. 예를 들어 다음 PowerShell 스크립트는 Azure-SSIS 통합 런타임 인스턴스에 할당된 노드 수를 5로 변경합니다.

## <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR 다시 구성

1. 먼저 [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet을 사용하여 Azure-SSIS Integration Runtime을 중지합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 다음으로 [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet을 사용하여 Azure-SSIS IR을 다시 구성합니다. 다음 샘플 명령은 Azure-SSIS Integration Runtime을 5개 노드로 확장합니다.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet을 사용하여 Azure-SSIS Integration Runtime을 시작합니다. 이 명령은 SSIS 패키지 실행을 위한 모든 노드를 할당합니다.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS IR 삭제
1. 먼저 데이터 팩터리에 기존의 모든 Azure SSIS IRs를 나열합니다.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. 다음으로 데이터 팩터리에서 기존의 모든 Azure SSIS IRs를 중지합니다.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. 다음으로 데이터 팩터리에서 기존의 모든 Azure SSIS IRs를 제거합니다.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. 마지막으로 데이터 팩터리를 제거합니다.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. 새 리소스 그룹을 만들 때 리소스 그룹을 제거합니다.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>다음 단계
Azure-SSIS 런타임에 대한 자세한 내용은 다음 항목을 참조하세요. 

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL 관리되는 인스턴스(비공개 미리 보기)를 사용하고 IR을 VNet에 조인하는 지침을 제공합니다. 
- [Azure-SSIS IR을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서는 Azure-SSIS IR을 Azure 가상 네트워크(VNet)에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 VNet에 조인할 수 있도록 VNet을 구성하는 단계도 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보와 반환된 정보의 상태 설명을 검색하는 방법을 설명합니다. 
 
