---
title: Azure VM에서 실행 중인 SQL Server에 대 한 azure Backup
description: Azure VM에서 실행 되는 Azure Backup SQL Server를 등록 하는 방법
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607612"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM에서 실행 중인 SQL Server에 대 한 azure Backup

다른 제품 간에 azure Backup에는 SQL Server와 같은 워크 로드를 백업 하기 위한 Azure Vm에서 실행 중인 지원 합니다. SQL 응용 프로그램은 Azure VM 내에서 실행 중인, 백업 서비스 응용 프로그램에 액세스 하 고 필요한 정보를 가져올 수 있는 권한이 필요 합니다.
이렇게 하려면 Azure Backup을 설치 합니다 **AzureBackupWindowsWorkload** SQL Server 실행 되는, 사용자에 의해 트리거되는 등록 프로세스 중 VM에서 확장 합니다.

## <a name="prerequisites"></a>필수 구성 요소

지원 되는 시나리오 목록을 참조 합니다 [지원 가능성 행렬](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) Azure Backup에서 지원 합니다.

## <a name="network-connectivity"></a>네트워크 연결

Azure Backup에서 프록시 서버를 배포, NSG 태그를 지원 또는 IP 범위를 나열 합니다. 각 방법에 대 한 내용은이 참조할 [문서](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)합니다.

## <a name="extension-schema"></a>확장 스키마

확장 스키마 및 속성 값은 서비스는 CRP API를 전달 하는 구성 값 (런타임 설정). 이러한 구성 값은 등록 및 업그레이드 하는 동안 사용 됩니다. **AzureBackupWindowsWorkload** 확장이이 스키마를 사용할 수도 있습니다. 스키마는 미리 설정 되어 있습니다. objectStr 필드에 새 매개 변수를 추가할 수 있습니다.

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

다음 JSON WorkloadBackup 확장에 대 한 스키마를 보여 줍니다.  

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
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string


## <a name="template-deployment"></a>템플릿 배포

가상 머신에서 SQL Server backup을 사용 하 여 가상 머신에 AzureBackupWindowsWorkload 확장을 추가 하는 것이 좋습니다. 이 통해 수행할 수 있습니다 합니다 [Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) 백업을 SQL Server VM에서 자동화를 위한 합니다.


## <a name="powershell-deployment"></a>PowerShell 배포

Recovery services 자격 증명을 사용 하 여 SQL 응용 프로그램을 포함 하는 Azure VM '등록' 해야 합니다. 등록 하는 동안 AzureBackupWindowsWorkload 확장은 VM에 설치 됩니다. 사용 하 여 [레지스터 AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) VM을 등록 하는 cmdlet입니다.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
명령은 반환 합니다는 **백업 컨테이너** 이 리소스 및 상태 됩니다 **등록**합니다.


## <a name="next-steps"></a>다음 단계

- [자세한](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) Azure SQL Server VM 백업에 대 한 문제 해결 지침
- [일반적인 질문](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) Azure virtual machines (Vm)에서 실행 되는 Azure Backup 서비스를 사용 하는 SQL Server 데이터베이스를 백업 하는 방법에 대 한 합니다.
