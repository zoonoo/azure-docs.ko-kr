---
title: "Azure Cloud Shell 제한 사항 | Microsoft Docs"
description: "Azure Cloud Shell의 제한 사항 개요"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: juluk
ms.openlocfilehash: 7e498582d78d2807070c943dfd838dd9efeb4ed2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell의 제한 사항

Azure Cloud Shell에는 다음과 같이 알려진 제한 사항이 있습니다.

## <a name="general-limitations"></a>일반적인 제한 사항

### <a name="system-state-and-persistence"></a>시스템 상태 및 지속성

Cloud Shell 세션을 제공하는 컴퓨터는 일시적이며 세션이 20분 동안 비활성화된 후 재순환됩니다. Cloud Shell은 Azure 파일 공유를 탑재해야 합니다. 따라서 Cloud Shell에 액세스하도록 구독에서 저장소 리소스를 설정할 수 있어야 합니다. 기타 고려 사항은 다음과 같습니다.

* 탑재된 저장소에서 `clouddrive` 디렉터리 내 수정 사항만 유지됩니다. Bash에서 `$Home` 디렉터리도 유지됩니다.
* Azure 파일 공유는 [할당된 지역](persisting-shell-storage.md#mount-a-new-clouddrive) 내에서만 탑재될 수 있습니다.
  * Bash에서 `ACC_LOCATION`로 설정된 해당 지역을 찾으려면 `env`을 실행합니다.
* Azure 파일은 로컬 중복 저장소 및 지역 중복 저장소 계정만 지원합니다.

### <a name="browser-support"></a>브라우저 지원

Cloud Shell은 Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox 및 Apple Safari의 최신 버전을 지원합니다. Safari는 개인 모드에서 지원되지 않습니다.

### <a name="copy-and-paste"></a>복사 및 붙여넣기

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>지정된 사용자에 대해 셸이 하나만 활성화될 수 있습니다.

사용자는 **Bash** 또는 **PowerShell**을 이용하여 한 번에 한 가지 유형의 셸만을 시작할 수 있습니다. 그러나 PowerShell 또는 Bash의 인스턴스는 동시에 여러 개 실행할 수 있습니다. PowerShell 또는 Bash 간에 교환이 일어나면 Azure Cloud Shell이 다시 시작되어 기존 세션이 종료됩니다.

### <a name="usage-limits"></a>사용 제한

Cloud Shell은 대화형 사용 사례를 위한 것입니다. 따라서 비대화형 세션을 오래 실행하면 경고 없이 종료됩니다.

## <a name="bash-limitations"></a>Bash 제한 사항

### <a name="user-permissions"></a>사용자 권한

권한은 sudo 액세스 권한이 없는 일반 사용자로 설정됩니다. 사용자 `$Home` 디렉터리 외부에서의 설치는 유지되지 않습니다.

### <a name="clouddrive-smb-limited-permissions"></a>Clouddrive SMB 제한된 사용 권한
`git clone`과 같이 `clouddrive` 디렉터리 내 특정 명령에 특정 파일을 읽고 쓰기 위한 적절한 권한이 없습니다. 이 문제가 발생할 경우 SMB 제한이 없는 `$Home` 디렉터리에서 다시 시도하세요.

### <a name="editing-bashrc"></a>.bashrc 편집

.bashrc를 편집할 때는 Cloud Shell에 예기치 않은 오류가 발생할 수 있으니 주의하세요.

### <a name="bashhistory"></a>.bash_history

Cloud Shell 세션 중단 또는 동시 세션으로 인해 bash 명령의 기록이 일관되지 않을 수 있습니다.

## <a name="powershell-limitations"></a>PowerShell 제한 사항

### <a name="slow-startup-time"></a>느린 시작 시간

Azure Cloud Shell의 PowerShell(미리 보기)은 미리 보기 중에 초기화하는 데 최대 60초가 걸릴 수 있습니다.

### <a name="no-home-directory-persistence"></a>$Home 디렉터리 지속성 없음

응용 프로그램(예: git, vim 및 기타)이 `$Home`에 기록한 데이터는 PowerShell 세션 간에 지속되지 않습니다. 해결 방법은 [여기를 참조하세요](troubleshooting.md#powershell-resolutions).

### <a name="default-file-location-when-created-from-azure-drive"></a>Azure 드라이브에서 만들 때 기본 파일 위치:

PowerShell cmdlet을 사용하여 사용자가 Azure 드라이브 아래에 파일을 만들 수 없습니다. 사용자가 vim 또는 nano 등의 다른 도구를 사용하여 새 파일을 만들 때 파일은 기본적으로 C:\Users 폴더에 저장됩니다. 

### <a name="gui-applications-are-not-supported"></a>GUI 응용 프로그램은 지원되지 않습니다.

사용자가 Windows 대화 상자를 만드는 명령을 실행할 경우(예: `Connect-AzureAD` 또는 `Login-AzureRMAccount`) `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` 같은 오류 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Cloud Shell 문제 해결](troubleshooting.md) <br>
[Bash에 대한 빠른 시작](quickstart.md) <br>
[PowerShell에 대한 빠른 시작](quickstart-powershell.md)
