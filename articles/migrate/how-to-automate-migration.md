---
title: Azure Migrate에서 에이전트 없는 VMware 마이그레이션 자동화
description: 스크립트를 사용하여 Azure Migrate에서 대량의 VMware VM을 마이그레이션하는 방법을 설명합니다.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754269"
---
# <a name="scale-migration-of-vmware-vms"></a>VMware VM 마이그레이션 스케일링 

이 문서는 스크립트를 통해 에이전트가 없는 방식을 사용하여 대량의 VMware VM(가상 머신)을 마이그레이션하는 방법을 이해하는 데 도움이 됩니다. 마이그레이션을 스케일링하려면 [Azure Migrate PowerShell 모듈](./tutorial-migrate-vmware-powershell.md)을 사용합니다. 

Azure Migrate VMware 마이그레이션 자동화 스크립트는 GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) 리포지토리에서 다운로드할 수 있습니다. 스크립트를 사용하여 에이전트가 없는 마이그레이션 방식으로 VMware VM을 Azure로 마이그레이션할 수 있습니다. 해당 스크립트에서 사용되는 Azure Migrate PowerShell 명령은 [여기](./tutorial-migrate-vmware-powershell.md)에 나와 있습니다.

## <a name="current-limitations"></a>현재 제한 사항
- 관련 스크립트는 모든 디스크를 대상으로 한 VMware VM 마이그레이션을 지원합니다. VMware VM에 연결된 디스크를 선택적으로 복제하려는 경우 스크립트를 업데이트할 수 있습니다. 
- 스크립트는 평가 권장 사항의 사용을 지원합니다. 평가 권장 사항을 사용하지 않으면 VMware VM에 연결된 모든 디스크가 동일한 관리 디스크 유형(표준 또는 프리미엄)으로 마이그레이션됩니다. 동일한 VM을 사용하여 여러 유형의 관리 디스크를 사용하려는 경우 스크립트를 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [검색 자습서를 완료](tutorial-discover-vmware.md)하여 마이그레이션을 위한 Azure 및 VMware를 준비합니다.
- Azure로 마이그레이션하기 전에 [VMware VM을 평가](./tutorial-assess-vmware-azure-vm.md)하는 두 번째 자습서를 완료하는 것이 좋습니다.
- Azure PowerShell `Az` 모듈이 있어야 합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="install-azure-migrate-powershell-module"></a>Azure Migrate PowerShell 모듈 설치

Azure Migrate PowerShell 모듈은 미리 보기로 제공됩니다. 다음 명령을 사용하여 PowerShell 모듈을 설치해야 합니다. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV 입력 파일
필수 조건이 모두 완료되었으면 마이그레이션할 각 원본 VM에 대한 데이터가 포함된 CSV 파일을 만들어야 합니다. 모든 스크립트가 동일한 CSV 파일을 사용하도록 설계되었습니다. 참조용 샘플 CSV 템플릿이 스크립트 폴더에 제공됩니다. 평가 권장 사항을 사용하고 특정 VM에 대해 특정 작업이 트리거되지 않을지 여부를 지정하도록 csv 파일을 구성할 수 있습니다. 

> [!NOTE]
> 동일한 csv 파일을 사용하여 여러 Azure Migrate 프로젝트에서 VM을 마이그레이션할 수 있습니다.

### <a name="csv-file-schema"></a>CSV 파일 스키마

**열 머리글** | **설명**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Azure Migrate 프로젝트 구독 ID를 제공합니다.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Azure Migrate 리소스 그룹 이름을 제공합니다.
AZMIGRATEPROJECT_NAME | 서버를 마이그레이션할 Azure Migrate 프로젝트의 이름을 제공합니다. 
SOURCE_MACHINE_NAME | Azure Migrate 프로젝트에서 검색된 VM의 이름(표시 이름)을 제공합니다.
AZMIGRATEASSESSMENT_NAME | 마이그레이션에 활용할 평가의 이름을 제공합니다.
AZMIGRATEGROUP_NAME | Azure Migrate 평가에 사용된 그룹의 이름을 제공합니다.
TARGET_RESOURCE_GROUP_NAME | VM을 마이그레이션할 Azure 리소스 그룹의 이름을 제공합니다.
TARGET_VNET_NAME| 마이그레이션된 VM에서 사용해야 하는 Azure Virtual Network의 이름을 제공합니다.
TARGET_SUBNET_NAME | 마이그레이션된 VM에서 사용할 대상 가상 네트워크의 서브넷 이름을 제공합니다. 비워 두면 “default” 서브넷이 사용됩니다.
TARGET_MACHINE_NAME | 마이그레이션된 VM이 Azure에서 사용할 이름을 제공합니다. 비워 두면 원본 머신 이름이 사용됩니다.  
TARGET_MACHINE_SIZE | VM이 Azure에서 사용할 SKU를 제공합니다. VM을 Azure의 D2_v2 VM으로 마이그레이션하려면 이 필드의 값을 “Standard_D2_v2”로 지정합니다. 평가를 사용하는 경우 이 값은 평가 권장 사항에 따라 파생됩니다.
LICENSE_TYPE | Windows Server VM에 Azure 하이브리드 혜택을 사용할지 여부를 지정합니다. “WindowsServer” 값을 사용하여 Azure 하이브리드 혜택을 활용합니다. 사용하지 않으려면 비워 두거나 “NoLicenseType”을 사용합니다.
OS_DISK_ID | 마이그레이션할 VM의 OS 디스크 ID를 제공합니다. 사용할 디스크 ID는 Get-AzMigrateServer cmdlet을 사용하여 검색된 디스크의 고유 식별자(UUID) 속성입니다. 값이 제공되지 않는 경우 스크립트는 VM의 첫 번째 디스크를 OS 디스크로 사용합니다.
TARGET_DISKTYPE | Azure에서 VM의 모든 디스크에 사용할 디스크 유형을 제공합니다. 프리미엄 관리 디스크의 경우 ‘Premium_LRS’, 표준 SSD 디스크의 경우 ‘StandardSSD_LRS’, 표준 HDD 디스크를 사용하려면 ‘Standard_LRS’를 사용합니다. 평가를 사용하도록 선택하는 경우 스크립트는 VM의 각 디스크에 권장 디스크 유형을 사용하여 우선 순위를 지정합니다. 평가를 사용하지 않거나 값을 지정하지 않으면 스크립트는 기본적으로 표준 HDD 디스크를 사용합니다.    
AVAILABILITYZONE_NUMBER | 마이그레이션된 VM에 사용할 가용성 영역 번호를 지정합니다. 가용성 영역을 사용하지 않으려는 경우 이 값은 비워 둘 수 있습니다. 
AVAILABILITYSET_NAME | 마이그레이션된 VM에 사용할 가용성 집합의 이름을 지정합니다. 가용성 집합을 사용하지 않으려는 경우 이 값은 비워 둘 수 있습니다.
TURNOFF_SOURCESERVER | 마이그레이션 시 원본 VM을 해제하려면 ‘Y’를 지정합니다. 그러지 않으려면 ‘N’을 사용합니다. 비워 두면 스크립트는 값을 ‘N’으로 가정합니다.
TESTMIGRATE_VNET_NAME | 테스트 마이그레이션에 사용할 가상 네트워크의 이름을 지정합니다.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Azure의 마이그레이션된 VM에서 사용할 리소스 그룹을 업데이트하려면 Azure 리소스 그룹의 이름을 지정하고, 그러지 않으려면 비워 둡니다. 
UPDATED_TARGET_VNET_NAME | Azure의 마이그레이션된 VM에서 사용할 Virtual Network를 업데이트하려면 Azure Virtual Network의 이름을 지정하고, 그러지 않으려면 비워 둡니다.
UPDATED_TARGET_MACHINE_NAME | Azure의 마이그레이션된 VM에서 사용할 이름을 업데이트하려면 사용할 새 이름을 지정하고, 그러지 않으려면 비워 둡니다.
UPDATED_TARGET_MACHINE_SIZE | Azure의 마이그레이션된 VM에서 사용할 SKU를 업데이트하려면 사용할 새 SKU를 지정하고, 그러지 않으려면 비워 둡니다.
UPDATED_AVAILABILITYZONE_NUMBER | Azure의 마이그레이션된 VM에서 사용할 가용성 영역을 업데이트하려면 사용할 새 가용성 영역을 지정하고, 그러지 않으려면 비워 둡니다.
UPDATED_AVAILABILITYSET_NAME | Azure의 마이그레이션된 VM에서 사용할 가용성 집합을 업데이트하려면 사용할 새 가용성 집합을 지정하고, 그러지 않으려면 비워 둡니다.
UPDATE_NIC1_ID | 업데이트할 NIC의 ID를 지정합니다. 비워 두면 스크립트는 값을 검색된 VM의 첫 번째 NIC로 가정합니다. VM의 NIC를 업데이트하지 않으려면 NIC 이름을 포함하는 모든 필드를 비워 둡니다. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | 이 NIC에 사용할 값을 지정합니다. “Primary”, “Secondary” 또는 “DoNotCreate”를 사용하여 NIC가 기본인지 아니면 보조인지 또는 마이그레이션되는 VM에서 이 NIC를 만들지 않을 것인지 지정합니다. VM의 기본 NIC로 하나의 NIC만 지정할 수 있습니다. 업데이트하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC1_SUBNET_NAME | 마이그레이션된 VM에서 NIC에 사용할 서브넷의 이름을 지정합니다. 업데이트하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC1_IP | 고정 IP를 사용하려는 경우 마이그레이션된 VM의 NIC에서 사용할 IPv4 주소를 지정합니다. IP를 자동으로 할당하려면 “auto”를 사용합니다. 업데이트하지 않으려면 비워 둡니다.
UPDATE_NIC2_ID | 업데이트할 NIC의 ID를 지정합니다. 비워 두면 스크립트는 값을 검색된 VM의 두 번째 NIC로 가정합니다. VM의 NIC를 업데이트하지 않으려면 NIC 이름을 포함하는 모든 필드를 비워 둡니다.
UPDATED_TARGET_NIC2_SELECTIONTYPE | 이 NIC에 사용할 값을 지정합니다. “Primary”, “Secondary” 또는 “DoNotCreate”를 사용하여 NIC가 기본인지 아니면 보조인지 또는 마이그레이션되는 VM에서 이 NIC를 만들지 않을 것인지 지정합니다. VM의 기본 NIC로 하나의 NIC만 지정할 수 있습니다. 업데이트하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC2_SUBNET_NAME | 마이그레이션된 VM에서 NIC에 사용할 서브넷의 이름을 지정합니다. 업데이트하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC2_IP | 고정 IP를 사용하려는 경우 마이그레이션된 VM의 NIC에서 사용할 IPv4 주소를 지정합니다. IP를 자동으로 할당하려면 “auto”를 사용합니다. 업데이트하지 않으려면 비워 둡니다.
OK_TO_UPDATE | ‘Y’를 사용하여 AzMigrate_UpdateMachineProperties 스크립트를 실행할 때 VM 속성을 업데이트할지 여부를 나타냅니다. 그러지 않으려면 ‘N’을 사용하거나 비워 둡니다.
OK_TO_MIGRATE | ‘Y’를 사용하여 AzMigrate_StartMigration 스크립트를 실행할 때 VM을 마이그레이션할지 여부를 나타냅니다. VM을 마이그레이션하지 않으려면 ‘N’을 사용하거나 비워 둡니다. 
OK_TO_USE_ASSESSMENT | ‘Y’를 사용하여 AzMigrate_StartReplication 스크립트를 실행할 때 VM이 평가 권장 사항을 사용하여 복제를 시작할지 여부를 나타냅니다. 그러면 csv 파일의 TARGET_MACHINE_SIZE 및 TARGET_DISKTYPE 값이 재정의됩니다. 평가 권장 사항을 사용하지 않으려면 ‘N’을 사용하거나 비워 둡니다.
OK_TO_TESTMIGRATE | ‘Y’를 사용하여 AzMigrate_StartTestMigration 스크립트를 실행할 때 VM 마이그레이션을 테스트할지 여부를 나타냅니다. VM 마이그레이션을 테스트하지 않으려면 ‘N’을 사용하거나 비워 둡니다. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | ‘Y’를 사용하여 AzMigrate_ReplicationStatus 스크립트를 실행할 때 VM의 복제 상태를 업데이트할지 여부를 나타냅니다. 복제 상태를 업데이트하지 않으려면 ‘N’을 사용하거나 비워 둡니다.
OK_TO_CLEANUP | ‘Y’를 사용하여 AzMigrate_StopReplication 스크립트를 실행할 때 VM에 대한 복제를 정리할지 여부를 나타냅니다. 그러지 않으려면 ‘N’을 사용하거나 비워 둡니다.
OK_TO_TESTMIGRATE_CLEANUP | ‘Y’를 사용하여 AzMigrate_CleanUpTestMigration 스크립트를 실행할 때 VM에 대한 테스트 마이그레이션을 정리할지 여부를 나타냅니다. 그러지 않으려면 ‘N’을 사용하거나 비워 둡니다.


## <a name="script-execution"></a>스크립트 실행

CSV가 준비되면 다음 단계를 실행하여 온-프레미스 VMware VM을 마이그레이션할 수 있습니다.

**단계 #** | **스크립트 이름** | **설명**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | csv에 나열된 모든 VM에 대해 복제를 사용하도록 설정합니다. 이 스크립트는 CSV 출력과 문제 해결을 위한 로그 파일을 만듭니다.
2 | AzMigrate_ReplicationStatus.ps1 | 복제 상태를 확인합니다. 이 스크립트는 각 VM의 상태를 포함한 csv 출력과 문제 해결을 위한 로그 파일을 만듭니다.
3 | AzMigrate_UpdateMachineProperties.ps1 | VM이 초기 복제를 완료하면 이 스크립트를 사용하여 VM의 대상 속성(컴퓨팅 및 네트워크 속성)을 업데이트합니다. 이 스크립트는 각 VM의 작업 세부 정보가 포함된 CSV 출력을 만듭니다.
4 | AzMigrate_StartTestMigration.ps1 |  테스트 마이그레이션을 위해 구성된 csv에 있는 모든 VM에 대해 테스트 장애 조치(failover)를 시작합니다. 이 스크립트는 각 VM의 작업 세부 정보가 포함된 CSV 출력을 만듭니다.
5 | AzMigrate_CleanUpTestMigration.ps1 | 테스트 장애 조치(failover)가 이루어진 VM의 유효성을 수동으로 검사한 후 이 스크립트를 사용하여 테스트 마이그레이션 정리를 위해 구성된 csv에 있는 모든 VM의 테스트 장애 조치(failover) VM을 정리합니다. 이 스크립트는 각 VM의 작업 세부 정보가 포함된 CSV 출력을 만듭니다.
6 | AzMigrate_StartMigration.ps1 | 마이그레이션에 대해 구성된 csv에 있는 모든 VM에 대해 마이그레이션을 시작합니다. 이 스크립트는 각 VM의 작업 세부 정보가 포함된 CSV 출력을 만듭니다.
7 | AzMigrate_StopReplication.ps1 | VM이 성공적으로 마이그레이션된 후 또는 다른 이유로 인해 복제를 취소하려는 경우 VM 복제를 중지합니다. 이 스크립트는 각 VM의 작업 세부 정보가 포함된 CSV 출력을 만듭니다.


다음 스크립트는 복제 설정, 테스트 마이그레이션 시작, VM 속성 업데이트 등의 모든 Azure Migrate 작업에 대해 다른 스크립트에 의해 호출됩니다. 모든 스크립트는 동일한 폴더/경로에 있어야 합니다. 

**단계 #** | **스크립트 이름** | **설명**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | API를 통해 평가 속성을 검색하고, 검색된 VM을 복제하고, VM을 복제하는 함수를 포함하는 일반 스크립트입니다. 
2 | AzMigrate_CSV_Processor.ps1 | 로그 로드, 읽기, 출력을 비롯한 csv 파일 작업에 사용되는 함수가 포함된 일반 스크립트입니다. 
3 | AzMigrate_Logger.ps1 | Azure Migrate 자동화 작업에 대한 로그 파일을 생성하기 위해 호출되는 일반 스크립트입니다. 로그 파일은 log.Scriptname.Datetime.txt 형식입니다.

위 스크립트 외에도 폴더에는 다양한 Azure Migrate 작업과 관련된 사용자 지정 스크립트를 빌드할 수 있는 기본 프레임워크가 포함된 AzMigrate_Template.ps1이 포함되어 있습니다. 

### <a name="script-execution-syntax"></a>스크립트 실행 구문

스크립트를 다운로드한 후에는 다음과 같이 스크립트를 실행할 수 있습니다.

스크립트를 실행하여 Input.csv 파일을 사용해 VM 복제를 시작하려면 다음 구문을 사용합니다. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Azure Migrate를 통해 VMware VM을 마이그레이션하는 데 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [자습서](./tutorial-migrate-vmware-powershell.md)를 따르세요.