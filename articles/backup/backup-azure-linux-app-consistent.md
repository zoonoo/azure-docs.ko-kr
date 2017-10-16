---
title: "Azure Backup: Linux VM의 응용 프로그램 일치 백업 | Microsoft Docs"
description: "Linux 가상 컴퓨터에 Azure에 대한 응용 프로그램 일치 백업을 보장하는 스크립트를 사용하세요. 스크립트는 Resource Manager 배포의 Linux VM에만 적용되며, Windows VM 또는 서비스 관리자 배포에는 적용되지 않습니다. 이 문서에서는 문제 해결을 비롯한 스크립트 구성 단계를 안내합니다."
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "앱 일치 백업, 응용 프로그램 일치 Azure VM 백업, Linux VM 백업, Azure Backup"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Azure Linux VM의 응용 프로그램 일치 백업(미리 보기)

이 문서에서는 Linux 사전 스크립트 및 사후 스크립트 프레임워크와 Azure Linux VM의 응용 프로그램 일치 백업을 수행하는 데 사용할 수 있는 방법에 대해 설명합니다.

> [!Note]
> 사전 스크립트 및 사후 스크립트 프레임워크는 Azure Resource Manager에서 배포한 Linux 가상 컴퓨터에 대해서만 지원됩니다. Service Manager 배포 가상 컴퓨터 또는 Windows 가상 컴퓨터에는 응용 프로그램 일관성 스크립트가 지원되지 않습니다.
>

## <a name="how-the-framework-works"></a>프레임워크의 작동 원리

프레임워크는 VM 스냅숏을 만드는 동안 사용자 지정 사전 스크립트 및 사후 스크립트를 실행하는 옵션을 제공합니다. 사전 스크립트는 VM 스냅숏을 작성하기 직전에 실행되고 사후 스크립트는 VM 스냅숏을 작성한 직후에 실행됩니다. 따라서 VM 스냅숏을 작성하는 동안 응용 프로그램 및 환경을 유연하게 제어할 수 있습니다.

이 시나리오에서는 응용 프로그램 일치 VM 백업을 확인하는 것이 중요합니다. 사전 스크립트는 응용 프로그램 네이티브 API를 호출하여 IO를 정지하고, 메모리 내 콘텐츠를 디스크에 플러시할 수 있습니다. 이를 통해 스냅숏이 응용 프로그램 일치 상태가 됩니다(즉, VM이 복원 후로 부팅될 때 응용 프로그램이 나타남). 사후 스크립트를 사용하여 IO를 재개할 수 있습니다. 사후 스크립트는 응용 프로그램이 VM 스냅숏 후 정상 작업을 다시 시작할 수 있도록 응용 프로그램 네이티브 API를 사용하여 이 작업을 수행합니다.

## <a name="steps-to-configure-pre-script-and-post-script"></a>사전 스크립트 및 사후 스크립트를 구성하는 단계

1. 백업하려는 Linux VM에 루트 사용자로 로그인합니다.

2. [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)에서 **VMSnapshotScriptPluginConfig.json**을 다운로드하고 백업하려는 모든 VM의 **/etc/azure** 폴더에 복사합니다. 존재하지 않는 경우 **/etc/azure** 디렉터리를 만듭니다.

3. 백업하려는 모든 VM의 응용 프로그램에 대한 사전 스크립트 및 사후 스크립트를 복사합니다. VM의 어떤 위치로도 스크립트를 복사할 수 있습니다. **VMSnapshotScriptPluginConfig.json** 파일에서 스크립트 파일의 전체 경로를 업데이트해야 합니다.

4. 다음 파일에 대해 다음과 같은 권한이 있는지 확인합니다.

   - **VMSnapshotScriptPluginConfig.json**: 권한 "600" 예를 들어 "루트" 사용자만 이 파일에 대한 "읽기" 및 "쓰기" 권한을 가져야 하며 사용자는 "실행" 권한을 가져서는 안됩니다.

   - **사전 스크립트 파일**: 권한 “700”  예를 들어 "루트" 사용자만 이 파일에 대한 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다.
  
   - **사후 스크립트**: 권한 “700” 예를 들어 "루트" 사용자만 이 파일에 대한 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다.

   > [!Important]
   > 이 프레임워크는 사용자에게 강력한 권한을 부여합니다. 또한 안전하며 "root" 사용자에게만 중요한 JSON 및 스크립트에 대한 액세스 권한을 부여합니다.
   > 이전 요구 사항이 충족되지 않으면 스크립트가 실행되지 않습니다. 이로 인해 파일 시스템/충돌 일치 백업이 생성됩니다.
   >

5. 다음에 설명된 대로 **VMSnapshotScriptPluginConfig.json**을 구성합니다.
    - **pluginName**: 이 필드를 있는 그대로 둡니다. 그렇지 않으면 스크립트가 예상대로 작동하지 않을 수 있습니다.

    - **preScriptLocation**: 백업될 VM에서 사전 스크립트의 전체 경로를 제공합니다.

    - **postScriptLocation**: 백업될 VM에서 사후 스크립트의 전체 경로를 제공합니다.

    - **preScriptParams**: 사전 스크립트에 전달해야 하는 선택적 매개 변수를 제공합니다. 모든 매개 변수는 큰따옴표로 묶어야 하며 여러 개의 매개 변수는 쉼표로 구분해야 합니다.

    - **postScriptParams**: 사후 스크립트에 전달해야 하는 선택적 매개 변수를 제공합니다. 모든 매개 변수는 큰따옴표로 묶어야 하며 여러 개의 매개 변수는 쉼표로 구분해야 합니다.

    - **preScriptNoOfRetries**: 종료하기 전에 모든 오류에서 사전 스크립트가 다시 시도되어야 하는 횟수를 설정합니다. 0은 한 번의 시도를 의미하고 실패의 경우 시도 없음을 의미합니다.

    - **postScriptNoOfRetries**: 종료하기 전에 모든 오류에서 사후 스크립트가 다시 시도되어야 하는 횟수를 설정합니다. 0은 한 번의 시도를 의미하고 실패의 경우 시도 없음을 의미합니다.
    
    - **timeoutInSeconds**: 사전 스크립트 및 사후 스크립트에 대한 개별 시간 제한을 지정합니다.

    - **continueBackupOnFailure**: Azure Backup을 사전 스크립트 또는 사후 스크립트가 실패하는 경우 파일 시스템 일관성/충돌 일관성 백업으로 대체하려는 경우 이 값을 **true**로 설정합니다. 이 값을 **false**로 설정하면 스크립트 오류가 발생하는 경우 백업에 실패합니다(이 설정과 관련 없는 충돌 일관성 백업으로 대체하는 단일 디스크 VM의 경우 제외).

    - **fsFreezeEnabled**: 파일 시스템 일관성을 유지하는 VM 스냅숏을 만드는 동안 Linux fsfreeze를 호출해야 하는지 여부를 지정합니다. 응용 프로그램이 fsfreeze 비활성화에 대한 종속성을 갖지 않는 한, 이 설정을 **true**로 유지하는 것이 좋습니다.

6. 이제 스크립트 프레임워크가 구성되었습니다. VM 백업이 이미 구성된 경우 다음 백업 시 스크립트가 호출되고 응용 프로그램 일치 백업이 트리거됩니다. VM 백업이 구성되지 않은 경우 [Recovery Services 자격 증명 모음에 Azure Virtual Machines 백업](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)을 사용하여 구성하세요.

## <a name="troubleshooting"></a>문제 해결

사전 스크립트 및 사후 스크립트를 작성하는 동안 적절한 로깅을 추가했는지 확인하고 스크립트 로그를 검토하여 모든 스크립트 문제를 해결하세요. 스크립트를 실행하는 데 여전히 문제가 있는 경우 자세한 내용은 다음 표를 참조하세요.

| 오류 | 오류 메시지 | 권장 작업 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |사전 스크립트가 오류를 반환하여 백업이 응용 프로그램에 일관되지 않을 수 있습니다.   | 이 문제를 해결하려면 스크립트에 대한 오류 로그를 확인하세요.|  
|   Post-ScriptExecutionFailed |    사후 스크립트가 응용 프로그램 상태에 영향을 줄 수 있는 오류를 반환했습니다. |    이 문제를 해결하려면 스크립트에 대한 오류 로그를 확인하고 응용 프로그램 상태를 확인하세요. |
| Pre-ScriptNotFound |  **VMSnapshotScriptPluginConfig.json** 구성 파일에 지정된 위치에서 사전 스크립트를 찾지 못했습니다. |   응용 프로그램 일관성 백업을 위해 사전 스크립트가 구성 파일에 지정된 경로에 있는지 확인하세요.|
| Post-ScriptNotFound | **VMSnapshotScriptPluginConfig.json** 구성 파일에 지정된 위치에서 사후 스크립트를 찾지 못했습니다. |   응용 프로그램 일관성 백업을 위해 사후 스크립트가 구성 파일에 지정된 경로에 있는지 확인하세요.|
| IncorrectPluginhostFile | VmSnapshotLinux 확장과 함께 제공되는 **Pluginhost** 파일이 손상되어서 사전 스크립트 및 사후 스크립트를 실행할 수 없으며 백업이 응용 프로그램에 일관되지 않습니다. | **VmSnapshotLinux** 확장을 제거하세요. 문제를 해결하기 위해 다음 백업과 함께 자동으로 다시 설치됩니다. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.json** 파일이 잘못되어서 사전 스크립트 및 사후 스크립트를 실행할 수 없으며 백업이 응용 프로그램에 일관되지 않습니다. | [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)에서 복사본을 다운로드하고 다시 구성하세요. |
| InsufficientPermissionforPre-Script | 스크립트 실행을 위해 “루트” 사용자는 파일의 소유자여야 하며 파일은 "700" 권한을 가져야 합니다. 즉, “소유자”만이 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다. | "루트" 사용자가 스크립트 파일의 "소유자"이고 “소유자”만이 "읽기", "쓰기" 및 "실행" 권한을 갖는지 확인합니다. |
| InsufficientPermissionforPost-Script | 스크립트 실행을 위해 루트 사용자는 파일의 소유자여야 하며 파일은 "700" 권한을 가져야 합니다. 즉, “소유자”만이 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다. | "루트" 사용자가 스크립트 파일의 "소유자"이고 “소유자”만이 "읽기", "쓰기" 및 "실행" 권한을 갖는지 확인합니다. |
| Pre-ScriptTimeout | 응용 프로그램 일치 백업 사전 스크립트의 실행이 시간 초과되었습니다. | 스크립트를 확인하고 **/etc/azure**에 있는 **VMSnapshotScriptPluginConfig.json** 파일에서 시간 제한을 늘립니다. |
| Post-ScriptTimeout | 응용 프로그램 일치 백업 사후 스크립트의 실행이 시간 초과되었습니다. | 스크립트를 확인하고 **/etc/azure**에 있는 **VMSnapshotScriptPluginConfig.json** 파일에서 시간 제한을 늘립니다. |

## <a name="next-steps"></a>다음 단계
[Recovery Services 자격 증명 모음에 VM 백업 구성](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
