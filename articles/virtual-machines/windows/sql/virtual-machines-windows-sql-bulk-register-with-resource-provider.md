---
title: SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL 가상 머신 대량 등록 | Microsoft Docs
description: SQL VM 리소스 공급자를 사용 하 여 Vm을 대량 SQL Server 등록 하면 관리 효율성을 향상 시킬 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4ee9d651e1ec7807d191bc3393c0c280ce1e52f9
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790543"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL 가상 머신 대량 등록

이 문서에서는 `Register-SqlVMs` PowerShell cmdlet을 사용 하 여 SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server VM (가상 머신)을 대량 등록 하는 방법을 설명 합니다.

`Register-SqlVMs` cmdlet을 사용 하 여 지정 된 구독, 리소스 그룹 또는 특정 가상 컴퓨터 목록에 있는 모든 가상 컴퓨터를 등록할 수 있습니다. 이 cmdlet은 가상 컴퓨터를 _경량_ 관리 모드로 등록 한 다음 [보고서와 로그 파일](#output-description)을 모두 생성 합니다. 

등록 프로세스는 위험 없이 발생 하 고 가동 중지 시간이 없으며 SQL Server 또는 가상 머신을 다시 시작 하지 않습니다. 

리소스 공급자에 대 한 자세한 내용은 [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)를 참조 하세요. 

## <a name="prerequisites"></a>전제 조건

리소스 공급자에 SQL Server VM을 등록 하려면 다음이 필요 합니다. 

- [리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) 되었으며 등록 되지 않은 SQL Server 가상 컴퓨터를 포함 하는 [Azure 구독](https://azure.microsoft.com/free/) 입니다. 
- 가상 컴퓨터를 등록 하는 데 사용 되는 클라이언트 자격 증명은 다음 RBAC 역할 ( **가상 컴퓨터 참가자**, **참가자**또는 **소유자**) 중 하나에 존재 합니다. 
- 최신 버전의 [Az PowerShell](/powershell/azure/new-azureps-module-az)입니다. 
- 최신 버전의 [Az. SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>시작

계속 하기 전에 먼저 스크립트의 로컬 복사본을 만들고 PowerShell 모듈로 가져온 다음 Azure에 연결 해야 합니다. 

### <a name="create-script"></a>스크립트 만들기

스크립트를 만들려면이 문서의 끝에서 [전체 스크립트](#full-script) 를 복사 하 고 `RegisterSqlVMs.psm1`로 로컬로 저장 합니다. 

### <a name="import-script"></a>스크립트 가져오기

스크립트를 만든 후에는 Powershell 터미널에서 모듈로 가져올 수 있습니다. 

관리 PowerShell 터미널을 열고 `RegisterSqlVMs.psm1` 파일을 저장 한 위치로 이동 합니다. 그런 다음, 다음 PowerShell cmdlet을 실행 하 여 스크립트를 모듈로 가져옵니다. 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Azure에 연결

다음 PowerShell cmdlet을 사용 하 여 Azure에 연결 합니다.

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>구독 목록의 모든 Vm 

다음 cmdlet을 사용 하 여 구독 목록의 모든 SQL Server 가상 컴퓨터를 등록할 수 있습니다.

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

예제 출력: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>단일 구독의 모든 Vm

다음 cmdlet을 사용 하 여 단일 구독에 모든 SQL Server 가상 컴퓨터를 등록할 수 있습니다. 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

예제 출력:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>여러 리소스 그룹의 모든 Vm

다음 cmdlet을 사용 하 여 단일 구독 내에 있는 여러 리소스 그룹의 모든 SQL Server 가상 컴퓨터를 등록할 수 있습니다.

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

예제 출력:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>리소스 그룹의 모든 Vm

다음 cmdlet을 사용 하 여 단일 리소스 그룹의 모든 SQL Server 가상 컴퓨터를 등록할 수 있습니다. 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

예제 출력:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>단일 리소스 그룹의 특정 Vm

다음 cmdlet을 사용 하 여 단일 리소스 그룹 내의 특정 SQL Server 가상 컴퓨터를 등록할 수 있습니다.

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

예제 출력:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>특정 VM

다음 cmdlet을 사용 하 여 특정 SQL Server 가상 컴퓨터를 등록 합니다. 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

예제 출력:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>출력 설명

`Register-SqlVMs` cmdlet을 사용할 때마다 보고서와 로그 파일이 모두 생성 됩니다. 

### <a name="report"></a>보고서

보고서는 `RegisterSqlVMScriptReport<Timestamp>.txt` 이라는 `.txt` 파일로 생성 됩니다. 여기서 타임 스탬프는 cmdlet이 시작 된 시간입니다. 보고서에는 다음과 같은 세부 정보가 나열 됩니다.

| **출력 값** | **설명** |
| :--------------  | :-------------- | 
| 액세스 권한이 없거나 자격 증명이 잘못 되었기 때문에에 대 한 구독을 등록 하지 못했습니다. | 제공 된 인증에 문제가 발생 한 구독의 수와 목록을 제공 합니다. 자세한 오류는 로그에서 구독 ID를 검색 하 여 찾을 수 있습니다. | 
| RP에 등록 되지 않았기 때문에 시도 하지 못한 구독 수입니다. | 이 섹션에는 SQL VM 리소스 공급자에 등록 되지 않은 구독 개수 및 목록이 포함 되어 있습니다. |
| 총 Vm 수 | Cmdlet에 전달 된 매개 변수 범위에서 발견 된 가상 컴퓨터의 수입니다. | 
| 이미 등록 된 Vm | 리소스 공급자에 이미 등록 되어 있는 가상 컴퓨터의 수입니다. |
| 성공적으로 등록 된 Vm 수 | Cmdlet을 실행 한 후 성공적으로 등록 된 가상 컴퓨터의 수입니다. `SubscriptionID, Resource Group, Virtual Machine`형식으로 등록 된 가상 컴퓨터를 나열 합니다. | 
| 오류로 인해 등록 하지 못한 Vm 수 | 오류로 인해 등록 하지 못한 가상 컴퓨터의 수입니다. 오류에 대 한 세부 정보는 로그 파일에서 찾을 수 있습니다. | 
| Vm의 vm 또는 돌풍 에이전트가 실행 되 고 있지 않으므로 건너뛴 Vm 수 | 가상 컴퓨터 또는 가상 컴퓨터의 게스트 에이전트가 실행 되 고 있지 않아 등록할 수 없는 가상 컴퓨터의 개수 및 목록입니다. 가상 컴퓨터 또는 게스트 에이전트가 시작 되 면 이러한 작업을 다시 시도할 수 있습니다. 세부 정보는 로그 파일에서 찾을 수 있습니다. |
| Windows에서 SQL Server 실행 되 고 있지 않으므로 건너뛴 Vm 수 | SQL Server 실행 되 고 있지 않거나 Windows 가상 컴퓨터가 아니므로 건너뛴 가상 컴퓨터의 수입니다. 가상 컴퓨터는 `SubscriptionID, Resource Group, Virtual Machine`형식으로 나열 됩니다. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>로그 

오류는 `VMsNotRegisteredDueToError<Timestamp>.log` 라는 로그 파일에 기록 됩니다. 여기서 timestamp는 스크립트가 시작 된 시간입니다. 오류가 구독 수준에 있으면 로그에 쉼표로 구분 된 SubscriptionID 및 오류 메시지가 포함 됩니다. 가상 컴퓨터 등록에 오류가 있는 경우 로그에는 구독 ID, 리소스 그룹 이름, 가상 컴퓨터 이름, 오류 코드 및 쉼표로 구분 된 메시지가 포함 됩니다. 

## <a name="remarks"></a>설명

제공 된 스크립트를 사용 하 여 리소스 공급자에 SQL Server Vm을 등록 하는 경우 다음을 고려 하십시오.

- 리소스 공급자를 등록 하려면 SQL Server VM에서 실행 되는 게스트 에이전트가 필요 합니다. Windows Server 2008 이미지에는 게스트 에이전트가 없으므로 이러한 가상 컴퓨터는 실패 하므로 [noagent 관리 모드](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)를 사용 하 여 수동으로 등록 해야 합니다.
- 투명 오류를 해결 하기 위해 기본 제공 되는 재시도 논리가 있습니다. 가상 컴퓨터가 성공적으로 등록 되 면 신속한 작업입니다. 그러나 등록에 실패 하면 각 가상 머신이 다시 시도 됩니다.  따라서 실제 시간 요구 사항은 오류 유형 및 수에 따라 달라 지지만 등록 프로세스를 완료 하는 데 상당한 시간이 걸릴 수 있습니다. 

## <a name="full-script"></a>전체 스크립트

GitHub에 대 한 전체 스크립트는 [Az PowerShell을 사용 하 여 SQL Vm 대량 등록](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)을 참조 하세요. 

전체 스크립트를 복사 하 고 `RegisterSqLVMs.psm1`로 저장 합니다.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
