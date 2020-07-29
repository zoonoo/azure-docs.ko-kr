---
title: Azure Data Factory에서 통합 런타임 모니터링
description: Azure Data Factory에서 다양한 유형의 통합 런타임을 모니터링하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 14f9ab0b1c3b8b437e46a7b6a2d8b87f03442a02
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290515"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Azure Data Factory의 통합 런타임 모니터링

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration runtime** 은 여러 네트워크 환경에서 다양 한 데이터 통합 기능을 제공 하기 위해 ADF (Azure Data Factory)에서 사용 하는 계산 인프라입니다. Data Factory는 세 가지 유형의 통합 런타임을 제공합니다.

- Azure 통합 런타임
- 자체 호스팅 통합 런타임
- Azure SQL Server Integration Services (SSIS) Integration runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IR(통합 런타임) 인스턴스의 상태를 가져 오려면 다음 PowerShell 명령을 실행합니다. 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

이 cmdlet은 여러 유형의 통합 런타임에 대해 서로 다른 정보를 반환합니다. 이 문서는 각 통합 런타임 유형의 속성 및 상태에 대해 설명합니다.  

## <a name="azure-integration-runtime"></a>Azure 통합 런타임

Azure 통합 런타임의 컴퓨팅 리소스는 Azure에서 완전히 탄력적으로 관리됩니다. 다음 표에서는 **AzDataFactoryV2IntegrationRuntime** 명령에서 반환 된 속성에 대 한 설명을 제공 합니다.

### <a name="properties"></a>속성

다음 테이블은 Azure 통합 런타임에 대해 cmdlet에서 반환하는 속성에 대한 설명을 제공합니다.

| 속성 | 설명 |
-------- | ------------- | 
| Name | Azure 통합 런타임의 이름. |  
| 상태 | Azure 통합 런타임의 상태. | 
| 위치 | Azure 통합 런타임의 위치. Azure 통합 런타임의 위치에 대한 자세한 내용은 [통합 런타임 소개](concepts-integration-runtime.md)를 참조하세요. |
| DataFactoryName | Azure 통합 런타임이 속한 데이터 팩터리의 이름. | 
| ResourceGroupName | 데이터 팩터리가 속한 리소스 그룹의 이름.  |
| 설명 | 통합 런타임에 대한 설명.  |

### <a name="status"></a>상태

다음 테이블은 Azure 통합 런타임의 가능한 상태를 제공합니다.

| 상태 | 설명/시나리오 | 
| ------ | ------------------ |
| 온라인 | Azure 통합 런타임이 온라인 상태이며 사용할 준비가 되었습니다. | 
| 오프라인 | Azure 통합 런타임이 내부 오류로 인해 오프라인 상태입니다. |

## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임

이 섹션에서는 AzDataFactoryV2IntegrationRuntime cmdlet이 반환 하는 속성에 대해 설명 합니다. 

> [!NOTE] 
> 반환된 속성 및 상태에는 전반적인 자체 호스팅 통합 런타임 및 런타임의 각 노드에 대한 정보가 포함됩니다.  

### <a name="properties"></a>속성

다음 테이블은 **각 노드**의 속성 모니터링에 대한 설명을 제공합니다.

| 속성 | 설명 | 
| -------- | ----------- | 
| Name | 자체 호스팅 통합 런타임의 이름 및 이와 연결된 노드. 노드는 자체 호스팅 통합 런타임이 설치된 온-프레미스 Windows 컴퓨터입니다. |  
| 상태 | 전반적인 자체 호스팅 통합 런타임 및 각 노드의 상태. 예: 온라인/오프 라인/제한 됨/등 이러한 상태에 대 한 자세한 내용은 다음 섹션을 참조 하세요. | 
| 버전 | 자체 호스팅 통합 런타임 및 각 노드의 버전. 자체 호스팅 통합 런타임 버전은 그룹에 있는 노드의 대다수 버전을 기반으로 결정됩니다. 자체 호스팅 통합 런타임 설정에 다른 버전의 노드가 있는 경우 논리 자체 호스팅 통합 런타임과 버전 번호가 동일한 노드만 제대로 작동합니다. 다른 버전의 노드는 제한된 모드에 있으므로 수동으로 업데이트해야 합니다(자동 업데이트가 실패할 경우에만). | 
| 사용 가능한 메모리 | 자체 호스팅 통합 런타임 노드에서 사용 가능한 메모리. 이 값은 거의 실시간 스냅샷입니다. | 
| CPU 사용률 | 자체 호스팅 통합 런타임 노드의 CPU 사용률. 이 값은 거의 실시간 스냅샷입니다. |
| 네트워킹(수신/송신) | 자체 호스팅 통합 런타임 노드의 네트워크 사용률. 이 값은 거의 실시간 스냅샷입니다. | 
| 동시 작업(실행/제한) | **실행 중**. 각 노드에서 실행되는 작업 또는 태스크의 수입니다. 이 값은 거의 실시간 스냅샷입니다. <br/><br/>**제한**. 제한은 각 노드의 최대 동시 작업 수를 나타냅니다. 이 값은 컴퓨터 크기에 따라 정의됩니다. CPU, 메모리 또는 네트워크가 충분히 활용되지 않는 경우에도 작업 시간이 초과하면 제한을 늘려 고급 시나리오에서 동시 작업 실행을 강화할 수 있습니다. 이 기능은 단일 노드 자체 호스팅 통합 런타임에서도 사용할 수 있습니다. |
| 역할 | 다중 노드 자체 호스팅 통합 런타임에는 디스패처 및 작업자라는 두 가지 유형의 역할이 있습니다. 모든 노드는 작업자이며, 이는 모두 작업을 실행하는 데 사용할 수 있음을 의미합니다. 디스패처 노드는 하나만 존재합니다. 이 노드는 클라우드 서비스에서 작업/태스크를 가져와서 다른 작업자 노드에 발송하는 데 사용됩니다. 디스패처 노드는 작업자 노드이기도 합니다. |

자체 호스팅 통합 런타임에 둘 이상의 노드가 있는 경우(즉, 규모 확장 시나리오) 속성의 일부 설정이 더 적합합니다.

#### <a name="concurrent-jobs-limit"></a>동시 작업 제한

동시 작업 제한의 기본값은 머신 크기에 따라 설정됩니다. 이 값을 계산하는 데 사용된 요소는 머신의 RAM 크기 및 CPU 코어 수에 따라 달라집니다. 따라서 코어와 메모리가 많을 수록 동시 작업의 기본 제한이 높아집니다.

노드 수를 늘려서 규모를 확장할 수 있습니다. 노드의 수를 늘리면 동시 작업 제한은 사용 가능한 모든 노드의 동시 작업 제한 값의 합계입니다.  예를 들어 하나의 노드를 사용하여 최대 12개의 동시 작업을 실행할 수 있는 경우 세 가지 유사한 노드를 추가하면 최대 48개(4x12)의 동시 작업을 실행할 수 있습니다. 각 노드의 기본 값을 포함하는 낮은 리소스 사용량이 표시되는 경우에만 동시 작업 제한을 늘리는 것이 좋습니다.

Azure Portal에서 계산된 기본값을 재정의할 수 있습니다. 작성자 > 연결 > 통합 런타임 > 편집 > 노드 > 노드당 동시 작업 값 수정을 차례로 선택합니다. PowerShell [Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) 명령을 사용할 수도 있습니다.
  
### <a name="status-per-node"></a>상태(노드당)

다음 테이블은 자체 호스팅 통합 런타임 노드의 가능한 상태를 제공합니다.

| 상태 | 설명 |
| ------ | ------------------ | 
| 온라인 | 노드가 Data Factory 서비스에 연결되어 있습니다. |
| 오프라인 | 노드가 오프라인 상태입니다. |
| 업그레이드 중 | 노드가 자동 업데이트 중입니다. |
| 제한됨 | 연결 문제로 인해 제한되는 상태입니다. 8050 HTTP 포트 문제, 서비스 버스 연결 문제 또는 자격 증명 동기화 문제 때문일 수 있습니다. |
| 비활성 | 노드의 구성이 다른 주 노드의 구성과 다릅니다. |

다른 노드에 연결할 수 없을 때 노드가 비활성 상태일 수 있습니다.

### <a name="status-overall-self-hosted-integration-runtime"></a>상태(전반적인 자체 호스팅 통합 런타임)

다음 테이블은 자체 호스팅 통합 런타임의 가능한 상태를 제공합니다. 이 상태는 런타임에 속한 모든 노드의 상태에 따라 달라집니다. 

| 상태 | 설명 |
| ------ | ----------- | 
| 등록 필요 | 자체 호스팅 통합 런타임에 등록된 노드가 아직 없습니다. |
| 온라인 | 모든 노드가 온라인 상태입니다. |
| 오프라인 | 온라인 상태인 노드가 없습니다. |
| 제한됨 | 자체 호스팅 통합 런타임의 일부 노드가 정상 상태가 아닙니다. 이 상태는 일부 노드가 중단되었을 수 있다는 경고입니다. 이 상태는 디스패처/작업자 노드의 자격 증명 동기화 문제 때문일 수 있습니다. |

**AzDataFactoryV2IntegrationRuntimeMetric** cmdlet을 사용 하 여 자체 호스팅 통합 런타임 속성이 포함 된 JSON 페이로드 및 cmdlet을 실행 하는 동안 해당 스냅숏 값을 가져옵니다.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

샘플 출력(자체 호스팅 통합 런타임과 연결된 노드가 두 개라고 가정):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임

Azure-SSIS IR은 SSIS 패키지를 실행 하는 전용 Azure 가상 컴퓨터 (또는 노드)의 완전히 관리 되는 클러스터입니다. Azure-SSIS IR에서 SSIS 패키지 실행을 호출할 수 있습니다. 예를 들어 Azure 사용 SQL Server Data Tools (SSDT), AzureDTExec 명령줄 유틸리티, SSMS (T-sql on SQL Server Management Studio)/s q l Server 에이전트를 통해, ADF 파이프라인에서 SSIS 패키지 작업을 실행할 수 있습니다. Azure-SSIS IR 다른 ADF 작업을 실행 하지 않습니다. 프로 비전 되 면 Azure PowerShell, Azure Portal 및 Azure Monitor를 통해 전체/노드 관련 속성 및 상태를 모니터링할 수 있습니다.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure SSIS 통합 런타임 모니터링

다음 Azure PowerShell cmdlet을 사용 하 여 Azure-SSIS IR의 전체/노드 관련 속성 및 상태를 모니터링할 수 있습니다.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>속성

다음 표에서는 Azure-SSIS IR에 대해 위의 cmdlet에서 반환 하는 속성에 대해 설명 합니다.

| 속성/상태              | 설명                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Azure-SSIS IR를 만든 UTC 시간입니다. |
| 노드                        | 노드 관련 상태 (시작/사용/재생/사용 불가)와 실행 가능한 오류를 사용 하 여 Azure-SSIS IR의 할당 된/사용 가능한 노드입니다. |
| OtherErrors                  | Azure-SSIS IR에 대 한 비 노드의 특정 조치 가능한 오류입니다. |
| LastOperation                | 실패 한 경우 실행 가능한 오류가 발생 하 여 Azure-SSIS IR에 대 한 마지막 시작/중지 작업의 결과입니다. |
| 상태                        | Azure-SSIS IR의 전체 상태 (초기/시작/시작/중지/중지/중지)입니다. |
| 위치                     | Azure-SSIS IR 위치입니다. |
| NodeSize                     | Azure-SSIS IR의 각 노드 크기입니다. |
| NodeCount                    | Azure-SSIS IR의 노드 수입니다. |
| MaxParallelExecutionsPerNode | Azure-SSIS IR 노드당 최대 병렬 실행 수입니다. |
| CatalogServerEndpoint        | SSIS 카탈로그 (SSISDB)를 호스트 하는 기존 Azure SQL Database 서버 또는 관리 되는 인스턴스의 끝점입니다. |
| CatalogAdminUserName         | 기존 Azure SQL Database 서버 또는 관리 되는 인스턴스의 관리자 사용자 이름입니다. ADF는이 정보를 사용 하 여 사용자를 대신해 서 SSISDB를 준비 하 고 관리 합니다. |
| CatalogAdminPassword         | 기존 Azure SQL Database 서버 또는 관리 되는 인스턴스에 대 한 관리자 암호입니다. |
| CatalogPricingTier           | Azure SQL Database server에서 호스트 하는 SSISDB에 대 한 가격 책정 계층입니다.  SSISDB를 호스트하는 Azure SQL Managed Instance에는 해당되지 않습니다. |
| VNetId                       | 연결할 Azure-SSIS IR의 가상 네트워크 리소스 ID입니다. |
| 서브넷                       | 조인할 Azure-SSIS IR의 서브넷 이름입니다. |
| ID                           | Azure-SSIS IR의 리소스 ID입니다. |
| Type                         | Azure-SSIS IR IR 유형 (관리/자체 호스팅)입니다. |
| ResourceGroupName            | ADF와 Azure-SSIS IR를 만든 Azure 리소스 그룹의 이름입니다. |
| DataFactoryName              | ADF의 이름입니다. |
| Name                         | Azure-SSIS IR 이름입니다. |
| 설명                  | Azure-SSIS IR에 대 한 설명입니다. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>상태 (Azure-SSIS IR 노드당)

다음 표에서는 Azure-SSIS IR 노드의 가능한 상태를 제공 합니다.

| 노드 관련 상태 | Description |
| -------------------- | ----------- | 
| 시작 중             | 이 노드가 준비 중입니다. |
| 사용 가능            | 이 노드는 SSIS 패키지를 배포/실행할 준비가 되었습니다. |
| 재생 중            | 이 노드는 복구 중/다시 시작 중입니다. |
| Unavailable          | 이 노드는 SSIS 패키지를 배포/실행할 준비가 되지 않았으며 해결할 수 있는 조치 가능한 오류/문제를 포함 합니다. |

#### <a name="status-overall-azure-ssis-ir"></a>상태 (전체 Azure-SSIS IR)

다음 표에서는 가능한 Azure-SSIS IR의 전체 상태를 제공 합니다. 전체 상태는 Azure-SSIS IR에 속하는 모든 노드의 결합 된 상태에 따라 달라 집니다. 

| 전반적인 상태 | 설명 | 
| -------------- | ----------- | 
| Initial        | Azure-SSIS IR 노드가 할당/준비 되지 않았습니다. | 
| 시작 중       | Azure-SSIS IR 노드를 할당/준비 하 고 청구를 시작 했습니다. |
| 시작됨        | Azure-SSIS IR 노드가 할당/준비 되었으며 SSIS 패키지를 배포/실행할 준비가 되었습니다. |
| 중지 중       | Azure-SSIS IR 노드가 해제 되 고 있습니다. |
| 중지됨        | Azure-SSIS IR 노드가 릴리스 되었으며 청구가 중지 되었습니다. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Azure Portal에서 Azure SSIS 통합 런타임 모니터링

Azure Portal에서 Azure-SSIS IR를 모니터링 하려면 모든 통합 런타임을 볼 수 있는 ADF UI에서 **모니터** 허브의 **통합 런타임** 페이지로 이동 합니다.

![모든 통합 런타임 모니터링](media/monitor-integration-runtime/monitor-integration-runtimes.png)

그런 다음 Azure-SSIS IR 이름을 선택 하 여 해당 모니터링 페이지를 엽니다. 여기서 전체/노드 관련 속성 및 상태를 볼 수 있습니다.

![Azure-SSIS IR 모니터링](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

Azure-SSIS IR 모니터링 페이지의 **상태** 타일에서 전체 상태 (예: **실행 중** 또는 **중지 됨**)를 볼 수 있습니다. **실행** 상태를 선택 하면 Azure-SSIS IR를 중지 하는 라이브 **중지** 단추가 포함 된 창이 표시 됩니다. **중지 됨** 상태를 선택 하면 Azure-SSIS IR 시작 하는 라이브 **시작** 단추가 있는 창이 표시 됩니다. 팝업 창에는 Azure-SSIS IR에서 실행 되는 ssis 패키지 실행 작업을 사용 하 여 ADF 파이프라인을 자동 생성 하는 **ssis 패키지 실행** 단추도 있습니다 ( [adf 파이프라인에서 Ssis 패키지를 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)하 여 ssis 패키지 실행 참조) 및 **리소스 ID** 텍스트 상자 `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` isv (독립 소프트웨어 공급 업체)에서 추가 프리미엄/사용이 허가 된 SSIS 구성 요소를 구매 하 고 Azure-SSIS IR에 바인딩하는 데 사용할 수 있는 Azure-SSIS IR 리소스 ID ()를 복사할 수 있습니다 ( [Azure-SSIS IR에서 프리미엄/라이선스 구성 요소 설치](https://docs.microsoft.com/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)참조).

![Azure-SSIS IR 상태 타일 모니터링](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

패키지가 Azure SQL Database 서버 또는 관리 되는 인스턴스에 의해 호스팅되는 SSISDB에 저장 되는 프로젝트 배포 모델을 사용 하는 경우 Azure-SSIS IR 모니터링 페이지에 **SSISDB 서버 끝점** 타일이 표시 됩니다 ( [Azure-SSIS IR 배포 설정 구성](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)참조). 이 타일에서는 Azure SQL Database 서버 또는 관리 되는 인스턴스를 지정 하는 링크를 선택 하 여 창을 표시할 수 있습니다 .이 창에서 텍스트 상자에서 서버 끝점을 복사 하 고 SSMS에서 연결 하 여 패키지를 배포, 구성, 실행 및 관리할 수 있습니다. 팝업 창에서 **Azure SQL Database 또는 관리 되는 인스턴스 설정 보기** 링크를 선택 하 여 AZURE PORTAL에서 SSISDB를 다시 구성 하거나 크기를 조정할 수도 있습니다.

![Azure-SSIS IR 모니터-SSISDB 타일](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

Azure-SSIS IR VNet에 연결 하는 경우 Azure-SSIS IR 모니터링 페이지에서 **vnet/서브넷 유효성 검사** 타일이 표시 됩니다 ( [Vnet에 Azure-SSIS IR 조인](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)참조). 이 타일에서 vnet 및 서브넷을 지정 하 여 창을 팝업 하도록 지정 하는 링크를 선택할 수 있습니다. 여기서 vnet 리소스 ID ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` )와 서브넷 이름을 텍스트 상자에서 복사 하 고, 필요한 인바운드/아웃 바운드 네트워크 트래픽을 Azure-SSIS IR 관리가 방해 되지 않도록 vnet 및 서브넷 구성의 유효성을 검사할 수 있습니다.

![Azure-SSIS IR 모니터링-타일의 유효성을 검사 합니다.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

Azure-SSIS IR 모니터링 페이지의 연결 **진단** 타일에서 **연결 테스트** 링크를 선택 하 여 창을 표시할 수 있습니다. 여기서는 Azure-SSIS IR 및 관련 패키지/구성/데이터 저장소와 관리 서비스는 정규화 된 도메인 이름 (FQDN)/ip 주소 및 지정 된 포트를 통해 연결을 확인할 수 있습니다 ( [Azure-SSIS IR에서 연결 테스트](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-diagnose-connectivity-faq)참조).

![Azure-SSIS IR 모니터링-진단 타일](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

패키지 배포 모델을 사용 하는 경우 패키지는 Azure SQL Managed Instance에서 호스트 되 고 Azure-SSIS IR 패키지 저장소를 통해 관리 되는 파일 시스템/Azure Files/SQL Server 데이터베이스 (MSDB)에 저장 되며, Azure-SSIS IR 모니터링 페이지에 **패키지 저장소** 타일이 표시 됩니다 ( [Azure-SSIS IR 배포 설정 구성](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)참조). 이 타일에서 Azure-SSIS IR에 연결 된 패키지 저장소 수를 지정 하는 링크를 선택 하 여 창을 팝 할 수 있습니다 .이 링크를 통해 Azure-SSIS IR 패키지 저장소에 대 한 관련 연결 된 서비스를 Azure SQL Managed Instance에서 호스트 하는 파일 시스템/Azure Files/MSDB 위에 다시 구성할 수 있습니다.

![Azure-SSIS IR-패키지 타일 모니터링](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

Azure-SSIS IR의 시작/중지/유지 관리/업그레이드에 문제가 있는 경우 Azure-SSIS IR 모니터링 페이지에 추가 **오류** 타일이 표시 됩니다. 이 타일에서 창을 팝업 하기 위해 Azure-SSIS IR에서 생성 된 오류 수를 지정 하는 링크를 선택할 수 있습니다. 여기에서 해당 오류에 대 한 자세한 내용을 확인 하 고 복사 하 여 문제 해결 가이드에서 권장 해결 방법을 찾을 수 있습니다 ( [Azure-SSIS IR 문제 해결](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-management-troubleshoot)참조).

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Azure Monitor를 사용 하 여 Azure SSIS 통합 런타임 모니터링

Azure Monitor에서 Azure-SSIS IR를 모니터링 하려면 Azure Monitor를 [사용 하 여 SSIS 작업 모니터링](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#monitor-ssis-operations-with-azure-monitor)을 참조 하세요.

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임에 대한 자세한 정보

Azure-SSIS 통합 런타임에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md). 이 문서에서는 Azure-SSIS IR을 만들고 SQL Database를 사용 하 여 SSIS 카탈로그를 호스트 하는 단계별 지침을 제공 합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장 하 고 SQL Managed Instance를 사용 하 고 IR을 가상 네트워크에 가입 하는 방법에 대 한 지침을 제공 합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
- [Azure-SSIS IR을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서에서는 Azure-SSIS IR을 Azure 가상 네트워크에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 가상 네트워크에 조인할 수 있도록 가상 네트워크를 구성하는 단계도 제공합니다. 

## <a name="next-steps"></a>다음 단계
다른 방식으로 파이프라인을 모니터링하려면 다음 문서를 참조하세요. 

- [빠른 시작: 데이터 팩터리를 만듭니다](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor를 사용하여 Data Factory 파이프라인 모니터링](monitor-using-azure-monitor.md)