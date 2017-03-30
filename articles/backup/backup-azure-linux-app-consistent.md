---
title: "Azure Backup: Azure Linux VM의 응용 프로그램 일치 백업 | Microsoft Docs"
description: "Azure Linux VM의 응용 프로그램 일치 백업"
services: backup
documentationcenter: dev-center-name
author: anuragm
manager: shivamg
keywords: "앱 일치 백업, 응용 프로그램 일치 Azure VM 백업, Linux VM 백업, Azure Backup"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 3/20/2017
ms.author: anuragm;markgal
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 044b5d3834518b44209485d1c1a68f2ac0f8a455
ms.lasthandoff: 03/22/2017


---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Azure Linux VM의 응용 프로그램 일치 백업(미리 보기)

이 문서는 Linux 사전 및 사후 스크립트 프레임워크와 Azure Linux VM의 응용 프로그램 일치 백업을 수행하는 데 사용할 수 있는 방법에 대해 설명합니다.

> [!Note]
> 사전 및 사후 스크립트 프레임워크는 Linux 가상 컴퓨터에 배포된 리소스 관리자에 대해서만 지원되며 클래식 가상 컴퓨터 또는 Windows 가상 컴퓨터에 대해 지원되지 않습니다.
>

## <a name="how-the-framework-works"></a>프레임워크의 작동 원리

프레임워크는 VM 스냅숏을 만드는 동안 사용자 지정 사전 및 사후 스크립트를 실행하는 옵션을 제공합니다. 사전 스크립트는 VM 스냅숏을 만들기 직전에 실행되며 사후 스크립트는 VM 스냅숏을 완료한 후 즉시 실행됩니다. 이는 VM 백업을 수행하는 동안 응용 프로그램 및 환경을 제어하는 사용자에게 유연성을 제공합니다.

이 프레임워크에 대한 중요한 시나리오는 응용 프로그램 일치 VM 백업을 확인하는 것입니다. 사전 스크립트는 응용 프로그램 네이티브 API를 호출하여 디스크에 대한 메모리 콘텐츠의 IO 및 플러시를 정지할 수 있습니다. 이를 통해 스냅숏은 응용 프로그램 일치, 즉 VM이 복원 후 부팅될 때 응용 프로그램이 나타나는 것을 확인합니다. 사후 스크립트는 응용 프로그램이 VM 스냅숏 후 정상 작업을 다시 시작할 수 있도록 응용 프로그램 네이티브 API를 사용하는 IO를 해동하는 데 사용할 수 있습니다.

## <a name="steps-to-configure-pre-script-and-post-script"></a>사전 스크립트 및 사후 스크립트를 구성하는 단계

1. 루트 사용자로 백업되도록 Linux VM에 로그인합니다.

2. [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)에서 VMSnapshotPluginConfig.json을 다운로드하고 백업될 모든 VM의 /etc/azure 폴더에 복사합니다. 존재하지 않는 경우 /etc/azure 디렉터리를 만듭니다.

3. 백업될 모든 VM의 응용 프로그램에 대한 사전 스크립트 및 사후 스크립트를 복사합니다. VM의 모든 위치에서 스크립트를 복사할 수 있습니다. VMSnapshotPluginConfig.json 파일에 있는 스크립트 파일의 전체 경로를 업데이트해야 합니다.

4. 파일에 대한 다음 권한을 확인합니다.

   - VMSnapshotPluginConfig.json - 권한 "600", 즉 "루트" 사용자만 이 파일에 대한 "읽기" 및 "쓰기" 권한을 가져야 하며 사용자는 "실행" 권한을 가져서는 안됩니다.
   - 사전 스크립트 파일 - 권한 "700", 즉 "루트" 사용자만 이 파일에 대한 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다.
   - 사후 스크립트 - 권한 "700", 즉 "루트" 사용자만 이 파일에 대한 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다.
   
   > [!Note]
   > 프레임워크는 사용자에게 많은 권한을 제공하므로 완전히 안전하고 "루트" 사용자만 중요한 json 및 스크립트 파일에 대한 액세스 권한을 가져야 하는 것은 매우 중요합니다.
   > 위의 요구 사항이 충족되지 않는 경우 스크립트는 실행되지 않으며 파일 시스템/충돌 일관성 백업이 발생합니다.
   >
   
5. 아래 세부 내용별 VMSnapshotPluginConfig.json 구성
    - **pluginName** - 이 필드를 있는 그대로 둡니다. 그렇지 않은 경우 스크립트는 예상대로 작동하지 않을 수 있습니다.
    - **preScriptLocation** - 백업될 VM에서 사전 스크립트의 전체 경로를 제공합니다.
    - **postScriptLocation** - 백업될 VM에서 사후 스크립트의 전체 경로를 제공합니다.
    - **preScriptParams** - 사전 스크립트에 전달되어야 하는 선택적 매개 변수, 모든 매개 변수는 여러 매개 변수인 경우 따옴표로 묶여 있고 쉼표로 구분되어야 합니다.
    - **postScriptParams** - 사후 스크립트에 전달되어야 하는 선택적 매개 변수, 모든 매개 변수는 여러 매개 변수인 경우 따옴표로 묶여 있고 쉼표로 구분되어야 합니다.
    - **preScriptNoOfRetries** - 종료하기 전에 모든 오류에서 사전 스크립트가 다시 시도되어야 하는 횟수입니다. 0은 한 번의 시도를 의미하고 실패의 경우 시도 없음을 의미합니다.
    - **postScriptNoOfRetries** - 종료하기 전에 모든 오류에서 사후 스크립트가 다시 시도되어야 하는 횟수입니다. 0은 한 번의 시도를 의미하고 실패의 경우 시도 없음을 의미합니다.
    - **timeoutInSeconds** - 사전 스크립트 및 사후 스크립트에 대한 개별 시간 제한입니다.
    - **continueBackupOnFailure** - Azure Backup을 사전 스크립트 또는 사후 스크립트가 실패하는 경우 파일 시스템 일관성/충돌 일관성 백업으로 대체하려는 경우 이 값을 true로 설정합니다. 이 값을 false로 설정하면 스크립트 오류가 발생하는 경우 백업에 실패합니다(이 설정과 관련 없는 충돌 일관성 백업으로 대체하는 단일 디스크 VM의 경우 제외).
    - **fsFreezeEnabled** - 파일 시스템 일관성을 유지하는 VM 스냅숏을 만드는 동안 Linux fsfreeze를 호출해야 하는 경우를 지정합니다. 응용 프로그램이 fsfreeze 비활성화에 대한 종속성을 갖지 않는 한 true로 유지하는 것이 좋습니다.
    
6. 이제 스크립트 프레임워크가 구성되었으므로 VM 백업이 이미 구성된 경우 다음 백업은 스크립트를 호출하고 응용 프로그램 일치 백업을 트리거합니다. VM 백업이 구성되지 않은 경우 [Recovery Services 자격 증명 모음에 Azure 가상 컴퓨터 백업](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)을 사용하여 구성하세요.

## <a name="troubleshooting"></a>문제 해결

사전 스크립트 및 사후 스크립트를 작성하는 동안 적절한 로깅을 추가했는지 확인하고 스크립트 로그를 검토하여 모든 스크립트 문제를 해결하세요. 스크립트를 실행하는 데 여전히 문제가 있는 경우 자세한 내용은 다음 표를 참조하세요.

| 오류 | 오류 메시지 | 권장 작업 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |사전 스크립트가 오류를 반환하여 백업이 응용 프로그램에 일관되지 않을 수 있습니다.    | 이 문제를 해결하려면 스크립트에 대한 오류 로그를 확인하세요.|  
|    Post-ScriptExecutionFailed |    사후 스크립트가 응용 프로그램 상태에 영향을 줄 수 있는 오류를 반환했습니다. |    이 문제를 해결하려면 스크립트에 대한 오류 로그를 확인하고 응용 프로그램 상태를 확인하세요. |
| Pre-ScriptNotFound |    VMSnapshotPluginConfig.json 구성 파일에 지정된 위치에서 사전 스크립트를 찾지 못했습니다. |    응용 프로그램 일관성 백업을 위해 사전 스크립트가 구성 파일에 지정된 경로에 있는지 확인하세요.|
| Post-ScriptNotFound |    VMSnapshotPluginConfig.json 구성 파일에 지정된 위치에서 사후 스크립트를 찾지 못했습니다. |    응용 프로그램 일관성 백업을 위해 사후 스크립트가 구성 파일에 지정된 경로에 있는지 확인하세요.|
| IncorrectPluginhostFile |    VmSnapshotLinux 확장과 함께 제공되는 Pluginhost 파일이 손상되어서 사전 스크립트 및 사후 스크립트를 실행할 수 없으며 백업이 응용 프로그램에 일관되지 않습니다.    | VmSnapshotLinux 확장을 제거하세요. 문제를 해결하기 위해 다음 백업과 함께 자동으로 다시 설치됩니다. |
| IncorrectJSONConfigFile | VMSnapshotPluginConfig.json 파일이 잘못되어서 사전 스크립트 및 사후 스크립트를 실행할 수 없으며 백업이 응용 프로그램에 일관되지 않습니다. | [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)에서 복사본을 다운로드하고 다시 구성하세요. |
| InsufficientPermissionforPre-Script | 스크립트 실행을 위해 루트 사용자는 파일의 소유자여야 하며 파일은 "700" 권한을 가져야 합니다. 즉, 소유자만이 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다. | "루트" 사용자가 스크립트 파일의 "소유자"이고 소유자만이 "읽기", "쓰기" 및 "실행" 권한을 갖는지 확인합니다. |
| InsufficientPermissionforPost-Script | 스크립트 실행을 위해 루트 사용자는 파일의 소유자여야 하며 파일은 "700" 권한을 가져야 합니다. 즉, 소유자만이 "읽기", "쓰기" 및 "실행" 권한을 가져야 합니다. | "루트" 사용자가 스크립트 파일의 "소유자"이고 소유자만이 "읽기", "쓰기" 및 "실행" 권한을 갖는지 확인합니다. |
| Pre-ScriptTimeout | 응용 프로그램 일치 백업 사전 스크립트의 실행이 시간 초과되었습니다. | 스크립트를 확인하고 /etc/azure에 있는 VMSnapshotPluginConfig.json 파일에서 시간 제한을 늘립니다. |
| Post-ScriptTimeout | 응용 프로그램 일치 백업 사후 스크립트의 실행이 시간 초과되었습니다. | 스크립트를 확인하고 /etc/azure에 있는 VMSnapshotPluginConfig.json 파일에서 시간 제한을 늘립니다. |

## <a name="next-steps"></a>다음 단계
[Recovery Services 자격 증명 모음에 VM 백업 구성](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

