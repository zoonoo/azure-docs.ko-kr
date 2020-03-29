---
title: SQL VM 리소스 공급자를 사용 하 여 Azure에서 대량 등록 SQL 가상 컴퓨터 | 마이크로 소프트 문서
description: SQL VM 리소스 공급자에 SQL Server VM을 일괄 등록하여 관리 용이성을 향상시킵니다.
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
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353878"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 Azure에서 대량 등록 SQL 가상 시스템

이 문서에서는 `Register-SqlVMs` PowerShell cmdlet을 사용하여 SQL VM 리소스 공급자와 Azure에서 SQL Server 가상 컴퓨터(VM)를 일괄 등록하는 방법에 대해 설명합니다.

cmdlet은 `Register-SqlVMs` 지정된 구독 목록, 리소스 그룹 또는 특정 가상 컴퓨터 목록에 있는 모든 가상 컴퓨터를 등록하는 데 사용할 수 있습니다. cmdlet은 가상 컴퓨터를 _경량_ 관리 모드로 등록한 다음 [보고서와 로그 파일을](#output-description)모두 생성합니다. 

등록 프로세스는 위험을 감수하지 않으며 가동 중지 시간이 없으며 SQL Server 또는 가상 컴퓨터를 다시 시작하지 않습니다. 

리소스 공급자에 대한 자세한 내용은 [SQL VM 리소스 공급자를](virtual-machines-windows-sql-register-with-resource-provider.md)참조하십시오. 

## <a name="prerequisites"></a>사전 요구 사항

SQL Server VM을 리소스 공급자에 등록하려면 다음이 필요합니다. 

- [리소스 공급자에 등록되어](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) 등록되지 않은 SQL Server 가상 컴퓨터를 포함하는 [Azure 구독입니다.](https://azure.microsoft.com/free/) 
- 가상 컴퓨터를 등록하는 데 사용되는 클라이언트 자격 증명은 다음 RBAC **역할(가상 컴퓨터 기여자,** **기여자**또는 **소유자)** 중 어느 한 가지에 존재합니다. 
- [Az PowerShell의](/powershell/azure/new-azureps-module-az)최신 버전. 
- [Az.SqlVirtualMachine의](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)최신 버전 .

## <a name="getting-started"></a>시작

계속하기 전에 먼저 스크립트의 로컬 복사본을 만들고 PowerShell 모듈로 가져온 다음 Azure에 연결해야 합니다. 

### <a name="create-script"></a>스크립트 만들기

스크립트를 만들려면 이 문서의 끝에서 [전체 스크립트를](#full-script) 복사하여 `RegisterSqlVMs.psm1`로컬로 저장합니다. 

### <a name="import-script"></a>스크립트 가져오기

스크립트가 만들어지면 Powershell 터미널에서 모듈로 가져올 수 있습니다. 

관리 PowerShell 터미널을 열고 파일을 저장한 `RegisterSqlVMs.psm1` 곳으로 이동합니다. 그런 다음 다음 PowerShell cmdlet을 실행하여 스크립트를 모듈로 가져옵니다. 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Azure에 연결

다음 PowerShell cmdlet을 사용하여 Azure에 연결합니다.

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>구독 목록에 있는 모든 VM 

다음 cmdlet을 사용하여 구독 목록에 모든 SQL Server 가상 컴퓨터를 등록합니다.

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

## <a name="all-vms-in-a-single-subscription"></a>단일 구독의 모든 VM

다음 cmdlet을 사용하여 모든 SQL Server 가상 컴퓨터를 단일 구독에 등록합니다. 

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

## <a name="all-vms-in-multiple-resource-groups"></a>여러 리소스 그룹의 모든 VM

다음 cmdlet을 사용하여 단일 구독 내의 여러 리소스 그룹에 모든 SQL Server 가상 컴퓨터를 등록합니다.

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

## <a name="all-vms-in-a-resource-group"></a>리소스 그룹의 모든 VM

다음 cmdlet을 사용하여 모든 SQL Server 가상 컴퓨터를 단일 리소스 그룹에 등록합니다. 

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

## <a name="specific-vms-in-single-resource-group"></a>단일 리소스 그룹의 특정 VM

다음 cmdlet을 사용하여 단일 리소스 그룹 내에서 특정 SQL Server 가상 컴퓨터를 등록합니다.

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

다음 cmdlet을 사용하여 특정 SQL Server 가상 컴퓨터를 등록합니다. 

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

`Register-SqlVMs` cmdlet을 사용할 때마다 보고서와 로그 파일이 모두 생성됩니다. 

### <a name="report"></a>보고서

보고서는 cmdlet이 `.txt` 시작된 `RegisterSqlVMScriptReport<Timestamp>.txt` 시간인 타임스탬프라는 파일로 생성됩니다. 보고서에는 다음과 같은 세부 정보가 나열되어 있습니다.

| **출력 값** | **설명** |
| :--------------  | :-------------- | 
| 액세스 권한이 없거나 자격 증명이 올바르지 않아 구독 등록에 실패한 수 | 이렇게 하면 제공된 인증에 문제가 있는 구독의 수와 목록이 제공됩니다. 자세한 오류는 구독 ID를 검색하여 로그에서 찾을 수 있습니다. | 
| RP에 등록되지 않았기 때문에 시도할 수 없는 구독 수 | 이 섹션에는 SQL VM 리소스 공급자에 등록되지 않은 구독 수 및 목록이 포함되어 있습니다. |
| 총 VM발견 | cmdlet에 전달된 매개 변수의 범위에서 발견된 가상 시스템의 개수입니다. | 
| VM이 이미 등록되었습니다. | 리소스 공급자에 이미 등록된 경우 건너뛴 가상 시스템의 수입니다. |
| 성공적으로 등록된 VM 수 | cmdlet을 실행한 후 성공적으로 등록된 가상 시스템의 수입니다. 등록된 가상 컴퓨터를 형식으로 `SubscriptionID, Resource Group, Virtual Machine`나열합니다. | 
| 오류로 인해 VM 수가 등록되지 못했습니다. | 일부 오류로 인해 등록하지 못한 가상 시스템의 수입니다. 오류에 대한 자세한 내용은 로그 파일에서 찾을 수 있습니다. | 
| VM 또는 VM의 돌풍 에이전트가 실행중일 때 건너뛴 VM 수 | 가상 컴퓨터또는 가상 시스템의 게스트 에이전트로 등록할 수 없는 가상 시스템의 개수 및 목록입니다. 가상 시스템 또는 게스트 에이전트가 시작되면 다시 시도할 수 있습니다. 자세한 내용은 로그 파일에서 찾을 수 있습니다. |
| Windows에서 SQL Server를 실행하지 않을 때 건너뛴 VM 수 | SQL Server를 실행하지 않거나 Windows 가상 시스템이 아닌 경우 건너뛴 가상 컴퓨터의 수입니다. 가상 컴퓨터는 형식으로 `SubscriptionID, Resource Group, Virtual Machine`나열됩니다. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>로그 

타임스탬프가 스크립트가 시작된 `VMsNotRegisteredDueToError<Timestamp>.log` 시간이라는 로그 파일에 오류가 기록됩니다. 오류가 구독 수준에 있는 경우 로그에 쉼표로 구분된 SubscriptionID와 오류 메시지가 포함됩니다. 가상 시스템 등록에 오류가 있는 경우 로그에 는 멤버쉽 ID, 리소스 그룹 이름, 가상 컴퓨터 이름, 오류 코드 및 쉼표로 구분된 메시지가 포함됩니다. 

## <a name="remarks"></a>설명

제공된 스크립트를 사용하여 리소스 공급자에 SQL Server VM을 등록할 때 다음을 고려하십시오.

- 리소스 공급자에 등록하려면 SQL Server VM에서 실행되는 게스트 에이전트가 필요합니다. Windows Server 2008 이미지에는 게스트 에이전트가 없으므로 이러한 가상 컴퓨터는 실패하고 [NoAgent 관리 모드를](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)사용하여 수동으로 등록해야 합니다.
- 투명한 오류를 극복하기 위해 재시도 논리가 내장되어 있습니다. 가상 시스템이 성공적으로 등록된 경우 빠른 작업입니다. 그러나 등록에 실패하면 각 가상 시스템이 다시 시도됩니다.  따라서 실제 시간 요구 사항은 오류의 유형과 수에 따라 다르지만 등록 프로세스를 완료하는 데 상당한 시간을 허용해야 합니다. 

## <a name="full-script"></a>전체 스크립트

GitHub의 전체 스크립트는 [Az PowerShell을 통해 SQL VM을 대량 등록하는](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)것을 참조하십시오. 

전체 스크립트를 복사하여 `RegisterSqLVMs.psm1`로 저장합니다.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL 서버에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM에서 SQL 서버에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
