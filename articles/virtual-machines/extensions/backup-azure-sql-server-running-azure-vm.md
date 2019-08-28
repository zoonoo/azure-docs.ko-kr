---
title: Azure VM에서 실행 되는 SQL Server에 대 한 Azure Backup
description: Azure VM에서 실행 중인 Azure Backup SQL Server를 등록 하는 방법
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: vijayts
ms.openlocfilehash: 25f23078af67b2f80f39faab975cbec54721c560
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871894"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM에서 실행 되는 SQL Server에 대 한 Azure Backup

Azure Backup 다른 제공 중에는 Azure Vm에서 실행 되는 SQL Server와 같은 워크 로드를 백업 하는 기능이 제공 됩니다. SQL 응용 프로그램은 Azure VM 내에서 실행 되므로 backup 서비스에는 응용 프로그램에 액세스 하 고 필요한 세부 정보를 가져올 수 있는 권한이 필요 합니다.
이렇게 하려면 Azure Backup 사용자가 트리거한 등록 프로세스 중에 SQL Server 실행 중인 VM에 **Azurebackupwindowsworkload 로드** 확장을 설치 합니다.

## <a name="prerequisites"></a>필수 구성 요소

지원 되는 시나리오 목록은 Azure Backup에서 지 원하는 지원 [가능성 매트릭스](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) 를 참조 하세요.

## <a name="network-connectivity"></a>네트워크 연결

Azure Backup는 NSG 태그, 프록시 서버 또는 나열 된 IP 범위 배포를 지원 합니다. 각 방법에 대 한 자세한 내용은이 [문서](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)를 참조 하세요.

## <a name="extension-schema"></a>확장 스키마

확장 스키마 및 속성 값은 서비스가 CRP API에 전달 하는 구성 값 (런타임 설정)입니다. 이러한 구성 값은 등록 및 업그레이드 하는 동안 사용 됩니다. **Azurebackupwindowsworkload 로드** 확장도이 스키마를 사용 합니다. 스키마가 미리 설정 되어 있습니다. objectStr 필드에 새 매개 변수를 추가할 수 있습니다.

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

다음 JSON은 WorkloadBackup 확장에 대 한 스키마를 보여 줍니다.  

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

이름 | 값/예제 | 데이터 형식
 --- | --- | ---
로캘(locale) | en-us  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> 은 publicsettings  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string


## <a name="template-deployment"></a>템플릿 배포

가상 머신에서 SQL Server 백업을 사용 하도록 설정 하 여 가상 머신에 AzureBackupWindowsWorkload 로드 확장을 추가 하는 것이 좋습니다. 이는 SQL Server VM 백업 자동화를 위해 설계 된 [리소스 관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) 통해 수행할 수 있습니다.


## <a name="powershell-deployment"></a>PowerShell 배포

Recovery services 자격 증명 모음을 사용 하 여 SQL 응용 프로그램이 포함 된 Azure VM을 ' 등록 ' 해야 합니다. 등록 하는 동안 AzureBackupWindowsWorkload 로드 확장이 VM에 설치 됩니다.  [AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet을 사용 하 여 VM을 등록 합니다.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
명령을 통해이 리소스의 **백업 컨테이너가** 반환 되 고 상태가 **등록**됩니다.


## <a name="next-steps"></a>다음 단계

- Azure SQL Server VM 백업 문제 해결 지침에 대 한 [자세한](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) 정보
- Azure Vm (가상 컴퓨터)에서 실행 되 고 Azure Backup 서비스를 사용 하는 SQL Server 데이터베이스 백업에 대 한 [일반적인 질문](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) 입니다.
