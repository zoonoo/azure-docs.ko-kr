---
title: "Azure SSIS 통합 런타임 관리 | Microsoft Docs"
description: "이미 프로비저닝한 Azure Data Factory에서 Azure-SSIS 통합 런타임을 재구성하는 방법을 알아봅니다."
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
ms.date: 09/25/2017
ms.author: spelluru
ms.openlocfilehash: cc0ed958a9e1018ed9f06fdcc94873ae5420ba95
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2017
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 관리
[Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md) 문서는 Azure Data Factory를 사용하여 Azure-SSIS 통합 런타임을 만드는 방법을 보여줍니다. 이 문서는 Azure-SSIS 통합 런타임을 중지, 시작, 재구성 또는 제거하는 방법에 대한 정보를 제공하여 이를 보완합니다.  

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="stop"></a>중지 
Azure-SSIS 통합 런타임 중지. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>시작 
Azure-SSIS 통합 런타임 시작. 이 명령은 모든 노드를 할당하고 청구를 시작합니다.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>다시 구
Azure-SSIS 통합 런타임 인스턴스를 프로비전하고 시작한 후에는 일련의 `Stop` - `Set` - `Start` PowerShell cmdlet을 연속적으로 실행하여 다시 구성할 수 있습니다. 예를 들어 다음 PowerShell 스크립트는 Azure-SSIS 통합 런타임 인스턴스에 할당된 노드 수를 5로 변경합니다.

1. 먼저 다음 명령을 실행하여 Azure-SSIS 통합 런타임을 중지합니다.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 이제 Azure-SSIS 통합 런타임을 5개의 노드로 확장합니다.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 그런 다음 Azure-SSIS 통합 런타임을 시작합니다. 이렇게 하면 SSIS 패키지 실행을 위한 모든 노드가 할당됩니다.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>제거
기존 Azure-SSIS 통합 런타임을 제거하려면 다음 명령을 실행합니다. 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>다음 단계
Azure-SSIS 런타임에 대한 자세한 내용은 다음 항목을 참조하세요. 

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL 관리되는 인스턴스(비공개 미리 보기)를 사용하고 IR을 VNet에 조인하는 지침을 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보와 반환된 정보의 상태 설명을 검색하는 방법을 설명합니다. 
- [Azure-SSIS IR을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서는 Azure-SSIS IR을 Azure 가상 네트워크(VNet)에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 VNet에 조인할 수 있도록 VNet을 구성하는 단계도 제공합니다. 
