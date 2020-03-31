---
title: Azure VM에서 실행 중인 SQL 서버에 대한 Azure 백업
description: 이 문서에서는 Azure 가상 컴퓨터에서 실행 중인 SQL Server에서 Azure Backup을 등록하는 방법을 알아봅니다.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247387"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM에서 실행 중인 SQL 서버에 대한 Azure 백업

Azure Backup은 다른 제품 과 함께 Azure VM에서 실행되는 SQL Server와 같은 워크로드 백업을 지원합니다. SQL 응용 프로그램이 Azure VM 내에서 실행중이므로 백업 서비스에는 응용 프로그램에 액세스하고 필요한 세부 정보를 가져올 수 있는 권한이 필요합니다.
이를 위해 Azure Backup은 사용자가 트리거한 등록 프로세스 중에 SQL Server가 실행 중인 VM에 **AzureBackupWindowsWorkload** 확장을 설치합니다.

## <a name="prerequisites"></a>사전 요구 사항

지원되는 시나리오 목록은 Azure Backup에서 지원하는 [지원 가능성 매트릭스를](../../backup/sql-support-matrix.md#scenario-support) 참조하십시오.

## <a name="network-connectivity"></a>네트워크 연결

Azure Backup은 NSG 태그를 지원하여 프록시 서버 또는 나열된 IP 범위를 배포합니다. 각 방법에 대한 자세한 내용은 이 [문서를](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)참조하십시오.

## <a name="extension-schema"></a>확장 스키마

확장 스키마 및 속성 값은 서비스가 CRP API에 전달하는 구성 값(런타임 설정)입니다. 이러한 구성 값은 등록 및 업그레이드 중에 사용됩니다. **AzureBackupWindows워크로드** 확장도 이 스키마를 사용합니다. 스키마는 미리 설정되어 있습니다. objectStr 필드에 새 매개 변수를 추가할 수 있습니다.

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

다음 JSON은 워크로드 백업 확장에 대한 스키마를 보여 주며, 이에 따라  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>속성 값

이름 | 값/예 | 데이터 형식
 --- | --- | ---
locale | ko-kr  |  문자열
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | 문자열
objectStr <br/> (공용)  | "eyJjb250YWluZXJQm9wZJ0aWVzIjp7IkNvbnNbnnnnbnnnwaWlcklEIjoiMzVjMjJQxYTItOgRjNy00ZGE5LWI4NTMtMjJJJJNDZlM2ZkIwiWWR212 GFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJZUlkIjoiMDU5NWIWIOGEtYzI4Zi00mFlLLWE5ODItOTKWOWMYMGVJNJVhIwiwiu3Vic2NyaXB0aWWWWWWWWWWWWWWWOI NgNgEzOTliNy1iYYAyLT2MWMtOdmYS1jNTM5ODI3ZTgzntQiLCJBbmlxdWDb250YWluZX조에이조이OD4MDUtNTQ4OS00NNNhWJZNZNZNZNZNZNZNZNZNZNZNZ Jg3OTcyIn0sInN0YW1wTGlzdCI6W3siUUU2VydmljZU5hbWuiUUUUsUsLnzpY2VTGFFBCI6Imh0dHA6XC9cL015V0xGYJJTDmIn1In1ini1tIn1 ==" | 문자열
commandStartTimeUTCTicks | "636967192566036845"  | 문자열
vmType  | "마이크로 소프트.컴퓨팅 / 가상 머신"  | 문자열
objectStr <br/> (보호설정) | "eyJjb250YWluZXJQm9wZJ0aWVzIjp7IkNvbnNbnnnnbnnnwaWlcklEIjoiMzVjMjJQxYTItOgRjNy00ZGE5LWI4NTMtMjJJJJNDZlM2ZkIwiWWR212 GFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJZUlkIjoiMDU5NWIWIOGEtYzI4Zi00mFlLLWE5ODItOTKWOWMYMGVJNJVhIwiwiu3Vic2NyaXB0aWWWWWWWWWWWWWWWOI NgNgEzOTliNy1iYYAyLT2MWMtOdmYS1jNTM5ODI3ZTgzntQiLCJBbmlxdWDb250YWluZX조에이조이OD4MDUtNTQ4OS00NNNhWJZNZNZNZNZNZNZNZNZNZNZNZ Jg3OTcyIn0sInN0YW1wTGlzdCI6W3siUUU2VydmljZU5hbWuiUUUUsUsLnzpY2VTGFFBCI6Imh0dHA6XC9cL015V0xGYJJTDmIn1In1ini1tIn1 ==" | 문자열
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 문자열
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 문자열

## <a name="template-deployment"></a>템플릿 배포

가상 컴퓨터에서 SQL Server 백업을 사용하도록 설정하여 AzureBackupWindowsWorkload 확장을 가상 시스템에 추가하는 것이 좋습니다. 이는 SQL Server VM에서 백업을 자동화하도록 설계된 [리소스 관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) 통해 수행할 수 있습니다.

## <a name="powershell-deployment"></a>PowerShell 배포

복구 서비스 자격 증명 모음을 사용하여 SQL 응용 프로그램을 포함하는 Azure VM을 '등록'해야 합니다. 등록 하는 동안 AzureBackupWindows워크로드 확장 VM에 설치 됩니다.  [레지스터-AzRecovery서비스백업컨테이너PS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet을 사용하여 VM을 등록합니다.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

명령은 이 리소스의 **백업 컨테이너를** 반환하고 상태가 **등록됩니다.**

## <a name="next-steps"></a>다음 단계

- Azure SQL Server VM 백업 문제 해결 지침에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)
- Azure 가상 시스템(VM)에서 실행되고 Azure 백업 서비스를 사용하는 SQL Server 데이터베이스 백업에 대한 [일반적인 질문입니다.](https://docs.microsoft.com/azure/backup/faq-backup-sql-server)
