---
title: 문제 해결 서비스 연결 윈도우 가상 데스크톱 - Azure
description: Windows 가상 데스크톱 테넌트 환경에서 클라이언트 연결을 설정할 때 문제를 해결하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127451"
---
# <a name="windows-virtual-desktop-service-connections"></a>윈도우 가상 데스크톱 서비스 연결

이 문서를 사용하여 Windows 가상 데스크톱 클라이언트 연결 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

Windows 가상 데스크톱 [기술 커뮤니티에서](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)제품 팀 및 기타 활성 커뮤니티 구성원과 Windows 가상 데스크톱 서비스에 대해 의견을 제시하고 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결하지만 아무 것도 표시되지 않습니다(피드 없음)

사용자는 원격 데스크톱 클라이언트를 시작할 수 있으며 인증할 수 있지만 웹 검색 피드에는 아이콘이 표시되지 않습니다.

이 명령줄을 사용하여 문제를 보고하는 사용자가 응용 프로그램 그룹에 할당되었는지 확인합니다.

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

사용자가 올바른 자격 증명으로 로그인하고 있는지 확인합니다.

웹 클라이언트를 사용하는 경우 캐시된 자격 증명 문제가 없는지 확인합니다.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 엔터프라이즈 다중 세션 가상 컴퓨터가 응답하지 않습니다.

가상 시스템이 응답하지 않고 RDP를 통해 액세스할 수 없는 경우 호스트 상태를 확인하여 진단 기능으로 문제를 해결해야 합니다.

호스트 상태를 확인하려면 다음 cmdlet을 실행합니다.

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

호스트 상태가 `NoHeartBeat`있는 경우 VM이 응답하지 않고 에이전트가 Windows 가상 데스크톱 서비스와 통신할 수 없다는 의미입니다.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

NoHeartBeat 상태를 수정하기 위해 수행할 수 있는 몇 가지 방법이 있습니다.

### <a name="update-fslogix"></a>FSLogix 업데이트

FSLogix가 최신 상태가 아닌 경우, 특히 frxdrvvt.sys의 버전 2.9.7205.27375인 경우 교착 상태가 발생할 수 있습니다. [FSLogix를 최신 버전으로 업데이트해야](https://go.microsoft.com/fwlink/?linkid=2084562)합니다.

### <a name="disable-bgtaskregistrationmaintenancetask"></a>BgTask등록등록 해제유지작업

FSLogix 업데이트가 작동하지 않는 경우 BiSrv 구성 요소가 주간 유지 관리 작업 중에 시스템 리소스를 소모하고 있는 것일 수 있습니다. 다음 두 가지 방법 중 하나를 사용 하 여 BgTaskRegistration유지 관리 작업을 사용 하지 않도록 설정 하 여 유지 관리 작업을 일시적으로 사용 하지 않도록 설정 합니다.

- 시작 메뉴로 이동하여 **작업 스케줄러를**검색합니다. 작업 **스케줄러 라이브러리** > 마이크로 이동**마이크로 소프트** > **윈도우** > 브로커**인프라**. **BgTask등록유지작업이라는**작업을 찾습니다. 찾을 때 마우스 오른쪽 단추로 클릭하고 드롭다운 메뉴에서 **사용 안 함(사용 안 함)을** 선택합니다.
- 관리자로 명령줄 메뉴를 열고 다음 명령을 실행합니다.
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원을](troubleshoot-set-up-overview.md)참조하십시오.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 문제를 해결하려면 [테넌트 및 호스트 풀 만들기를](troubleshoot-set-up-issues.md)참조하십시오.
- Windows 가상 데스크톱에서 가상 시스템(VM)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 시스템 구성을](troubleshoot-vm-configuration.md)참조하십시오.
- Windows 가상 데스크톱에서 PowerShell을 사용할 때 문제를 해결하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조하십시오.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포 문제를 해결 합니다.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
