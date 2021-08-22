---
title: Azure VM에서 실행 중인 SQL Server용 Azure Backup
description: 이 문서에서는 Azure 가상 머신에서 실행되는 SQL Server에 Azure Backup을 등록하는 방법에 대해 알아봅니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b63e5c7de8a7198d5631075e9748d13f72ddb354
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294052"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM에서 실행 중인 SQL Server용 Azure Backup

Azure Backup은 다른 제품 중에서도 Azure VM에서 실행되는 SQL Server와 같은 워크로드를 백업할 수 있도록 지원합니다. SQL 애플리케이션이 Azure VM 내에서 실행되고 있기 때문에 백업 서비스에는 애플리케이션에 액세스하고 필요한 세부 정보를 가져올 수 있는 권한이 있어야 합니다.
이렇게 하려면 Azure Backup은 사용자가 트리거한 등록 프로세스 중에 SQL Server가 실행 중인 VM에 **AzureBackupWindowsWorkload** 확장을 설치합니다.

## <a name="prerequisites"></a>필수 요건

지원되는 시나리오 목록은 Azure Backup에서 지원하는 [지원 가능성 매트릭스](../../backup/sql-support-matrix.md#scenario-support)를 참조하세요.

## <a name="network-connectivity"></a>네트워크 연결

Azure Backup은 NSG 태그, 프록시 서버 또는 나열된 IP 범위 배포를 지원합니다. 각 방법에 대한 자세한 내용은 이 [문서](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)를 참조하세요.

## <a name="extension-schema"></a>확장 스키마

확장 스키마 및 속성 값은 서비스가 CRP API에 전달하는 구성 값(런타임 설정)입니다. 이러한 구성 값은 등록 및 업그레이드하는 동안 사용됩니다. **AzureBackupWindowsWorkload** 확장도 이 스키마를 사용합니다. 스키마가 미리 설정되어 있습니다. objectStr 필드에 새 매개 변수를 추가할 수 있습니다.

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

다음 JSON은 WorkloadBackup 확장에 대한 스키마를 보여줍니다.  

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

Name | 값/예제 | 데이터 형식
 --- | --- | ---
locale | ko-kr  |  문자열
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | 문자열
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | 문자열
commandStartTimeUTCTicks | "636967192566036845"  | 문자열
vmType  | "microsoft.compute/virtualmachines"  | 문자열
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | 문자열
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 문자열
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | 문자열

## <a name="template-deployment"></a>템플릿 배포

가상 머신에서 SQL Server 백업을 사용하도록 설정하려면 AzureBackupWindowsWorkload 확장을 추가하는 것이 좋습니다. 이는 SQL Server VM 백업을 자동화하도록 설계된 [Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-vm-workload-backup)을 통해 수행할 수 있습니다.

## <a name="powershell-deployment"></a>PowerShell 배포

SQL 애플리케이션이 포함된 Azure VM을 Recovery Services 자격 증명 모음에 '등록'해야 합니다. 등록하는 동안 AzureBackupWindowsWorkload 확장이 VM에 설치됩니다.  [Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) cmdlet을 사용하여 VM을 등록합니다.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

이 명령을 사용하면 리소스의 **백업 컨테이너** 를 반환하고 상태는 **등록됨** 으로 변경됩니다.

## <a name="next-steps"></a>다음 단계

- Azure SQL Server VM 백업 문제 해결 지침에 대한 [자세한 정보](../../backup/backup-sql-server-azure-troubleshoot.md)
- Azure VM(가상 머신)에서 실행되고 Azure Backup 서비스를 사용하는 SQL Server 데이터베이스 백업에 대한 [일반적인 질문](../../backup/faq-backup-sql-server.yml)입니다.
