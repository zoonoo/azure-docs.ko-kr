---
title: Azure AD Connect 클라우드 동기화에 대 한 AADCloudSyncTools PowerShell 모듈
description: 이 문서에서는 Azure AD Connect 클라우드 프로비저닝 에이전트를 설치하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3ce5a7a313ab0f0680558aa60b34e3ebb9b51c9
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255125"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화에 대 한 AADCloudSyncTools PowerShell 모듈

공개 미리 보기 2의 릴리스에서는 AADCloudSyncTools PowerShell 모듈이 도입 되었습니다.  이 모듈에서는 Azure AD Connect 클라우드 동기화 배포를 관리 하는 데 사용할 수 있는 유용한 도구 집합을 제공 합니다.

## <a name="pre-requisites"></a>필수 구성 요소
다음 필수 구성 요소가 필요 합니다.
- 이 모듈은 MSAL 인증을 사용 하므로 MSAL.PS 모듈이 설치 되어 있어야 합니다. Azure AD 또는 Azure AD Preview에 더 이상 의존 하지 않습니다.   확인 하려면 관리 PowerShell 창에서를 실행 `Get-module MSAL.PS` 합니다. 모듈이 올바르게 설치 된 경우 응답을 받게 됩니다.  `Install-AADCloudSyncToolsPrerequisites`를 사용 하 여 최신 버전의 MSAL.PS를 설치할 수 있습니다.
- AzureAD PowerShell 모듈입니다.  일부 cmdlet은 AzureAD PowerShell 모듈을 사용 하 여 작업을 수행 합니다.  확인 하려면 관리자 PowerShell 창에서를 실행 `Get-module AzureAD` 합니다. 응답을 받아야 합니다.  `Install-AADCloudSyncToolsPrerequisites`를 사용 하 여 최신 버전의 AzureAD PowerShell 모듈을 설치할 수 있습니다.
- PowerShell에서 모듈을 설치 하는 것은 TLS 1.2을 사용 하 여 적용할 수 있습니다.  모듈을 설치할 수 있는지 확인 하려면 다음을 설정 합니다.
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>AADCloudSyncTools PowerShell 모듈을 설치 합니다.
AADCloudSyncTools 모듈을 설치 하 고 사용 하려면 다음 단계를 사용 합니다.

1.  관리자 권한으로 Windows PowerShell 열기
2.  `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`을 입력 하 고 enter 키를 누릅니다.
3.  다음을 입력 하거나 복사 하 여 붙여넣습니다. 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  Enter 키를 누릅니다.
4.  모듈이 설치 되었는지 확인 하려면 다음을 입력 하거나 복사 하 여 붙여넣습니다.
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  이제 모듈에 대 한 정보가 표시 됩니다.
6.  다음 실행
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  그러면 PowerShell Get 모듈이 설치 됩니다.  PowerShell 창을 닫습니다.
8.  관리자 권한으로 Windows PowerShell 열기
9.  3 단계를 사용 하 여 모듈을 다시 가져옵니다.
10. `Install-AADCloudSyncToolsPrerequisites`을 실행 하 여 MSAL 및 AzureAD 모듈을 설치 합니다.
11. 모든 사전 요구 사항 설치 모듈을 설치 해야 함 ![](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools Cmdlet
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
AzureAD 모듈을 사용 하 여 Azure AD에 연결 하 고 MSAL.PS 모듈을 사용 하 여 Microsoft Graph에 대 한 토큰을 요청 합니다.


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
는 다음과 같이 모든 문제 해결 데이터를 압축 파일에 내보내고 패키지 합니다.
 1. AADCloudSyncToolsVerboseLogs를 사용 하 여 자세한 추적을 시작 합니다.  이러한 추적 로그는 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 폴더에서 확인할 수 있습니다.
 2. 3 분 동안 추적 로그를 수집 합니다.
   -TracingDurationMins을 사용 하 여 다른 시간을 지정 하거나-SkipVerboseTrace을 사용 하 여 자세한 정보 추적을 건너뛸 수 있습니다.
 3. Stop-AADCloudSyncToolsVerboseLogs로 자세한 추적을 중지 합니다.
 4. 지난 24 시간 동안 이벤트 뷰어 로그를 수집 합니다.
 5. 모든 에이전트 로그, 자세한 정보 로그 및 이벤트 뷰어 로그를 사용자의 문서 폴더에 있는 압축 된 zip 파일로 압축 합니다. 
 </br>-OutputPath를 사용 하 여 다른 출력 폴더를 지정할 수 있습니다. \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Azure AD 테 넌 트 세부 정보 및 내부 변수 상태를 표시 합니다.

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
그래프를 사용 하 여 AD2AAD 서비스 주체를 가져오고 동기화 작업 정보를 반환 합니다.
는 특정 동기화 작업 ID를 매개 변수로 사용 하 여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
그래프를 사용 하 여 AD2AAD 서비스 주체를 가져오고 동기화 작업의 일정을 반환 합니다.
는 특정 동기화 작업 ID를 매개 변수로 사용 하 여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
그래프를 사용 하 여 AD2AAD 서비스 주체를 가져오고 동기화 작업의 스키마를 반환 합니다.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
그래프를 사용 하 여 제공 된 동기화 작업 ID에 대 한 동기화 작업의 스키마를 가져오고 모든 필터 그룹의 범위를 출력 합니다.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
그래프를 사용 하 여 AD2AAD 서비스 주체를 가져오고 동기화 작업의 설정을 반환 합니다.
는 특정 동기화 작업 ID를 매개 변수로 사용 하 여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
그래프를 사용 하 여 AD2AAD 서비스 주체를 가져오고 동기화 작업의 상태를 반환 합니다.
는 특정 동기화 작업 ID를 매개 변수로 사용 하 여 호출할 수도 있습니다.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
그래프를 사용 하 여 AD2AAD 및/또는 SyncFabric에 대 한 서비스 사용자를 가져옵니다.
매개 변수가 없는 경우 AD2AAD는 서비스 주체를 반환 합니다.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
PowerShellGet v 2.2.4.1 이상 및 Azure AD 및 MSAL.PS 모듈이 있는지 확인 하 고 누락 된 경우이를 설치 합니다.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
매개 변수로 지정 된 URI, 메서드 및 본문에 대 한 웹 요청을 호출 합니다.

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Azure AD PowerShell을 사용 하 여 현재 계정 (있는 경우)을 삭제 하 고 Azure AD의 새 동기화 계정으로 동기화 계정 인증을 다시 설정 합니다.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
전체 동기화를 다시 시작 합니다.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
이전 워터 마크에서 동기화를 계속 합니다.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
자세한 추적을 사용 하도록 설정 하 고 AADConnectProvisioningAgent 서비스를 다시 시작 하도록 ' AADConnectProvisioningAgent.exe.config '를 수정 합니다.-SkipServiceRestart를 사용 하 여 서비스를 다시 시작 하지 않지만 구성 변경 내용은 적용 되지 않습니다.  이러한 추적 로그는 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 폴더에서 확인할 수 있습니다.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
' AADConnectProvisioningAgent.exe.config '를 수정 하 여 자세한 추적을 사용 하지 않도록 설정 하 고 AADConnectProvisioningAgent 서비스를 다시 시작 합니다. -SkipServiceRestart을 사용 하 여 서비스를 다시 시작 하는 것을 방지할 수 있지만 구성 변경은 적용 되지 않습니다.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
동기화를 일시 중지 합니다.

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

