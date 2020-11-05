---
title: Azure Migrate에서 에이전트 없는 VMware 마이그레이션 자동화
description: 스크립트를 사용 하 여 Azure Migrate에서 많은 수의 VMware Vm을 마이그레이션하는 방법을 설명 합니다.
author: rahulgup
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2020
ms.author: rahugup
ms.openlocfilehash: e1b34db7f2473e16d3ebde11376652f654f2f778
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377958"
---
# <a name="scale-migration-of-vmware-vms"></a>VMware Vm의 규모 마이그레이션 

이 문서는 에이전트 없는 메서드를 사용 하 여 스크립트를 사용 하 여 많은 수의 VMware Vm (가상 머신)을 마이그레이션하는 방법을 이해 하는 데 도움이 됩니다. 마이그레이션의 크기를 조정 하려면 [Azure Migrate PowerShell 모듈](https://aka.ms/azuremigratepowershellvmware)을 사용 합니다. 

Azure Migrate VMware 마이그레이션 자동화 스크립트는 GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) 리포지토리에서 다운로드할 수 있습니다. 스크립트를 사용 하 여 에이전트 없는 마이그레이션 방법을 사용 하는 VMware Vm을 Azure로 마이그레이션할 수 있습니다. 이러한 스크립트에서 사용 되는 Azure Migrate PowerShell 명령은 [여기](https://aka.ms/azuremigratepowershellvmware)에 설명 되어 있습니다.

## <a name="current-limitations"></a>현재 제한 사항
- 이러한 스크립트는 모든 디스크를 사용 하 여 VMware Vm의 마이그레이션을 지원 합니다. VMware VM에 연결 된 디스크를 선택적으로 복제 하려면 스크립트를 업데이트할 수 있습니다. 
- 스크립트는 평가 권장 사항의 사용을 지원 합니다. 평가 권장 사항을 사용 하지 않으면 VMware VM에 연결 된 모든 디스크가 동일한 관리 디스크 유형 (Standard 또는 Premium)으로 마이그레이션됩니다. 동일한 VM을 사용 하 여 여러 유형의 관리 디스크를 사용 하려는 경우 스크립트를 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [검색 자습서를 완료](tutorial-discover-vmware.md)하여 마이그레이션을 위한 Azure 및 VMware를 준비합니다.
- Azure로 마이그레이션하기 전에 [VMware VM을 평가](tutorial-assess-vmware.md)하는 두 번째 자습서를 완료하는 것이 좋습니다.
- Azure PowerShell `Az` 모듈이 있어야 합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="install-azure-migrate-powershell-module"></a>Azure Migrate PowerShell 모듈 설치

Azure Migrate PowerShell 모듈은 미리 보기로 제공됩니다. 다음 명령을 사용하여 PowerShell 모듈을 설치해야 합니다. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV 입력 파일
모든 필수 구성 요소가 완료 되 면 마이그레이션할 각 원본 VM에 대 한 데이터가 포함 된 CSV 파일을 만들어야 합니다. 모든 스크립트가 동일한 CSV 파일을 사용하도록 설계되었습니다. 참조용 샘플 CSV 템플릿이 스크립트 폴더에 제공됩니다. Csv 파일은 평가 권장 사항을 사용 하 고 특정 VM에 대해 특정 작업이 트리거되지 않을 지 여부를 지정할 수 있도록 구성할 수 있습니다. 

> [!NOTE]
> 동일한 csv 파일을 사용 하 여 여러 Azure Migrate 프로젝트에서 Vm을 마이그레이션할 수 있습니다.

### <a name="csv-file-schema"></a>CSV 파일 스키마

**열 머리글** | **설명**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Azure Migrate 프로젝트 구독 ID를 제공 합니다.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Azure Migrate 리소스 그룹 이름을 제공 합니다.
AZMIGRATEPROJECT_NAME | 서버를 마이그레이션하려는의 Azure Migrate 프로젝트 이름을 제공 합니다. 
SOURCE_MACHINE_NAME | Azure Migrate 프로젝트에서 검색 된 VM의 이름 (표시 이름)을 입력 합니다.
AZMIGRATEASSESSMENT_NAME | 마이그레이션에 활용 해야 하는 평가의 이름을 제공 합니다.
AZMIGRATEGROUP_NAME | Azure Migrate 평가에 사용 된 그룹의 이름을 제공 합니다.
TARGET_RESOURCE_GROUP_NAME | VM을 마이그레이션해야 하는 Azure 리소스 그룹의 이름을 제공 합니다.
TARGET_VNET_NAME| 마이그레이션된 VM에서 사용 해야 하는 Azure Virtual Network의 이름을 제공 합니다.
TARGET_SUBNET_NAME | 마이그레이션된 VM에서 사용 해야 하는 대상 가상 네트워크의 서브넷 이름을 제공 합니다. 비워 두면 "default" 서브넷이 사용 됩니다.
TARGET_MACHINE_NAME | 마이그레이션된 VM이 Azure에서 사용 해야 하는 이름을 제공 합니다. 비워 두면 원본 컴퓨터 이름이 사용 됩니다.  
TARGET_MACHINE_SIZE | VM이 Azure에서 사용 해야 하는 SKU를 제공 합니다. Azure에서 vm을 D2_v2 vm으로 마이그레이션하려면이 필드의 값을 "Standard_D2_v2"로 지정 합니다. 평가를 사용 하는 경우이 값은 평가 권장 사항에 따라 파생 됩니다.
LICENSE_TYPE | Windows Server Vm에 대해 Azure 하이브리드 혜택를 사용할지 여부를 지정 합니다. "WindowsServer" 값을 사용 하 여 Azure 하이브리드 혜택를 활용 합니다. 그렇지 않으면 비워 두거나 "NoLicenseType"를 사용 합니다.
OS_DISK_ID | 마이그레이션할 VM에 대 한 OS 디스크 ID를 제공 합니다. 사용할 디스크 ID는 Get-AzMigrateServer cmdlet을 사용 하 여 검색 된 디스크에 대 한 고유 식별자 (UUID) 속성입니다. 값을 제공 하지 않을 경우 스크립트는 VM의 첫 번째 디스크를 OS 디스크로 사용 합니다.
TARGET_DISKTYPE | Azure에서 VM의 모든 디스크에 사용할 디스크 유형을 제공 합니다. 프리미엄 관리 디스크에 ' Premium_LRS ', 표준 SSD 디스크의 경우 ' StandardSSD_LRS ', 표준 HDD 디스크를 사용 하려면 ' Standard_LRS '을 사용 하세요. 평가를 사용 하도록 선택 하는 경우 스크립트는 VM의 각 디스크에 권장 디스크 유형을 사용 하 여 우선 순위를 지정 합니다. 평가를 사용 하지 않거나 값을 지정 하지 않는 경우 스크립트는 기본적으로 표준 HDD 디스크를 사용 합니다.    
AVAILABILITYZONE_NUMBER | 마이그레이션된 VM에 사용할 가용성 영역 번호를 지정 합니다. 가용성 영역을 사용 하지 않으려는 경우에는이 값을 비워 둘 수 있습니다. 
AVAILABILITYSET_NAME | 마이그레이션된 VM에 사용할 가용성 집합의 이름을 지정 합니다. 가용성 집합을 사용 하지 않으려는 경우에는이 값을 비워 둘 수 있습니다.
TURNOFF_SOURCESERVER | 마이그레이션 시 원본 VM을 해제 하려면 ' Y '를 지정 합니다. 그렇지 않으면 ' N '을 사용 합니다. 이 인수를 비워 두면 스크립트는 값을 ' N '으로 가정 합니다.
TESTMIGRATE_VNET_NAME | 테스트 마이그레이션에 사용할 가상 네트워크의 이름을 지정 합니다.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Azure의 마이그레이션된 VM에서 사용할 리소스 그룹을 업데이트 하려면 Azure 리소스 그룹의 이름을 지정 하 고, 그렇지 않으면 비워 둡니다. 
UPDATED_TARGET_VNET_NAME | Azure의 마이그레이션된 VM에서 사용할 Virtual Network를 업데이트 하려면 Azure Virtual Network의 이름을 지정 하 고, 그렇지 않으면 비워 둡니다.
UPDATED_TARGET_MACHINE_NAME | Azure의 마이그레이션된 VM에서 사용할 이름을 업데이트 하려면 사용할 새 이름을 지정 하 고, 그렇지 않으면 비워 둡니다.
UPDATED_TARGET_MACHINE_SIZE | Azure의 마이그레이션된 VM에서 사용할 SKU를 업데이트 하려면 사용할 새 SKU를 지정 하 고, 그렇지 않으면 비워 둡니다.
UPDATED_AVAILABILITYZONE_NUMBER | Azure의 마이그레이션된 VM에서 사용할 가용성 영역을 업데이트 하려면 사용할 새 가용성 영역을 지정 하 고, 그렇지 않으면 비워 둡니다.
UPDATED_AVAILABILITYSET_NAME | Azure의 마이그레이션된 VM에서 사용할 가용성 집합을 업데이트 하려면 사용할 새 가용성 집합을 지정 하 고, 그렇지 않으면 비워 둡니다.
UPDATE_NIC1_ID | 업데이트할 NIC의 ID를 지정 합니다. 이 인수를 비워 두면 스크립트는 값을 검색 된 VM의 첫 번째 NIC로 가정 합니다. VM의 NIC를 업데이트 하지 않으려면 NIC 이름을 포함 하는 모든 필드를 비워 둡니다. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | 이 NIC에 사용할 값을 지정 합니다. "Primary", "Secondary" 또는 "DoNotCreate"를 사용 하 여이 NIC가 기본, 보조 또는 마이그레이션된 VM에서 만들어지지 않아야 하는지 여부를 지정 합니다. VM의 기본 NIC로 하나의 NIC만 지정할 수 있습니다. 업데이트 하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC1_SUBNET_NAME | 마이그레이션된 VM의 NIC에 사용할 서브넷의 이름을 지정 합니다. 업데이트 하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC1_IP | 고정 IP를 사용 하려는 경우 마이그레이션된 VM의 NIC에서 사용할 IPv4 주소를 지정 합니다. IP를 자동으로 할당 하려면 "자동"을 사용 합니다. 업데이트 하지 않으려면 비워 둡니다.
UPDATE_NIC2_ID | 업데이트할 NIC의 ID를 지정 합니다. 이 인수를 비워 두면 스크립트는 값을 검색 된 VM의 두 번째 NIC로 가정 합니다. VM의 NIC를 업데이트 하지 않으려면 NIC 이름을 포함 하는 모든 필드를 비워 둡니다.
UPDATED_TARGET_NIC2_SELECTIONTYPE | 이 NIC에 사용할 값을 지정 합니다. "Primary", "Secondary" 또는 "DoNotCreate"를 사용 하 여이 NIC가 기본, 보조 또는 마이그레이션된 VM에서 만들어지지 않아야 하는지 여부를 지정 합니다. VM의 기본 NIC로 하나의 NIC만 지정할 수 있습니다. 업데이트 하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC2_SUBNET_NAME | 마이그레이션된 VM의 NIC에 사용할 서브넷의 이름을 지정 합니다. 업데이트 하지 않으려면 비워 둡니다.
UPDATED_TARGET_NIC2_IP | 고정 IP를 사용 하려는 경우 마이그레이션된 VM의 NIC에서 사용할 IPv4 주소를 지정 합니다. IP를 자동으로 할당 하려면 "자동"을 사용 합니다. 업데이트 하지 않으려면 비워 둡니다.
OK_TO_UPDATE | ' Y '를 사용 하 여 AzMigrate_UpdateMachineProperties 스크립트를 실행할 때 VM 속성을 업데이트 해야 하는지 여부를 나타냅니다. ' N '을 사용 하거나 그렇지 않으면 비워 둡니다.
OK_TO_MIGRATE | ' Y '를 사용 하 여 AzMigrate_StartMigration 스크립트를 실행할 때 VM을 마이그레이션해야 하는지 여부를 나타냅니다. VM을 마이그레이션하지 않으려면 ' N '을 사용 하거나 비워 두세요. 
OK_TO_USE_ASSESSMENT | ' Y '를 사용 하 여 VM이 AzMigrate_StartReplication 스크립트를 실행할 때 평가 권장 사항을 사용 하 여 복제를 시작 해야 하는지 여부를 나타냅니다. 이렇게 하면 csv 파일의 TARGET_MACHINE_SIZE 및 TARGET_DISKTYPE 값이 재정의 됩니다. 평가 권장 사항을 사용 하지 않으려면 ' N '을 사용 하거나 비워 두세요.
OK_TO_TESTMIGRATE | ' Y '를 사용 하 여 AzMigrate_StartTestMigration 스크립트를 실행할 때 VM을 테스트로 마이그레이션해야 하는지 여부를 나타냅니다. VM의 마이그레이션을 테스트 하지 않으려면 ' N '을 사용 하거나 비워 두세요. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | ' Y '를 사용 하 여 AzMigrate_ReplicationStatus 스크립트를 실행할 때 VM의 복제 상태를 업데이트 해야 하는지 여부를 나타냅니다. 복제 상태를 업데이트 하지 않으려면 ' N '을 사용 하거나 비워 두세요.
OK_TO_CLEANUP | ' Y '를 사용 하 여 AzMigrate_StopReplication 스크립트를 실행할 때 VM에 대 한 복제를 정리 해야 하는지 여부를 나타냅니다. ' N '을 사용 하거나 그렇지 않으면 비워 둡니다.
OK_TO_TESTMIGRATE_CLEANUP | ' Y '를 사용 하 여 AzMigrate_CleanUpTestMigration 스크립트를 실행할 때 VM에 대 한 테스트 마이그레이션을 정리 해야 하는지 여부를 나타냅니다. ' N '을 사용 하거나 그렇지 않으면 비워 둡니다.


## <a name="script-execution"></a>스크립트 실행

CSV가 준비 되 면 다음 단계를 실행 하 여 온-프레미스 VMware Vm을 마이그레이션할 수 있습니다.

**단계 #** | **스크립트 이름** | **설명**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Csv에 나열 된 모든 Vm에 대해 복제를 사용 하도록 설정 하면 스크립트는 문제 해결을 위해 CSV 출력과 로그 파일을 만듭니다.
2 | AzMigrate_ReplicationStatus.ps1 | 복제 상태를 확인 합니다. 스크립트는 각 VM에 대 한 상태 및 문제 해결을 위한 로그 파일을 포함 하는 csv 출력을 만듭니다.
3 | AzMigrate_UpdateMachineProperties.ps1 | Vm이 초기 복제를 완료 한 후이 스크립트를 사용 하 여 VM (Compute 및 네트워크 속성)의 대상 속성을 업데이트 합니다. 이 스크립트는 각 VM에 대 한 작업 세부 정보를 포함 하는 CSV 출력을 만듭니다.
4 | AzMigrate_StartTestMigration.ps1 |  테스트 마이그레이션을 위해 구성 된 csv에 나열 된 모든 Vm에 대해 테스트 장애 조치 (failover)를 시작 합니다. 이 스크립트는 각 VM에 대 한 작업 세부 정보를 포함 하는 CSV 출력을 만듭니다.
5 | AzMigrate_CleanUpTestMigration.ps1 | 테스트 장애 조치 (failover) 된 Vm의 유효성을 수동으로 검사 한 후이 스크립트를 사용 하 여 테스트 마이그레이션 정리를 위해 구성 된 csv에 나열 된 모든 Vm에 대 한 테스트 장애 조치 (failover) Vm을 정리 합니다. 이 스크립트는 각 VM에 대 한 작업 세부 정보를 포함 하는 CSV 출력을 만듭니다.
6 | AzMigrate_StartMigration.ps1 | 마이그레이션에 대해 구성 된 csv에 나열 된 모든 Vm에 대해 마이그레이션을 시작 합니다. 이 스크립트는 각 VM에 대 한 작업 세부 정보를 포함 하는 CSV 출력을 만듭니다.
7 | AzMigrate_StopReplication.ps1 | VM이 성공적으로 마이그레이션된 후 또는 다른 이유로 인해 복제를 취소 하려는 경우 VM에 대 한 복제를 중지 합니다. 이 스크립트는 각 VM에 대 한 작업 세부 정보를 포함 하는 CSV 출력을 만듭니다.


다음 스크립트는 복제 설정, 테스트 마이그레이션 시작, VM 속성 업데이트 등의 모든 Azure Migrate 작업에 대해 다른 스크립트에서 호출 됩니다. 모든 스크립트가 동일한 폴더/경로에 있는지 확인 합니다. 

**단계 #** | **스크립트 이름** | **설명**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | API를 통해 평가 속성을 검색 하 고, 검색 된 Vm을 복제 하 고, Vm을 복제 하는 함수를 포함 하는 일반 스크립트 
2 | AzMigrate_CSV_Processor.ps1 | 로그 로드, 읽기 및 인쇄를 포함 하 여 csv 파일 작업에 사용 되는 함수를 포함 하는 공통 스크립트입니다. 
3 | AzMigrate_Logger.ps1 | Azure Migrate 자동화 작업에 대 한 로그 파일을 생성 하기 위해 호출 하는 일반 스크립트입니다. 로그 파일은 log.Scriptname.Datetime.txt 형식이 됩니다.

위의 외에도 폴더에는 다양 한 Azure Migrate 작업에 대 한 사용자 지정 스크립트를 빌드하기 위한 기본 프레임 워크를 포함 하는 AzMigrate_Template.ps1 포함 되어 있습니다. 

### <a name="script-execution-syntax"></a>스크립트 실행 구문

스크립트를 다운로드 한 후에는 다음과 같이 스크립트를 실행할 수 있습니다.

Input.csv 파일을 사용 하 여 Vm에 대 한 복제를 시작 하는 스크립트를 실행 하려면 다음 구문을 사용 합니다. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Azure Migrate를 사용 하 여 VMware Vm을 마이그레이션하는 Azure PowerShell를 사용 하는 방법에 대해 자세히 알아보려면 [자습서](https://aka.ms/azuremigratepowershellvmware)를 따르세요.